---
layout: post
title: "A Refactoring Story"
header_img: "pipe.jpg"
description: "A recent refactoring."
---
<p>
  Last spring, development started up again on a side business that I'm
  involved in. I'd hardly touched the two apps that run the business in over 3
  years, and they needed some love. Back in May I discussed wrestling with the question of whether to <a
  href="https://www.briefs.fm/speric/4">Refactor or Rewrite</a> these
  apps. I decided to refactor, and I don't regret the decision.
  What follows is one small refactoring that I did.
</p>

<h3>Context</h3>

<p>
  <a href="https://talentsoup.com">TalentSoup</a> is a marketplace.
  It connects professional and amateur actors, actresses, and models
  with producers and photographers. Talent sign up, upload photos, and
  fill out a basic profile. They are then matched with
  open projects in their area that they are a fit for.
</p>

<p>
  Each talent sees a Talent Dashboard when they first log in. The Dashboard has two main
  components. The first is a listing of all open projects across TalentSoup. The second,
  a listing of projects for which they are under consideration to be cast.
</p>

<p>
  The data driving these two components comes from a separate, internal
  project management app we've written. This app exposes a REST-ful API
  that TalentSoup calls to get the data it needs to display the
  Dashboard.
</p>

<h3>Dashboard Behavior: How It Should Work</h3>

<p>
  If it's the user's first time seeing the Dashboard, we want to display a "welcome message" which has
  tips on how to get the most out of their TalentSoup account. There are
  two cases which are a "first-time view" of the Dashboard. The first is a
  new user signing up and being immediately redirected to the Dashboard. The second case is an
  existing user who is viewing the Dashboard for the first time after we
  had made significant UI changes to the old Dashboard. We want them to
  see the "welcome message" too, since the changes were so radical.  On subsequent visits,
  they'll all see the real dashboard.
</p>

<p>
  To keep track of whether a user had viewed the "welcome message", I
  added a boolean column, <code>wc_view</code>, (default
  <code>false</code>) to the users table (<code>wc</code> stands for
  "Webcomp", which is what we call Talent portfolios). When the welcome message has
  been viewed, <code>wc_view</code> becomes <code>true</code>. Looking
  back, this column is unhelpfully named. Something like
  <code>welcome_message_viewed</code> would have been clearer.
</p>

<h3>Initial Code</h3>

<p>
  Before the refactor, the controller action driving the Dashboard code looked liked this (URLs are not real,
obviously):
</p>

```ruby
class TalentController < ApplicationController
  def dashboard
    if current_talent.wc_view
      begin
        @open_projects = HTTParty.get(
          "https://commissary.dev/open-projects",
          body: "",
          output: 'json'
        )
        @availability_requests = HTTParty.get(
          "https://commissary.dev/availability-requests/#{current_talent.id}",
          body: "",
          output: 'json'
        )
      rescue
        @availability_requests = []
        @open_projects = []
      end
    else
      current_talent.wc_view = true
      current_talent.save
      render template: "talent/welcome"
    end
  end
end
```

<h3>Code Review</h3>

<p>
  I cringe looking at this, but it's how I wrote code 4-5 years ago. You have to start somewhere.
</p>

<p>
  If I were reviewing this code in a pull request today, I would make the following comments:
</p>

<ul>
  <li>
    <p>
      The <code>dashboard</code> action is not a REST-ful route, according to Rails idioms. It should extracted to it's own controller .
    </p>
  </li>
  <li>
    <p>
      The controller is updating <code>current_talent</code> (an <code>ActiveRecord</code> object) directly. This means it has to know which columns mean what. The knowledge about how the application should determine whether or not a Talent has viewed the "welcome message" should be pushed down to the Talent class itself (or some other class?), but shouldn't live in the controller.
    </p>
  </li>
  <li>
    <p>
      The knowledge about how to mark a talent as having viewing the "welcome message" should also live in the Talent class
itself (or another class), but not in the controller.
    </p>
  </li>
  <li>
    <p>
      The two <code>HTTParty</code> calls to the external Commissary service means the controller
      knows everything about where the endpoints are, and what to do in case of an error. This isn't the worst approach, but could improved by moving these calls to a separate class. Doing so would also give us the benefit of being able to use those classes in other places.
    </p>
  </li>
</ul>

<p>
  Generally speaking, this code violates a number of rules that I (now)
  try to abide by:
</p>

<ul>
  <li><p>Violates Rails idioms.</p></li>
  <li><p>Spreads too much knowledge about implementation details.</p></li>
  <li><p>Misses the opportunity to extract interesting functionality
(calls to the external Commissary service) into small classes that can
be reused.</p></li>
  <li><p>Is ugly to look at.</p></li>
</ul>

<h3>The Refactor</h3>

<p>
  The first thing I did was move all the "has Talent viewed their
  welcome message before?" logic out of the controller. I can get around
  how badly the <code>wc_view</code> column is named by wrapping it in an
  intention-revealing method.
</p>

```ruby
# app/models/talent.rb
class Talent < ActiveRecord::Base
  def first_webcomp_view?
    !wc_view
  end

  def first_webcomp_viewed
    update_column(:wc_view, true)
  end
end

# test/models/talent_test.rb
class TalentTest < ActiveSupport::TestCase
  def setup
    @talent = talents(:luke_skywalker)
  end

  test "#first_webcomp_view? is true when wc_view is true" do
    @talent.update_column(:wc_view, true)
    assert @talent.wc_view
    refute @talent.first_webcomp_view?
  end

  test "#first_webcomp_view? is true when wc_view is false" do
    @talent.update_column(:wc_view, false)
    refute @talent.wc_view
    assert @talent.first_webcomp_view?
  end

  test "#first_webcomp_viewed sets wc_view to true" do
    @talent.update_column(:wc_view, false)
    @talent.first_webcomp_viewed!

    assert @talent.wc_view
    refute @talent.first_webcomp_view?
  end
end
```

<p>
  Now I am able to clean the controller up, and rework the logic with
  my new methods. The controller will be a bit easier to read, and the
  implementation details have been effectively hid:
</p>

```ruby
class TalentController < ApplicationController
  def dashboard
    if current_talent.first_webcomp_view?
      current_talent.first_webcomp_viewed
      render template: "talent/welcome"
    else
      begin
        @open_projects = HTTParty.get(
          "https://commissary.dev/open-projects",
          body: "",
          output: 'json'
        )
        @availability_requests = HTTParty.get(
          "https://commissary.dev/availability-requests/#{current_talent.id}",
          body: "",
          output: 'json'
        )
      rescue
        @availability_requests = []
        @open_projects = []
      end
    end
  end
end
```

<p>
  Now, the details about what columns Talent has, and what they mean,
  are pushed down into the Talent class itself. If those columns change,
  or if what it means to have made the initial view of your Dashboard changes,
  we can change the underlying implementation and are less likely to need
  to touch the controller.
</p>

<p>
  Next, I re-worked the two calls to the external Commissary service.
  I'm going to detail the "open projects" path, since it's shorter.
</p>

```ruby
# app/services/commissary.rb
module Commissary
  ENDPOINT = "https://commissary.dev/"

  def self.endpoint_for(action)
    "#{ENDPOINT}#{action}"
  end
end

# app/services/commissary/open_projects.rb
module Commissary
  class OpenProjects
    attr_accessor :http_client

    def self.fetch
      self.new.fetch
    end

    def initialize
      @http_client = HTTParty
    end

    def fetch
      http_client.get(
        Commissary.endpoint_for("open-projects"),
        body: '',
        output: 'json'
      ).map { |project| OpenStruct.new(project) }
    rescue
      []
    end
  end
end
```

<p>
  This is a simple class, but there's more going on that might initially appear. The
  first is that there's an <code>attr_accessor</code> for an <code>http_client</code>. You
  can't initialize the class with a custom <code>http_client</code> (the "default" is
  <code>HTTParty</code>, but you can set one after initialization. I can't take credit
  for this pattern; I learned it (and many other things!) from
  <a href="https://twitter.com/brandonhilkert">Brandon Hilkert</a>.
</p>

<p>
  I also convert the response into an array of <code>OpenStruct</code>
  objects. This is purely for the convenience of using dot notation in
  my views.
</p>

<p>
  One of the advantages of this approach is that it makes the
  <code>Commissary::OpenProjects</code> class easier to test. There are of
  course many ways to stub/mock out an HTTP request, but using this
  method, we don't need any external libraries! We can do it all in plain
  Ruby:
</p>

```ruby
require 'test_helper'

class Commissary::OpenProjectsTest < ActiveSupport::TestCase
  test "#fetch with a good response" do
    client = Commissary::OpenProjects.new
    client.http_client = FakeHTTParty
    open_projects = client.fetch

    assert_equal 1, open_projects.count
    project = open_projects.first
    assert_equal "987654321", project.id
    assert_equal "An amazing project", project.name
    # other assertions omitted for brevity
  end

  test "#fetch when an exception occurs" do
    client = Commissary::OpenProjects.new
    client.http_client = FakeHTTPartyWithException
    open_projects = client.fetch

    assert_equal [], open_projects
  end

  class FakeHTTParty
    def self.get(*args)
      [
        {
          id: "987654321",
          name: "An amazing project",
        }
      ]
    end
  end

  class FakeHTTPartyWithException
    def self.get(*args)
      raise "Oops!"
    end
  end
end
```

<p>
  We've "mocked" an HTTP request to an external service without using
  a gem like VCR or Webmock. The disadvantage to this
  approach is that, if the response from the Commissary service changes, I
  can't just blow away my cassettes if I were using VCR, regenerate them, and fix my
  tests. I'll have to fix my fake client(s) also. However, weighed against the
  overhead of adding another gem to my app, it's a tradeoff I am willing
  to make. Additionally, in writing a simple mock HTTP client like this,
  you become familiar with the shape of the response coming back from the
  external service in a way you don't when using VCR, for example.
  That's been my experience, having used VCR extensively.
</p>

<p>
  The other interesting thing in <code>Commissary::OpenProjects</code> that's
  is the <code>fetch</code> class method, which is just:
</p>

```ruby
def self.fetch
  self.new.fetch
end
```

<p>
  This is a pattern I learned from <a href="https://twitter.com/moklett">Michael Klett</a>. It
  can be more work than it's worth sometimes, but in this case, since the constructor for
  <code>Commissary::OpenProjects</code> takes no args, having this class method saves us from doing
  <code>Commissary::OpenProjects.new.fetch</code>, which I find aesthetically objectionable!
</p>

<p>Our refactored controller action now looks like:</p>

```ruby
class TalentController < ApplicationController
  def dashboard
    if current_talent.first_webcomp_view?
      current_talent.first_webcomp_viewed
      render template: "talent/welcome"
    else
      @open_projects = Commissary::OpenProjects.fetch
      # refactoring not shown for this
      @availability_requests = Commissary::AvailabilityRequests.fetch(
        talent_id: current_talent.id
      )
    end
  end
end
```

<p>
  The final step was to move this action (part of a larger controller
  with many other non-RESTful routes) into it's own controller:
</p>

```ruby
# config/routes.rb
resource :talent_dashboard, only: [:show]

# app/controllers/talent_dashboards_controller.rb
class TalentDashboardsController < ApplicationController
  def show
    if current_talent.first_webcomp_view?
      current_talent.first_webcomp_viewed
      render template: "talent/welcome"
    else
      @open_projects = Commissary::OpenProjects.fetch
      @availability_requests = Commissary::AvailabilityRequests.fetch(talent_id: current_talent.id)
    end
  end
end
```

<p>
  The motivation here is to adhere to Rails idioms. Additionally,
  smaller controllers are easier to read, and therefore easier to get context
  about. I don't mind an app with a lot of small controllers that do one
  or two things well.
</p>

<h3>Conclusion</h3>

<p>
  This code could be improved even further by extracting the methods on
  the <code>Talent</code> class into a plain Ruby object (a "service"
  object, in the parlance of our times). I decided against this because
  the <code>Talent</code> class, although it represents the main user of
  the app, is not a <a href="https://en.wikipedia.org/wiki/God_object">god object</a>.
  If, in the future, it feels like <code>Talent</code> is growing out of
  control, I could see the value in such an extraction.
</p>

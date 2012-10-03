---
layout: post
title: Dynamic Images (Or A Lightweight Gravatar)
---
<p>We recently rolled out <a href="https://talentsoup.com/producers">Creative Commissary</a>, our project management tool for image producers.  It's a completely separate app from <a href="https://talentsoup.com">TalentSoup</a> in every way.  They do not share databases, and communicate via a JSON-based RESTful API.  Clients build and manage projects in Commissary, and the talent details (name, contact info, etc.) are stored in TalentSoup.</p>

<p>In TalentSoup, talent have the option of selecting any of the photos as their main photo.  This is the photo whose thumbnail will be shown in search results and project galleries.  This main photo is the talent's first impression on the client sees, and therefore the talent's most important photo.</p>

<p>Originally, when doing a search return (Commissary sending search parameters to TalentSoup, TalentSoup returning a search result to Commissary), I was including the static URL for the main photo at the time of the search as the <code>main_photo</code> value.  This value would be stored in Commissary and would be referenced when rendering a gallery page for the client to look at.  One thing I didn't take into account, however, was that talent might change their <code>main_photo</code> anytime after the gallery for that particular project was built.  When that happened, the image being displayed in the gallery wouldn't change, because the URL was being stored statically on the Commissary side.</p>

<p>This is a problem because sometimes talent change their <code>main_photo</code> based on what type of project they're being considered for (we let them know when they're added to a gallery, and what type of job it is).  For example, right now we're managing a job for a client who is going to be shooting people running.  As a result, some talent changed their <code>main_photo</code> to show them running or doing some sort of physical exercise.  This should be reflected in the galleries, obviously!</p>

<p>Basically what I needed was a service like <a href="http://gravatar.com">Gravatar</a>.  One URL I could pass around between TalentSoup and Commissary that would always render the given talent's <code>main_photo</code>.  I came up with a simple solution.  I am not sure if this is the best way to accomplish this, but it works.

<p>
{% highlight ruby %}
#app/controllers/users_controller.rb
class TalentController < ApplicationController
  def main_photo
    @user = User.find(params[:id], :include => :main_photo)
    redirect_to @user.main_photo.url(:medium) # medium thumbnail url
  end
end
{% endhighlight %}
</p>

<p>Now I can do this on the Commissary side:</p>

<p>
{% highlight html %}
<img src="https://talentsoup.com/talent/<%= @talent.id %>/main_photo %>"/>
{% endhighlight %}
</p>

<p>And I'll always get the <code>main_photo</code> for a given talent even when they change it.  We can make this more efficient by caching on the TalentSoup side, and invalidating when the talent changes their main photo.</p>
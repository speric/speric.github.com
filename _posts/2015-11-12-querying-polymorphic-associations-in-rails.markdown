---
layout: post
title: Querying Polymorphic Assocations in Rails
header_img: "poly.jpg"
description: "Rails makes it simple to query polymorphic associations."
---
<p>On a recent project, I had the opportunity to use <a
href="http://guides.rubyonrails.org/association_basics.html#polymorphic-associations">polymorphic
associations</a> for the first time in a non-trivial context. Along the way I discovered a neat way to query those associations with Active Record.</p>

<p>Let's take a trivial example of simple associations:</p>

<p>
{% highlight ruby %}
class User < ActiveRecord::Base
  has_many :posts
end

class Post < ActiveRecord::Base
  belongs_to :user
end
{% endhighlight %}
</p>

<p>Given that association, you couldn't do:</p>
<p>
{% highlight ruby %}
user = User.first
posts = Post.where(user: user)

#<ActiveRecord::StatementInvalid: Mysql2::Error: Unknown column 'posts.author' in 'where clause'
{% endhighlight %}
</p>

<p>Not that you'd want to do it that way. Normally you'd just do
<code>posts = author.posts</code>, but the point here is that you can't pass in
an actual AR object to a query in that manner, even if the relation
exists.</p>

<p>But, you can do just that with polymorphic associations!</p>

<p>Let's modify our example slightly:</p>

<p>
{% highlight ruby %}
class User < ActiveRecord::Base
  has_many :posts, as: :authorable
end

class Administrator < ActiveRecord::Base
  has_many :posts, as: :authorable
end

class Post < ActiveRecord::Base
  belongs_to :authorable, polymorphic: true
end
{% endhighlight %}
</p>

<p>Now, with this set-up you can do:</p>

<p>
{% highlight ruby %}
user = User.first
posts = Post.where(authorable: user)
{% endhighlight %}
</p>

<p>Again, you wouldn't normally want to get a user's posts this way. But I
thought it was neat that you could simply pass the <code>authorable</code> object
into the <code>where</code> clause, and Active Record would "do the right thing"
with it, as opposed to basic associations, where you cannot pass in an
object in that same manner.</p>

<h3>See Also</h3>
<p><a
href="http://www.gotealeaf.com/blog/understanding-polymorphic-associations-in-rails">Understanding Polymorphic Associations in Rails</a></p>

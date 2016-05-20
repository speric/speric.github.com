---
layout: post
title: "Things I Learned This Week: May 16-20, 2016"
header_img: "nyc-skyline.jpg"
description: "What I learned this week."
---
<p><b><a href="https://kapeli.com/dash">Dash</a></b><br/>
This app is already indispensable to my workflow. Just check it out.</p>

<p><b><a href="https://en.wikipedia.org/wiki/Monotonic_function">Monotonic functions</a></b></br>
A function is called monotonic if and only if it is either entirely increasing or decreasing.</p>

<p><b><a
href="https://facebook.github.io/react/docs/reusable-components.html">Stateless Components in ReactJS</a></b><br/>
Stateless components are pure functions of their props. They (obviously)
don't have internal state, and do not call any component lifecycle
methods.</p>

<p><b><a
href="https://hexdocs.pm/ecto/Ecto.Query.html#order_by/3">Ecto.Query.order_by/3</a></b><br/>
Learned various ways this function can be used. The Ecto version of
Rails' <code>order("created_at desc")</code> would be
<code>order_by(desc: :created_at)</code>.
</p>
<p><b><a href="https://hexdocs.pm/ecto/Ecto.Changeset.html#foreign_key_constraint/3">Ecto.Changeset.foreign_key_constraint/3</a></b><br/>Ecto provides the ability to capture foreign key constraint violations as errors on a changeset. The big difference to me between this function and <code>assoc_constraint</code> is that you can pass any key as the first argument, and Ecto will put the error on that key if the constraint fails. This is in contrast to <code>assoc_constraint</code>, which inflects on the key you pass in to determine what association constraint it's checking:</p>
<p>
{% highlight elixir %}
# Assume a post belongs to an author, lets ensure the author exists. If this fails,
# the error will be put on the "author" key in the changeset
assoc_constraint(:author)

# We can also capture some other foreign key violation,
# and put it on another key within the changeset
foreign_key_constraint(:some_custom_changeset_key, name: :some_database_fkey, message: "bad data!")
{% endhighlight %}
</p>

<p><b><a href="http://www.singlecutbeer.com/">Singlecut Beersmiths</a></b><br/>A brewery from my native Queens, NY. I had the <a href="https://untappd.com/user/speric/checkin/312429363">Billy Half-Stack IPA</a>, and I'd drink it again!</p>

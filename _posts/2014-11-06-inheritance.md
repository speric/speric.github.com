---
layout: post
title: Inheritance
header_img: "inheritance.png"
---
<p>I'm coming to the opinion that, in Ruby, inheritance is never the first tool you should reach for when designing classes. This has been a difficult mental shift for me to make, coming to Ruby as I did from the Java world. Reading <i><a href="http://www.poodr.com/">Practical Object-Oriented Design in Ruby</a></i> earlier this year first put the idea in my head. But recent experience doing post-mortems on production code which made use of inheritance has made the abstract idea concrete.</p>

<p>The major reason for this shift in thinking is that, in my
experience, code which makes
use of inheritance is harder to reason about. This is especially the
case when one is trying to track down a bug during an outage or some
other "support incident". Your experience may be different. But there's
something to be said for having all the methods that a class makes use of all in one place.</p>

<p>It can be argued that composition also introduces the same type of
problem, but I think it's mitigated for me by the fact that classes
which make use of composition tend to have shallower hierarchies. Most
of the time you're just jumping to one file to look for a method
definition, whereas with inheritance you are potentially jumping into a
complex class hierarchy.</p>

<p>I'm beginning to think that dependency injection is a great way to
make potentially complex model interactions very simple. When you focus
on message-based design, you then discover news ways to leverage Ruby's
duck-typing capabilities in the service of writing small classes that do
one thing and are simple to reason about. These types of classes are
easy to change, and in my experience, easier for the person who didn't
write them to someone to come in and understand. And for teams working
on large applications, that's a big win.</p>

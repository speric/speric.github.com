---
layout: post
title: 10 Things I Wish I Knew As A Junior Developer
header_img: "things.jpg"
description: "10 things I wish I knew when I was a junior (and solo)
developer trying to level up."
---
<p>Here are some things I wish I knew when I was a junior (and solo) developer trying to "level up":</p>

<ul>
  <li><p>The best way to level up is by pairing with someone more experienced. Books & screencasts are great and should be used, but pairing will help you make the leap more quickly. My "level up hierarchy" is <code>pairing > code review > books/screencasts</code>.</p></li>
  <li><p>Everything is a tradeoff. You need to see the advantages and
disadvantages of different approaches to solving a problem, and then
decide with your team which advantages are most important, and which
disadvantages you can live with. There will rarely be a perfect solution
to a problem, and the framework for deciding which tradeoffs to make
varies from team to team.</p></li>
  <li><p>Design will become an important question. When you see two
classes with similar functionality, should you extract a common base
class? Should you move some methods to a shared module? Why or why not?
Your answers to these questions will change over time, but it's
important to develop reasoned opinions. Remember, everything is a
tradeoff.</p></li>
  <li><p>One of the biggest challenges of working on a large Rails app is code
organization. Questions like "where do non-ActiveRecord classes go?" arise and are trickier to answer than they first appear.</p></li>
  <li><p>There's nothing magical about the code you write for a large
app. There's no secret subset of Ruby (or Rails) that you need to be
initiated into before you can code "at scale". You will find yourself
considering different things when working on a large vs small app
(performance, background jobs, caching, etc.) but those are design issues. Ruby is still
Ruby.</p></li>
  <li><p>Learn to love testing. In most cases, you should be nervous about shipping
code that is not tested.</p></li>
  <li><p>Learn about testing philosophy. More tests are not necessarily
better. It's important to know what to test and why. See
<a href="https://github.com/speric/til/blob/master/ruby/questions-to-ask-when-writing-tests.md">Questions to ask when writing tests</a>.</p></li>
  <li><p>Don't be afraid to ask questions of more senior developers if
something doesn't make sense. You'll either learn something new, or your
questioning will result in a new approach that the other developer
hadn't considered. Everyone wins!</p></li>
  <li><p>If an idea doesn't make sense at the time, come back to it in 6
months. Sometimes we're not ready to absorb a new language or
paradigm, but that doesn't mean we won't be ready at some future point
after growing in another area. I had this experience with functional
programming.</p></li>
  <li><p>Related to the last point, and somewhat contradictory, you need to aggressively find ways
to use new things. The only way to truly learn a new language or
technology is to build something with it. This will often require tinkering with
things on your own time. Some things are hard to learn, and require
persistence.</p></li>
</ul>

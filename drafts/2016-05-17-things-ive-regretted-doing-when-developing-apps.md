---
layout: post
title: Things I've Regretted Doing When Developing Web Apps
header_img: "junkyard.jpg"
description: "Some things I've regretted."
---
<p>Regrets? I've got a few.</p>

<h4>Using subdomains for a multi-tenant app ala Basecamp</h4>
<p>The idea of giving every client their own subdomain in my app was too
much to resist. Armed with a <a
href="https://gist.github.com/dhh/45076">gist from DHH</a>, I
implemented account-scoping by subdomain. While cool from a marketing
standpoint, it's been a bit of a nightmare to maintain. And users always
forget their subdomain, so you wind up building a "launchpad" anyway. I'm currently in the process of removing this feature from the app in question.</p>


<h4>Not writing tests</h4>

<p>Easily the biggest mistake I've made in the past, and one I am still
paying for with a few apps. Not writing tests because you want to move faster is one of those
"robbing Peter to pay Paul" scenarios. Whatever you gain my being able
to ship a feature faster, you lose in the future when you have to figure
out why something suddenly stopped working when you made that one small
change that shouldn't have affected any other part of the app.</p>

<p>And good luck running through a framework upgrade without tests. How
can you have confidence that everything still works when there are no
tests?</p>

<h4>Falling behind with Rails & Ruby upgrades</h4>
<p>Maybe it was because my apps weren't well tested that I punted on
getting on the latest Ruby + Rails versions for so long, but I will
never get stuck in that position again. When a new version is released,
do what you need to do to get on it.</p>

Trying to manage a server on my own
Deeply nesting routes (basically, not following http://weblog.jamisbuck.org/2007/2/5/nesting-resources)

Putting the admin app inside the main app, instead of as a separate app


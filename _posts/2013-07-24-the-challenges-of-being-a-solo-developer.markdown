---
layout: post
title: The Challenges Of Being A Solo Developer
header_img: "skyline_from_harlem.jpg"
---
<p>Since October 2009 I have been the only developer at <a href="https://talentsoup.com">TalentSoup</a>.  In the past, I've been part of large co-located teams, and smaller distributed teams (across continents). TalentSoup is my first experience building an application this large, by myself. I want to briefly talk about my experience as a solo developer, and offer some helpful tips to others in the same situation.</p>

<h3>The Good</h3>
<p>The best part of being a solo developer is that you can do whatever you want. It turns out that this is a double edged sword, as I'll get to below. But there are times when it's nice to use whatever technologies or frameworks that you are most productive with. No bureaucracy to deal with.</p>

<p>The other great thing is that you learn a lot.  When working on an application with other developers, the tendency is to divide up the work based on the expertise of the team, especially when time is of the essence. It takes a strong lead developer to say, "Jane doesn't have a lot of experience with billing systems, so we're going to let her spend the next few weeks working on some news features for ours, so she can learn." As a solo developer, you get those opportunities by default.  For me, this also involved learning basic sysadmin stuff.  Here are some things I've done at TalentSoup that I'd not done before:</p>
<ul>
  <li><p>Set up and secure a VPS</p></li>
  <li><p>Install and set up Apache, nginx, and MySQL</p></li>
  <li><p>Set up and integrate Graphite + statsd</p></li>
  <li><p>Integrate with a recurring billing solution (Chargify and Stripe in our case)</p></li>
  <li><p>App monitoring with Scout</p></li>
  <li><p>Setting up A/B tests</p></li>
</ul>

<p>All this, in addition to things like upgrading from Rails 2 to 3, and all the little things you learn as you refactor, gain a deeper understanding of your language and framework, etc.  Not to say I wouldn't have learned as much if I were working with someone else, just that I <b>had</b> to learn. Having worked in the past at places where there were separate teams handing server and database administration, I enjoyed the chance to dig in to new things.</p>

<h3>The Bad</h3>
<p>As indicated above, having the freedom to do whatever you want has huge drawbacks. The longer I am a solo developer, the more I think it's a net negative.  Unless you have an exhaustive knowledge of the technologies available for a given solution, and the experience to know the benefits/limitations of each, the fact is that you will have blindspots in your thinking. I'm not saying that someone with limited experience can't find a good solution to a problem. What I am saying is that it's good to have someone who can suggest a couple of different courses of action, because there might be a solution that fits your problem better than the one you're about to implement.</p>

<p>This principle also affects the learning process.  There have been times when I have wanted to try out a new database for example, but knowing there'd be a learning curve, I avoided it and stuck with what I knew. There's a good business reason for doing this: when stuff breaks, it's better to be working with something you know how to fix. But it would have been nice to be working with someone who had experience with that new database and provided a safety net in terms of knowledge and experience. Even if no one on a team has experience with a new technology, stepping into the unknown with a group is better than stepping in alone.</p>

<p>Another factor is that we all have comfort zones.  I have test frameworks and gems I like to use, and a development process that feels comfortable to me.  But if I want to keep my skills relevant, thinking about opportunities beyond my current position, it would be in my best interest to branch out and learn other things. As a solo dev, unless you are really disciplined, it's hard to step out of that comfort zone.</p>

<p>Also, it can be emotionally difficult to bear the weight of knowing every feature, bug fix and upgrade depends on you. This can lead to burnout.</p>

<p>Finally, when you are a solo developer, there's no one to review your actual code. No one to call you out on a bad implementation, or educate you on the idiomatic way to do a certain thing. No one to raise a flag when untested code goes into production.  I know I've gotten into some bad habits that have taken much effort to reverse. When you are a solo developer you don't get the positive peer pressure that's needed to grow as a craftsman.</p>

<h3>A Way Forward</h3>
<p>The challenges of being a solo developer aren't insurmountable. If you feel that you're not able to get out of your comfort zone, you have to, in the words of my friend <a href="http://twitter.com/snakes">Matt</a>, "Aggressively find ways to use things."  It takes being disciplined and intentional to gain experience with new things when you are on your own. There's no other way. No secret sauce.</p>

<p>I've had the privilege of getting to know some really good Ruby developers.  I'll send an email every once in a while asking how they'd best solve some problem, or if they could recommend a book to help me level up. There are also resources like <a href="http://www.pairprogramwith.me/">pairprogramwith.me</a> if you're looking to pair with another developer.  I got to spend a day pairing with a startup recently, and it was an eye-opening experience, one that I am eager to participate in again. Talking out loud with another developer, while looking at the same code in the same room, exposed so many areas of my craft that I need to improve. It was worth many books and screencasts.</p>

<p>If you are feeling overwhelmed by your workload, might I suggest something counterintuitive: walk away. Take a day or two and don't open the editor, or look at a bug list. Just take some time for yourself. Or, if you are going to work, work on a pet project, something fun. At TalentSoup, a couple of small features that have turned out to be popular were the result of me blowing off a laundry list of things I <b>had</b> to do, to work on something fun, just for my sanity.</p>

<p>These certainly aren't an exhaustive list of solutions, but if you're a solo dev and know you will be for the forseeable future, it's a starting point.</p>

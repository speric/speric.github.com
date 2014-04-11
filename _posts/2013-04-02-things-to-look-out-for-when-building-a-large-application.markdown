---
layout: post
title: Things To Look Out For When Building An App
header_img: "pipe.jpg"
---
<p>A while back I found <a href="http://simon.incutio.com/notes/2006/summit/schachter.txt">a great set of notes</a> that were taken at a presentation by <a href="https://delicious.com/">del.icio.us</a> creator <a href="https://twitter.com/joshu">Joshua Schachter</a> at the 2006 Future Of Web Apps summit.  They were really helpful to me in my growth as a developer.  Unfortunately the notes are no longer available at the original URL, so I've taken the liberty to reproduce them here.</p>

<p>Again, these are not my own notes (credit goes to <a href="https://twitter.com/simonw">Simon Willison</a>) but I want to preserve them for posterity's sake. If you know of an "official" location for these notes please let me know.</p>

<ul>
<li><p>Browser quirks. CSS/JavaScript/rendering. IE caching.</p></li>
<li><p>Scaling: avoid early optimization. SQL doesn't map well to these problems - think about how to split up data over multiple machines. Understand indexing strategies, profile every SQL statement. Nagios or similar for monitoring.</p></li>
<li><p>Tags don't map well to SQL. Sometimes you can prune based on usage - only index the first few pages for example. This keeps indexes small and fast.</p></li>
<li><p>Some latency in the system is OK - work out where you have leeway, e.g. RSS feeds can fall a few minutes behind without anyone minding.</p></li>
<li><p>People are always going to abuse your system (scraping, greasemonkey, etc.)</p></li>
<li><p>"Idiots are a lot smarter than you" - wait to see what breaks before you fix it.</p></li>
<li><p>Learn Apache - tuning can make things a lot faster. Understand headers, mod_rewrite (a dark art). Put a proxy in front of Apache e.g. Perlbal from LiveJournal - one guy on a modem can suck up lots of resources otherwise.</p></li>
<li><p>Images off a different server, RSS from a different server.</p></li>
<li><p>"Save site for offline use" feature in IE is particularly nasty.</p></li>
<li><p>The easier the API to get in and out of, the more people will use it. There's a long tail of smart developers. Stuff like SOAP discourages adoption. No API key = people can play with it faster; drives attention.</p></li>
<li><p>Don't expose your unique id's to the outside world (php?id=1 etc.) People can scrape through everything very easily. This is why del.icio.us uses MD5 hashes of links instead.</p></li>
<li><p>Giving everything a unique ID in the database is a scaling problem in its own right.</p></li>
<li><p>Features: the features you put in are as important as the ones you leave out. There's no "send a note" in del.icio.us feature because e-mail already exists.</p></li>
<li><p>I don't add featues that are available elsewhere e.g. messaging</p></li>
<li><p>When people ask for features, get to the bottom of why they are asking for that exact thing. Solve the problem, rather than doing exactly what your asked for.</p></li>
<li><p>With tags, people ask for "A and B and NOT C or D" - but less than 1% of queries even use more than a single tag.</p></li>
<li><p>RSS important in del.icio.us, because it's a native way for people to access lists (of links). Put RSS everywhere you can. del.icio.us does way more RSS traffic than HTML or API stuff - partly because of poorly written readers.</p></li>
<li><p>Understand the headers - especially if-not-modified.</p></li>
<li><p>Make sure the URLs follow the path of the site. DON'T include session data, drop ugly details that are to do with the system, not the user (.php, .aspx, ?, &, etc.)</p></li>
<li><p>URLs are prime real estate - respect them</p></li>
<li><p>When you chose what to build, solve a problem you have yourself so you can be sure to understand it. Passion counts. It's cheap and easy to build stuff, so other people will be building it too.</p></li>
<li><p>A niche product with a limited audience is still good business (with how advertising and PayPal work)</p></li>
<li><p>Every day that you don't have something properly out in the world (not on an invite only beta) you're losing a chance to gain users. Get it out there ASAP.</p></li>
<li><p>Aggregation is often a focus of attention (latest, most active, etc.)</p></li>
<li><p>As the population gets larger, the bias drifts; del.icio.us/popular becomes less interesting to the original community members. Work out ways to let the system fragment in to different areas of attention.</p></li>
<li><p>"Spam is attention theft" - that's one of the reasons del.icio.us doesn't have a top 10 links of all time - it's an attractive nuisance.</p></li>
<li><p>When you've figured out someone is spamming, don't let them know – let them keep posting and just silently junk their stuff.</p></li>
<li><p>Make users do the minimum amount of work. But make them do something.</p></li>
<li><p>Understand the user's motivation: "You have to understand the selfish user" – user #1 has to find the system useful or you won't get user #2. Systems that only become useful when lots of people are using them usually fail, because there's no incentive for people to contribute themselves. The real trick is to make the user base you have want to invite more people in to the system.</p></li>
<li><p>Manage your effort - be careful where you spend your efforts.</p></li>
<li><p>Don't waste time building features nobody uses.</p></li>
<li><p>Measurement: watch your system intensely: "Intuition is ideas backed by numbers"</p></li>
<li><p>Measure behavior rather than claims. del.icio.us doesn't have stars because why would you bookmark something that was no good? This way people bookmark things that they really care about rather than trying to tell the system things.</p></li>
<li><p>User Acceptance Testing (UAT) is important. Make the system suitable for the people actually using the system. Everybody on the team should see this in action. Labs are great but expensive. If you don't have a lab do ghetto testing in Starbucks. We did one day then two days user testing in a user testing lab.</p></li>
<li><p>Goals skew the results. People don't read, they cram crap in to boxes. Let people wander don't give them tasks.</p></li>
<li><p>You have to speak the user's language. "Bookmarks" are what you call them if you use Netscape of Firefox - most users these days know the term "favourite" instead. Half of his population (? users) didn't know what a bookmark was.</p></li>
<li><p>Don't make users register before they can get in to your site. Maybe even give them an anonymous account to play with. A lot of users want to know what they'll get if they register - especially from fear of giving out email address, spyware etc. You can't tell them; they're not going to read it. You have to show them.</p></li>
<li><p>Use Verbs - doing words - to prompt actions.</p></li>
<li><p>If users do have to register, send them straight back to where they were when they're done. Don't dump them on the homepage.</p></li>
<li><p>"Design Grammar" - if you're presenting a system that's different from how other things work (del.icio.us had novel tags, save your bookmarks to the web) you should still try to reflect the design patterns of the web.</p></li>
<li><p>Morals: You have to develop a sense of morals when you build your system. It's the user's data; it's not yours. Make sure they can remove themselves and their account if they want to.</p></li>
<li><p>Infection: Understand infection vectors for promoting your system. "Enable evangelism". RSS lets you get at users who don't use your system directly. Also think about iCal, M3U - anything that a desktop app can consume over HTTP. Do an inventory to get into every desktop app. possible.</p></li>
</ul>

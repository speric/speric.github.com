---
layout: post
title: Setting up statsd + graphite on Ubuntu 12.04 LTS
header_img: "liverpool.jpg"
---
<p>There are many great tutorials about setting up <a href="https://github.com/etsy/statsd">statsd</a> and <a href="http://graphite.wikidot.com/">graphite</a>, specifically on Ubuntu 12.04.  The best walk through I found was <a href="https://github.com/janoside/ubuntu-statsd-graphite-setup">https://github.com/janoside/ubuntu-statsd-graphite-setup</a>.  I'd like to add some things that were left out of that tutorial that I had to figure out.</p>

<h3>Database Creation</h3>
<p>The above Gist leaves out the initial database creation.  Be sure to thoroughly read <a href="http://graphite.wikidot.com/installation">http://graphite.wikidot.com/installation</a> so you know at which step to create the db. I did this step after configuring Apache but before restarting the server.</p>

<h3>WSGISocketPrefix</h3>
<p>If you see an error message similar to the following:</p>

<p><pre>(2)No such file or directory: mod_wsgi (pid=19506): Unable to connect to WSGI daemon process 'graphite' on '/etc/apache2/run/wsgi.19365.1.1.sock' after multiple attempts.</pre></p>

<p>That's a clue to edit <pre>/etc/apache2/sites-available/default</pre> and make sure that the configuration for <code>WSGISocketPrefix</code> is set as follows:</p>

<p><pre>WSGISocketPrefix /var/run/apache2/wsgi</pre></p>

<p>Thanks to <a href="http://marcelo-olivas.blogspot.com/2012/06/installing-graphite-on-ubuntu-1204.html">Marcelo Olivas</a> for that tip.</a>

<h3>Target WSGI script '/opt/graphite/conf/graphite.wsgi' cannot be loaded as Python module.</h3>
<p>If you see this error, make sure that apache owns the directory</p>
<p><pre>/opt/graphite/storage/logs/webapp</pre></p>

<p>This <a href="https://answers.launchpad.net/graphite/+question/178110">thread</a> pointed me in the right direction.</p>

<p>Good luck.</p>

<h3>See Also</h3>
<p><a href="https://github.com/github/statsd-ruby">statsd-ruby</a></p>
<p><a href="http://codeascraft.etsy.com/2011/02/15/measure-anything-measure-everything/">Measure anything, measure everything</a></p>

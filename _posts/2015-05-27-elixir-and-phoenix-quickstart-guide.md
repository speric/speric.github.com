---
layout: post
title: Elixir and Phoenix Quickstart Guide 
header_img: "shop.jpg"
description: Get up and running with Elixir and Phoenix on OS X 10.10.3.
---
<p>I've begun tinkering with <a href="http://elixir-lang.org/">Elixir</a> and the <a href="http://www.phoenixframework.org/">Phoenix framework</a>. The guides on the Phoenix site seem to be slightly out of date, so I thought it'd be helpful to post a quick start guide. I am running OS X 10.10.3.</p>

<h4>1 - Install Elixir via Homebrew</h4>
<pre>$ brew install elixir</pre>

<p>The latest version as of today is `1.0.4`. Along with Elixir comes <a href="http://elixir-lang.org/docs/stable/mix/">Mix</a>.

<blockquote><p>Mix is a build tool that provides tasks for creating, compiling, testing Elixir projects, as well as handle dependencies, and more.</p></blockquote>

<h4>2 - Install Hex</h4>

<blockquote><p><a href="https://hex.pm">Hex</a> is a package manager for the Erlang ecosystem.</p></blockquote>

<pre>$ mix local.hex</pre>

<h4>3 - Install latest version of Phoenix</h4>
<p>As of today, the latest Phoenix release is `v0.13.1`. Check the project's <a href="https://github.com/phoenixframework/phoenix/releases">Releases page</a> to be sure you're going to install the latest version.</p>

<pre>$ mix archive.install https://github.com/phoenixframework/phoenix/releases/download/v0.13.1/phoenix_new-0.13.1.ez</pre>

<h4>4 - Create your Phoenix app</h4>
<p>By default, your app will be created in the same directory you're in
when you run this command:</p> 

<pre>$ mix phoenix.new phoenix_test_app</pre>

<p>Specifying an absolute path also works:</p>

<pre>$ mix phoenix.new /path/to/my/phoenix_test_app</pre>

<p>Be sure to type "Y" when prompted to install all dependencies.</p>

<h4>5 - Start the web server</h5>

<pre>$ cd phoenix_test_app
$ mix phoenix.server</pre>

<p>Open your browser and visit <a href="http://localhost:4000">http://localhost:4000</a>. You should see the following:</p>

<center><div><img src="/assets/images/phoenix.png" class="img-polaroid"/></center>



---
layout: post
title: Sinatra, ActiveRecord, and MySQL
---
<p>If you want to use ActiveRecord with <a href="http://www.sinatrarb.com/">Sinatra</a>, the <a href="https://github.com/janko-m/sinatra-activerecord">sinatra-activerecord</a> gem is the way to go.  The examples in their documentation are for the <code>sqlite3</code> adapter.  I thought it'd be helpful to show how I used this gem with MySQL instead.  I created a <code>models</code> folder in my app root, where I store my ActiveRecord models.  You can include all of them in one file (like <code>models.rb</code>) or give each model it's own file.  We'll use the former for this example.</p>

<p>
<pre>
# config/database.yml
database: dbFoo
username: db_user
password: db_pass
host: localhost
port: 9999
</pre>
<p>

<p>
{% highlight ruby %}
# app.rb
require 'sinatra'
require 'sinatra/activerecord'
require 'yaml'
require './models/models.rb'

DB_CONFIG = YAML::load(File.open('config/database.yml'))

database_url = "mysql://#{DB_CONFIG['username']}:#{DB_CONFIG['password']}@#{DB_CONFIG['host']}:#{DB_CONFIG['port']}/#{DB_CONFIG['database']}"

set :database, database_url

get '/' do
  @items = Item.all
  erb :index
end
{% endhighlight %}
</p>

<p>Simple, and you get all the power of ActiveRecord as well as the ability to run migrations.</p>

<h3>See Also</h3>
<p><a href="http://www.sinatrarb.com/">Sinatra</a></p>
<p><a href="https://github.com/janko-m/sinatra-activerecord">sinatra-activerecord</a></p>
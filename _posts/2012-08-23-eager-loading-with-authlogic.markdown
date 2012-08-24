---
layout: post
title: Eager Loading With Authlogic
---
<p>I've used <a href="https://github.com/binarylogic/authlogic/">authlogic</a> for authentication and session management on a number of projects.  For one application, I wanted to eager-load an association at the time of a successful sign-in.  I thought it'd be helpful to demonstrate the code here, since it took some digging through the documentation and other examples to figure out how to accomplish this.</p>

<p>Basically you want to create a new session within a <code>with_scope</code> block, and pass a <code>find_options</code> hash with whatever options you need.  Here's the <code>create</code> action in the <code>UserSessions</code> controller:</p>

<p>
{% highlight ruby %}
class UserSessionsController < ApplicationController
  
  def create
    UserSession.with_scope(:find_options => {:include => :photos}) do
      @user_session = UserSession.new(params[:user_session])
    end
    if @user_session.save
		  flash[:notice] = "Login successful!"
		  redirect_back_or_default account_url
		else
		  render :action => :new
		end
  end
end
{% endhighlight %}
</p>

<h3>See Also</h3>
<p><a href="http://rdoc.info/github/binarylogic/authlogic">authlogic documentation</a></p>
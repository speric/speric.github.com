---
layout: post
title: Water Collected Between Two Towers in Elixir
header_img: "podcast.png"
description: "My solution to an interesting problem."
---
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

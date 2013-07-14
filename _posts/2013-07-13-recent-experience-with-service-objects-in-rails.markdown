---
layout: post
title: Recent Experience With Service Objects In Rails
---
<p>I read <a href="https://twitter.com/brynary">Bryan Helmkamp's</a> blog post <a href="http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/">7 Patterns to Refactor Fat ActiveRecord Models</a> when it was originally posted, but it's taken me until recently to implement some of his suggestions in <a href="https://talentsoup.com">TalentSoup</a>. Specifically, I've been writing service objects, using similar criteria to Bryan.</p>

<p>One quick example of how I've used service objects is in the downgrading process. Previously, my code for downgrading an account looked like this:</p>

<p>
{% highlight ruby %}
#controller action
def downgrade
  @subscription = Chargify::Subscription.find_by_customer_reference(@current_user.chargify_reference)
  @current_user.downgrade_brand_to("new_brand", @subscription)
  AuditLog.create(user_id: @current_user.id, content: params[:downgrade_reason])
  flash[:notice] = "Your account has been successfully downgraded."
  redirect_to :controller => :home
end
{% endhighlight %}
</p>

<p>My <code>user.rb</code> class was responsible for post-downgrade cleanup in the <code>downgrade_brand_to</code> method, like canceling the subscription with Chargify, and resetting some of the features availabile to our Pro users.  The code certainly worked, but there were a number of things wrong the approach (besides how obviously hideous it is!):</p>
  
<ul>
  <li><p>Spread out the business logic behind canceling across a controller and a model.  I always want to log the reason for a cancellation along with doing the actual canceling, but the previous implementation had those two occuring separately.</p></li>
  <li><p>Since there was no single point of entry that would encapsulate the entire downgrade process, it was hard to test.</p></li>
  <li><p>It was also not portable. We might want to downgrade in other parts of the app too (reconciliation process via Rake task, perhaps) and the current process would necessitate repeating ourselves, and possibly leaving out something important.</p></li>
</ul>

<p>My refactored code now looks like this:</p>

<p>
{% highlight ruby %}
#controller action
def downgrade
  @subscription = Chargify::Subscription.find_by_customer_reference(@current_user.chargify_reference)
  WebcompDowngrader.new(@current_user, @subscription, params[:downgrade_reason]).downgrade!
  redirect_to controller: :home, notice: "Your account has been successfully downgraded."
  end
end

#service object
class WebcompDowngrader
  def initialize(user, subscription, downgrade_reason)
    @user             = user
    @subscription     = subscription                       
    @downgrade_reason = downgrade_reason
  end
  
  def downgrade!
    reset_subscription_level
    delete_chargify_subscription    
    log_downgrade_reason
  end
  
  private
  
  def reset_subscription_level
    #some downgrading actions on the user's account...
    @user.save
  end
  
  def log_downgrade_reason
    AuditLog.create(user_id: @user.id, content: @downgrade_reason)
  end
  
  def delete_chargify_subscription
    Chargify::Subscription.delete(@subscription.id)
  end                             
end                                                                        
{% endhighlight %}
</p>

<p>I think this is much cleaner, it's reusable, and it's easier to test.</p>

<p>To be quite honest, as great as those things are, the biggest win for me so far has been that I feel better about the application.  I feel more confident going in to change things, because I can focus on much smaller parts of the application, rather than digging into God models. As a solo developer, it becomes harder to keep the whole application in your head as the code base grows.  Therefore, anything that will express business logic in a concise way and make it easier to come back and understand months later ("I just need to look at one small class to see what we do when we downgrade") is huge.</p>

<h3>See Also</h3>
<p><a href="http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/">7 Patterns to Refactor Fat ActiveRecord Models</a></p>
<p><a href="http://rubyrogues.com/083-rr-decomposing-fat-models-with-bryan-helmkamp/">Ruby Rogues - Decomposing Fat Models with Bryan Helmkamp</a></p>

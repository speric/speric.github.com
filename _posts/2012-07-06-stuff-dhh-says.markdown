---
layout: post
title: Stuff DHH Says
---
<p>One of the things that makes being a developer interesting (or maddening, depending on your perspective) is the need to keep your skillset up-to-date.  Even if time does not allow you to play with certain technologies or frameworks, you should still be aware of their existence, and have a general idea what they do and where you might use them.</p>

<p>The problem, as I see it, is that it's difficult to separate the signal from the noise.  With so many frameworks, methodologies, data stores, etc., one can feel overwhelmed quickly. A few prominent voices start touting this or that approach, and you're left with the question: do I jump on board?  Or do I stick with what I know and what has worked, and potentially get left behind?  These questions can affect careers.  You have to learn to balance the desire to stay up-to-date with the temptation to drink the Kool-Aid and take your eye off the ball, which is the fact that, in the <a href=”http://www.codersatwork.com/”>words</a> of <a href=”http://www.jwz.org/”>jwz</a>, you're not here to write code, you're here to ship products. What approach or technology or framework will help you actually ship code?</p>

<p>Over time, I have found <a href=”http://david.heinemeierhansson.com/”>DHH</a> to be a sane voice of reason in what can sometimes be an insane Rails community.  The framework he created has basically furnished me a career and lifestyle, so I have a high level of respect for him, and I think twice if I find myself disagreeing with him in a given area. Not only did he create the Rails framework, but 37signals has created some of, if not the most, successful Rails applications to date.</p>

<p>In the last few weeks I've watched DHH’s <a href=”https://twitter.com/dhh”>Twitter feed</a>
with great interest, as he's been offering his thoughts on various aspects of Rails development that are relevant to me.  His approach resonates with me, as he seems to advocate a sane, simple, common-sense approach to Rails development.  I’ve taken the time to aggregate some of his recent Tweets into paragraph form. This is helpful to me, and maybe it will be helpful to you.</p>
<br/>
<h3>On Testing and Testing Frameworks</h3>
<p><blockquote>Spurred by this debate on rails-core <a href=”https://groups.google.com/forum/?fromgroups#!topic/rubyonrails-core/_lcjRRgyhC0”>https://groups.google.com/forum/?fromgroups#!topic/rubyonrails-core/_lcjRRgyhC0</a>, I'd like to see some more real FactoryGirl code gisted.  All the tests I've seen written with FactoryGirl, or other factory approaches, have been worse than the equivalent fixture approach. I like <a href=”http://twitter.com/tenderlove”>@tenderlove</a>'s theory that people got hooked on factories before foxy fixtures with named associations and never revisited.  That's really the sad part about both rspec and FactoryGirl. Newcomers are lead astray to these tar pits instantly.  It's like most Rails books can't wait to pimp the framework ride. Hey yo, that chariot needs some spinning rims, yo! Ugh.</blockquote></p>
<hr>
<p><blockquote><a href=”http://twitter.com/rgreen</a>@rgreen</a>: everone is pushing Rspec. Where do we go for better instructions?<br/>
<a href=”http://twitter.com/dhh”>@dhh</a>: See Agile Web Development with Rails and stick to test/unit as it comes out of the box.</blockquote></p>
<hr>
<p><blockquote><a href=”http://twitter.com/paulvolpato</a>@paulvolpato</a>: if you don't mock often, do you tend to write unit tests where the object under test talks to real collaborators?<br/>
<a href=”http://twitter.com/dhh”>@dhh</a>: Yes. I don't believe much in the "complete isolation" idea. My unit tests are more like model tests and do hit the db.</blockquote></p>

<p>Sources: <a href=”http://bitly.com/bundles/o_t794esdva/5”>http://bitly.com/bundles/o_t794esdva/5</a></p>
<br/>
<h3>On Software Architects and Architecture</h3>
<p><blockquote>It's easy to forget that there are still people who proudly call themselves software architects, use UML, and clamor for repeatable process! I was only reminded by reading <a href=”http://www.javacodegeeks.com/2012/07/architects-need-pragmatic-software.html”>ttp://www.javacodegeeks.com/2012/07/architects-need-pragmatic-software.html</a>. Ugh. Here's a process for you: Software architects who don't implement a substantial amount of what they design will fuck shit up. Badly.</blockquote></p>
<hr>
<p><blockquote>Following up on the discussion from <a href=”http://twitter.com/rubyrogues”>@rubyrogues</a>, this is a great example of SRR pattern wankery: <a href=”http://www.naildrivin5.com/blog/2012/06/10/single-responsibility-principle-and-rails.html”>http://www.naildrivin5.com/blog/2012/06/10/single-responsibility-principle-and-rails.html</a>.  Premature extraction!  The over-architecture of injecting the mailer is particularly offensive. Reminds me of that thread where someone got canned for such code. Test is simple: Look at the original code and compare to the concoction that follows. Forget the authoritative pattern name. Is it better? This coordination work is exactly what the controller is there to perform. It's completely fine for controllers to be more than 1 line. (Just to be clear, I think moving the mailing action into the model is an abomination. Leave it in the controller. That's what it's for!) Preemptive programming is what happens when you just can't wait to apply your patterns until they're actually needed. I didn't think it was possible, but Single Responsibility Principle appears to be producing even more bullshit code than Law of Demeter. Love how these patterns are not supposed to bear critique in any of their examples. It's always about MAINTAINABILITY. You have to believe! If you can't provide a convincing before/after code for your pattern, you're selling snake oil. Fuck your faith-based programming bullshit.</blockquote></p>
<hr>
<p><blockquote>Another example where SRR went hog wild: <a href=”https://gist.github.com/2838490”>https://gist.github.com/2838490</a>. Every line of activity becomes a class instead of just a method. Fuck me. (The roar of the "proper OO" crowd in that thread going rah-rah-rah is hilarious, btw.) I think having the mailer call inline in the controller is a better fit until you have more real problems tugging at it.</blockquote></p>
<hr>
<p><blockquote>I'm a little late to the party, but unless you're actually reusing this PostComment logic in multiple places, I agree with your senior developer. Here's how I'd swing it in the regular controller:<br/>
{% highlight ruby %}
class PostsController
 before_filter :set_entry
 before_filter :reject_spam

 def create
   @comment = @entry.comments.create!(params[:post].permit(:title, :body).merge(author: current_user))

   Notifications.new_comment(@comment).deliver

   TwitterPoster.new(current_user, @comment.body).post              if @comment.share_on_twitter?
   FacebookPoster.new(current_user, @comment.body).action(:comment) if @comment.share_on_facebook?    
 end

 private
   def set_entry
     @entry = current_account.entries.find(params[:id])
   end

   def reject_spam
     head :bad_request if SpamChecker.spammy?(params[:post][:body])
   end
end
{% endhighlight %}
<br/>I'm assuming that the language detection is something that needs to happen in the comment, so I would keep it in that model -- but not sure what it does. (<i>In response to this thread: <a href=”https://gist.github.com/2838490”>https://gist.github.com/2838490</a></i>)</blockquote></p>

<p>Sources: <a href=”http://bitly.com/bundles/o_t794esdva/6”>http://bitly.com/bundles/o_t794esdva/6</a></p>
<br/>
<h3>On Rails Application Design</h3>
<p><blockquote><a href=”http://twitter.com/seandevineinc</a>@seandevineinc</a>: Any advice on when to use AR observers instead of AR callbacks?<br/>
<a href=”http://twitter.com/dhh”>@dhh</a>: I basically do not use Observers any more. I find them too indirect. I use callbacks all the time, though. But a general rule would be that callbacks should only be used for model concerns, observers can trigger external services. So if saving a model should send an email, I'd use an observer if I couldn't do it in the controller. Not a callback.</blockquote></p>
<hr>
<p><blockquote><a href=”http://twitter.com/rdetert</a>@rdetert</a>: what about observers for sending notifications?<br/>
<a href=”http://twitter.com/dhh”>@dhh</a>: I'm not a big fan in general. It makes the flow opaque. Just triggering from the controller is much simpler in most cases.</blockquote></p>

<p>Sources: <a href=http://bitly.com/bundles/o_t794esdva/7”>http://bitly.com/bundles/o_t794esdva/7</a></p>

<p>P.S. - The new <a href=”http://bitly.com”>bitly</a> is awesome.</p>
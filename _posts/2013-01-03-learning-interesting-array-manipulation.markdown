---
layout: post
title: Learning - Interesting Array Manipulation
---
<p>I said in the post about my <a href="http://ericfarkas.com/posts/learning-plan-for-2013/">2013 learning plans</a> that I'd blog about whatever new things I learn, even if they're dead simple or things I should have known already.  This is my first such post.</p>

<p>The first chapters of <a href="http://pragprog.com/book/ruby3/programming-ruby-1-9">Programming Ruby</a> have reinforced a lot of my Ruby knowledge, but on page 46 I was introduced to a method of array manipulation I hadn't seen before. I'll use a modified version of the examples given in the book but offer a more thorough explanation.</p>

<p>Let's start with this array:</p>

<p>
{% highlight ruby %}
a = [ 1, 3, 5, 7, 9 ]
#=> [1, 3, 5 , 7, 9]
{% endhighlight %}
</p>

<p>We can replace two elements with one using the following notation:</p>

<p>
{% highlight ruby %}
#=>a = [1, 3, 5 , 7, 9]
a[ 2, 2 ] = 'foo'
#=>a = [1, 3, "foo", 9]
{% endhighlight %}
</p>

<p>Think of <code>a[ 2, 2 ] = "foo"</code> as meaning, "starting at index 2, replace 2 elements with the following value".  We can modify the same notation to insert a value but not replace anything in the array:</p>

<p>
{% highlight ruby %}
#=>a = [1, 3, "foo", 9]
a[ 2, 0 ] = 'bar'
#=>a = [1, 3, "bar", "foo", 9]
{% endhighlight %}
</p>

<p>We can also think of <code>a[ 2, 0 ] = "bar"</code> as meaning, "at index 2, insert the following value".  It has the same effect as doing <code>a.insert(2, "bar")</code>.

<p>The range notation can also be used to replace a range of elements, like so:</p>

<p>
{% highlight ruby %}
#=>a = [1, 9, 8, 7, "bar", "foo", 9]
a[0..3] = []
#=>a = ["bar", "foo", 9]
{% endhighlight %}
</p>

<p>Think of this as "replace elements at indexes 0 to 3 with the following".  The interesting thing about both of these methods of array manipulation is that the number of new elements you're assigning doesn't have to equal the number of elements you're replacing.  Using our original array, I can do the following:</p>

<p>
{% highlight ruby %}
#=>a = a = [1, 3, 5 , 7, 9]
a[ 2, 2 ] = "fizz", "buzz", "fizz", "buzz"
#=>a = [1, 3, "fizz", "buzz", "fizz", "buzz", 9]

#the above is equivalent to:
#=>a = a = [1, 3, 5 , 7, 9]
a[ 2, 2 ] = ["fizz", "buzz", "fizz", "buzz"]
#=>a = [1, 3, "fizz", "buzz", "fizz", "buzz", 9]

# using our range notation, we can do the same as above:

#=>a = a = [1, 3, 5 , 7, 9]
a[2..3] = "fizz", "buzz", "fizz", "buzz"
#=>a = [1, 3, "fizz", "buzz", "fizz", "buzz", 9]
{% endhighlight %}
</p>

<p>In each example I replaced two elements with four elements.  The array grows automatically (see documentation on <a href="http://www.ruby-doc.org/core-1.9.3/Array.html#method-i-5B-5D-3D">[]=</a>)</p>

<p>One last thing.  If you specify a range outside of the size of the array, the intermin elements will be set to <code>nil</code>:</p>

<p>
{% highlight ruby %}
#=>a = ["bar", "foo", 9]
a[5..6] = "fizz", "buzz"
#=>a = ["bar", "foo", 9, nil, nil, "fizz", "buzz"]
{% endhighlight %}
</p>

<p>As always in Ruby, there are multiple ways to accomplish the same thing.</p>

<h3>See Also</h3>
<p><a href="http://www.ruby-doc.org/core-1.9.3/Array.html">Class: Array (Ruby 1.9.3)</a></p>
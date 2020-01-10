---
layout: post
title: "Questions I Ask During Code Review"
header_img: "code-review.jpg"
description: "What are some questions to ask when doing code review?"
---

_The following is adapted from an internal writeup I did for my team on the topic of
code review. It has been shaped by my experience as a developer and engineering
manager in the particular technical, social, and business contexts of my career. A year ago,
this document would have looked different. A year from now, it will hopefully look
different too. While I think some of these points are applicable in all contexts, I
make no claim that this is how everyone should do code review._

What should we look for when doing code review? How should we receive code reviews from others?

thoughbot's [Code Review](https://github.com/thoughtbot/guides/blob/master/code-review/README.md) guide is a
great starting point. It articulates principles for the relational side of code review. This
is especially relevant for us in a remote environment. _How_ we give and receive feedback is
as important as the feedback itself.

### Things To Look For In Code Review

The following is a very incomplete list of questions to ask yourself during code review. The
items here are meant to be starting points for a conversation with the code's author. Always
assume the best intentions.

* Is the commit message well-formed? See [How to Write a Git Commit Message](https://chris.beams.io/posts/git-commit/) and [5 Useful Tips For A Better Commit Message](https://robots.thoughtbot.com/5-useful-tips-for-a-better-commit-message).

* Given the commit message, do I, as a reviewer, understand the purpose of this code? Do I know how to manually QA it? Do I know how I'd even determine if the code does it what it claims to do?

* Has anyone outside of the author's team QA'd the feature or bugfix to verify it?

* Have all it's tests passed?

* Are there any Hound errors? Has ESLint been run on any new JS code?

* Are there any lines of code that make me scroll horizontally? If so, the line is probably too long.

* Are there tests? If not, why?

* Do the tests follow our RSpec conventions? If not, the author should present a case for introducing a new pattern.

* Are these the right tests? This can be hard to determine.

* Do the tests prove that the code does what it claims to do? Generally, there should _at least_ be tests at the [highest level possible level](https://robots.thoughtbot.com/testing-from-the-outsidein).

* Are the tests efficient? Do we need to hit the database _that many times_ to prove our code works? Do we need to reload that object? Are we doing `js: true` in feature specs that don't need JS? There are so many aspects to this question.

* Is the code readable (well-named variables and methods, etc.)?

* Is there duplication of _business logic_? This is not always a bad thing (see [The Wrong Abstraction](https://www.sandimetz.com/blog/2016/1/20/the-wrong-abstraction)). But we should be on the lookout for duplicated business logic nonetheless.

* Does the code follow the general pattern of other code that does similar things? If not, the author should present a case for introducing a new pattern.

* Can we achieve any performance boost (however small) from [memoizing](https://www.justinweiss.com/articles/4-simple-memoization-patterns-in-ruby-and-one-gem/)?

* If writing a new background job, has the Operations team taken a look at the code and approved it? What are the things they look at when determining if a background job is efficient or not? Learn those things!

* If writing an Elasticsearch query, is pagination going to behave the way the author thinks it will?

### Ruby-Specific

During code review we want to look for sub-optimal Ruby Standard API usage. Most of
these instances will be related to misuse of [Enumberable](https://ruby-doc.org/core-2.6/Enumerable.html) methods. Reading [Refactor to use Ruby standard library](https://thomasleecopeland.com/2013/09/18/refactor-to-standard-library.html) will give you a good grounding in ways to make Ruby code more idiomatic.

The most common instances take this shape:
```ruby
def some_collection_filtering_method
  my_array = []
  @collection_from_class_args.each do |item|
    if item.meets_some_condition?
      my_array << item
    end
  end
  my_array
end
```
A more idiomatic approach would be to use [`#select`](https://ruby-doc.org/core-2.6/Enumerable.html#method-i-select):
```ruby
def some_collection_filtering_method
  @collection_from_class_args.select(&:meets_some_condition?)
end
```

Similarly:
```ruby
def some_collection_transforming_method
  my_array = []
  @collection_from_class_args.each do |item|
    modified_item = widgetize(item)
    my_array << modified_item
  end
  my_array
end
```
A more idiomatic approach would be to use [`#map`](https://ruby-doc.org/core-2.6/Enumerable.html#method-i-map):
```ruby
def some_collection_transforming_method
  @collection_from_class_args.map do |item|
    widgetize(item)
  end
end
```
And if the result of `widgetize` could be `nil`, we could use [`#compact`](https://ruby-doc.org/core-2.6/Array.html#method-i-compact):
```ruby
def some_collection_transforming_method
  @collection_from_class_args.map do |item|
    widgetize(item)
  end.compact
end
```
One should look for these instances when doing code review.

### Rails-Specific
Some Rails-specific things to look out for in code review:

* Are there any unnecessary database calls? Are we missing an [eager-load](https://guides.rubyonrails.org/active_record_querying.html#eager-loading-associations)?

* Using `where(...).first` instead of `find_by(...)`.

* Using `update_attributes` instead of `update`.

* Not capturing the output of `save` or `update`. Why doesn't the author care if their database call was successful or not? If they don't care, should the code raise an exception if it's not successful, via `save!` or `update!`? Be careful of this in tests: if we are doing an `update` in a test but not saving the return value and ensuring it's `true`, how are we sure our test setup is correct?

* Not using `find_each` when iterating over collections of unknown size.

* Not scoping queries. There are cases where it's OK to do this, but they are rare.

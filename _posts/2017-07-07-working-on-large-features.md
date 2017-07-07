---
layout: post
title: "Working On Large Features"
header_img: "crane.jpg"
description: "Lessons learned leading and working on large features."
---

I've spent most of the last 18 months working on large features at my day job.  For my purposes, a "large feature" will have most of the following characteristics:

<ul>
  <li><p>Needs more than one developer, if the goal is to ship the feature in a reasonable timeframe (a month, a quarter, etc.).</p></li>
  <li><p>Immediately becomes a core aspect of the product offering, from a business perspective.</p></li>
  <li><p>Immediately becomes a core aspect of the application, from a developer perspective. An example of this would be doing a major version upgrade of Rails. Another is switching to a new background processing library.</p></li>
  <li><p>Involves changes to core aspects of the application's data model.</p></li>
  <li><p>Involves changes to core aspects of the application's business logic. A bug here would have negative business impact on customers.</p></li>
  <li><p>If extending an existing feature, then making significant UI changes. This involves bringing in a UI/UX-focused team member.</p></li>
  <li><p>Lots of new code, either reworking existing classes or introducing new ones. LOC is not a great measurement of the impact of a feature. Indeed, it's possible to leverage a small code change into a big win. But most large features that need more than one developer are also going to add a lot of new code.</p></li>
</ul>

Here are some things I've learned that have helped the teams I've been on to stay organized and ship large projects.

## Plan Up-front

The worst thing to do when working on a large project is to start coding immediately. Resist the urge to spike. Instead, spend time planning what you will work on. 

This will always involve a bit of guesswork. Many times, you will be working with incomplete requirements. You won't have all the resources you need. And, of course, things change once people start using the actual feature.

What you're trying to do is articulate what you do know, and how you'll go about executing on it. I recommend using a tool like Trello to make cards for each distinct task. Even if you are not sure of all that the task entails, write it down.

Doing this accomplishes two things. One, it frees you from having to keep the entire feature in your head. Since you are working with other developers, this is a good thing. Second, it teases out the things you don't know about the feature. You can then bring those questions to the right people. I create a "Questions" list in Trello for these issues, and ping the people who can help resolve them. In the meantime, I can work on what I do know.

## Ship Early

As people a lot smarter than I [have](https://blog.newrelic.com/2012/11/14/long-running-branches-considered-harmful/) [argued](https://martinfowler.com/bliki/FeatureBranch.html), long-running feature branches can be painful. But sometimes you can't avoid them. What I look for in the up-front planning phase are opportunities to get code into master as soon as possible. This way, if we do need a long-running feature branch, it will be as small and easy to review as possible.

With enough planning, database migrations are good candidates for early merging. Code refactoring that makes a path for new behavior is also a good candidate. For a recent feature, we shipped a refactoring of core business logic to production a month before the rest of the feature. In this way, we proved that the changes introduced in the new feature would be "backwards compatible". It gave the rest of the team a comfort level with the new feature that they would have otherwise not have had. Other candidates are UI changes that are behind a feature flag, and new API endpoints that are not public yet.

An added benefit of going to master as soon as possible with smaller code changes is that team members not working on the feature need less context to review pull requests. Huge pull requests are hard to review, and often become a matter of "if it works, ship it!". Smaller PRs are easier to review. Teammates don't need to know the entire scope of the feature to review the change.

The faster you can get into master and deployed to production, the faster non-dev feature stakeholders can start using the feature. Again, I am standing on the shoulders of giants when I say this: the faster you can get to the point where you're getting real user feedback, the better. My own experience has borne this out.

What I have found is that the more I try to ship things earlier, the better I get at identifying _what_ to ship earlier. There doesn't seem to be a hard and fast rule here. You're going to need to experiment. I imagine much of your success in this area will depend on team culture and operational setup.

## Keep Everything Green

If you are going to use a feature branch, treat it like master: keep it green all the time. Pull requests into the feature branch should be green themselves. If the feature branch's tests are failing after a merge, stop what you're doing and fix them.

It's a nightmare to fix failing specs on a branch that has had a lot of churn. It feels like trying to hit a moving target. The sooner you fix failing tests, the better.

## Double Down On Strengths

In chapter 12 of the 1950 edition of [_The Armed Forces Officer_](https://www.amazon.com/Armed-Forces-Officer-1950/dp/098836963X), entitled "Group Nature", S.L.A. Marshall offers the following:

> Progress comes of making the most of strengths rather than looking for ways to repair weaknesses. This is true in things both large and small.

Marshall was speaking in a military context. The stakes there are much higher than shipping software. But the thought is an intriguing one.  I've come to understand that quote to mean this: when it comes time for battle, double down on your strengths. The time for self improvement, as an individual or as a group, is not on the battleground. You will learn on the battleground, but that's not your main purpose.

Applying that idea to teams doing software development requires a delicate balance. Developers always want to work with new things and learn. But when working on large features that have business-impacting scope, the idea seems to be that developers should work on tasks that cater to their strengths. 

This is not to say that developers shouldn't have opportunity to work with new tech. Say you have two developers on a project. They both know Rails well. One knows React well, and enjoys front-end work. One has less React knowledge, and enjoys back-end and API work. They should work on what they are best at and enjoy the most. It doesn't mean the developer who is front-end focused on this feature can't work on the back-end, and vice versa. There will be plenty of smaller tasks that are perfect to learn on. But I would argue the bulk of the work each developer does should be in the area they are strongest in. Not all, but most. In this way, morale will stay high, since both developers are doing what they enjoy. Additionally, output stays high, since both are doing what they are comfortable with.

This isn't a hard and fast rule. Sometimes both developers are good in the same area, and weak in the same area. The work will then be more evenly distributed. Team dynamics will always be at play here. In an environment with a lot of pairing, there will be more cross-pollination of learning. But even without pairing, learning can happen via pull request review as well.

The general idea is to play to your team's strengths.

## Stay Organized

Process for process sake is a waste of time. Process that gets in the way of shipping code is useless too. But process also gives structure to your work. The constraints of process provide the freedom within which one can stay focused on the task at hand.

We work hard to keep our Trello board up-to-date. If you are working on a card, assign it to yourself and move it to the "Doing" column. Move it to "Done" after merging the pull request. If there's a discussion going on in Slack which results in a new task, put it in Trello. Keep checklists.

Doing these things allow us to actually focus on writing code. I don't need to worry about remembering that thing we were talking about on standup because I created a card for it. I don't need to remember what I am working on next. There are cards already assigned to me, or I can grab the next one from the "To Do" list. If a manager asks me about the status of the project, a glance at Trello will give them all the information they need.

The actual tool you use to organize is not as important as the fact of staying organized. I cannot stress enough how important it is to have an up-to-date task list that is accessible by any team member. I've never regretted taking the time to create and maintain such a list. 

## Summary

I am still learning about this part of the craft of software development. There's still more to learn.  But in the meantime, these are some of the things that have helped the teams I've been on ship features.

---
layout: post
title: "The Map Is Not The Territory"
header_img: "li-map.png"
description: "How to think about your work."
---

I've been thinking about how the idea that ["the map is not the territory"](https://fs.blog/2015/11/map-and-territory/)
applies to software development.

One useful way to tackle the process of building software is to use tools that
break down tasks and features into smaller tasks, often represented by "cards". Indeed, one
valuable skill for programmers to develop is being able to break down a large task
into smaller, discrete components.

However, if we don't pay attention, those same tools can
lull us into thinking of our application in terms of the smaller units those "cards" represent. We
begin to mistake the map for the territory.

In reality, an application is a _system_. Changes in one area can (and often do)
have implications in seemingly unrelated areas of the system. This is true not only
on the level of actual code, but in the capabilites an application affords.

How many times have you come across a bug you were _sure_ wasn't related to a recent change, only to discover that
it was? Or implemented a new feature that gave users a new capability, only to realize that
you inadvertently allowed users to put data in a state that another part of the app was not ready
to handle?

Therefore, it's necessary to step back from your task and ask: what are the implications
of this change for the whole application? What am I missing? What questions am I not asking?

A good team lead will encourage everyone, regardless of experience level, to think in
these terms. Once we are asking these questions, together, as we plan out our work, it
will become natural for team members to think this way, individually.

The Trello board is not the iteration.

### See Also
[On Exactitude In Science](https://genius.com/Jorge-luis-borges-on-exactitude-in-science-annotated) by Jorge Luis Borges


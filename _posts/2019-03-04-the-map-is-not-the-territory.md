---
layout: post
title: "The Map Is Not The Territory"
header_img: "li-map.png"
description: "How to think about your work."
---

I've been thinking about how the idea that ["the map is not the territory"](https://fs.blog/2015/11/map-and-territory/)
applies to software development.

In software development, we use tools that us help break large tasks into smaller ones. We can represent these sub-tasks as "cards".

But, if we don't pay attention, those same tools can lull us into thinking of our application as the smaller units those "cards" represent. We begin to mistake the map for the territory.

In reality, an application is a _system_. Changes in one area can (and often do) have implications in other areas of the system. Put another way: few changes are actually _isolated_. This is true not only on the level of actual code, but in the capabilities an application affords.

How many times have you come across a bug you were _sure_ wasn't related to a recent change, only to discover that it was? Or gave users a new capability, only to discover you now have data in a state that another part of the app cannot handle?

Thus, it's necessary to step back from your tasks. You have to ask: what are the implications of this change for the whole application? What am I missing? What questions am I not asking?

A good team lead will encourage everyone, regardless of experience level, to think in these terms. Once the team is asking these questions, together, it will become natural for team members to think this way, on their own.

The Trello board is not the iteration.

### See Also
[On Exactitude In Science](https://genius.com/Jorge-luis-borges-on-exactitude-in-science-annotated) by Jorge Luis Borges

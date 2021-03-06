---
layout: post
title: CQRS Scalability
words: 344
characters: 1,943
readingTime: '00:01:43'
tags: [cqrs, design]
add_trailing_slash_for_disqus: true
---
Scalability is one of the several different benefits you gain from applying CQRS and Event Sourcing to your application architecture. And that is what I wanted to take a closer look at in this post.

One of the first obvious ways to increase the performance of your system is to split the Command from the Query side by using a service bus or just a simple queue. So instead of one machine being responsible for both responsibilities you now have 2 machines.

Then you also get the ability to measure more precisely what the actual bottleneck is, normally your application queries for data many times more then that it is executing behavior. And there are already many well known approaches for scaling your reporting database so that is not what I would like to talk about, but I guess it is clear that this side can now be scaled-out individually from the command side.

Now the command side; because even if this is used many times less then the query side, the actual behavior may take much more time and or processing power.

There are two natural ways of splitting up the domain, the first one is by Aggregate Root type, so you can choose to place a single (or multiple) Aggregate Root type on a different server. The second way is by splitting-up a single Aggregate Root type by the Identity each individual instance has. Think of for example about splitting them up depending on the last number of the Id, even versus un-even.

So who decides? The decision needs to be made by the process that accepts the commands and passes them on to the command handlers, these command handlers can then be running on different machines and each machine may even have it’s own Event Store.

This is a very flexible way of splitting-up your system into different components, and because of the Event Driven Architecture basically build-into this approach you will be able to trigger other behavior on different Aggregate Roots without added trouble.
---
layout: post
title: CQRS Event Versioning
words: 507
characters: 2,822
readingTime: '00:02:32'
tags: [cqrs, design]
add_trailing_slash_for_disqus: true
---
When using Event Sourcing you store your events in an Event Store. This Event Store can only insert new events and read historical events, nothing more nothing less. So when you change your domain logic and also the events belonging to this behavior, then you cannot go back into the Event Store and do a one time convert of all the historical events belonging to the same behavior. The Event Store needs to stay intact, that is one of its powers.

So you make a new version of the original event, this new version carries more or less information then the original one. Lets take a look at a very simple example:

<script src="http://gist.github.com/507250.js?file=1.cs"></script>

This to me looks like a natural evolution for this type of event, so how do you deal with this. Because after having used the system, before adding this extension there have been many cash withdrawals. So all these events are in the Event Store, they cannot be altered, and when you retrieve an Aggregate Root from the Event Store all these historical events need to be processed in order to restore the internal state.

Now what you don’t want is to maintain code in the Aggregate Root that knows how to handle these old event versions, sure one version is ok, but what about one hundred different versions? Also we are not just talking about just in the Aggregate Root, also the different event handlers need to be kept and maintained.

The better approach is to have a mechanism that you can hook-up with different event convertors. Then when an event is retrieved from the Event Store it first goes through this pipeline of convertors to be converted to the latest event version.

Now I wanted to do this properly and write some actual code for this, and then blog about it, but someone kept nagging me about it, so here is a very rough spike instead, first some tests:

<script src="http://gist.github.com/507250.js?file=2.cs"></script>

So basically some tests to confirm the correct conversion from one event to another event, now below here is the full implementation:

<script src="http://gist.github.com/507250.js?file=3.cs"></script>

This implementation is definitely not very elegant (so it doesn’t really belong on this blog) but hey it does show you how a possible solution would work. When building this yourself you might want to use conventions to auto register the convertors and chain them together during configuration so there is no need for the recursive functionality.

Also look at the jump from version 1 to version 4, this is an optimization to speed up the conversion. You would do this after a few versions, not for each version.

I’ll be adding a proper solution to the example in the near future, something that you would just plug the convertors in and the system would figure out how to handle them itself.
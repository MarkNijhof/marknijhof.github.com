---
layout: post
title: CQRS Event Sourcing
words: 607
characters: 3,478
readingTime: '00:03:02'
tags: [cqrs, design]
add_trailing_slash_for_disqus: true
---
So after reading this blog post by Rob Conery about [Reporting In NoSQL](http://blog.wekeroad.com/2010/02/05/reporting-in-nosql) where he explains very well what the problem is when using a RDBMS for persisting the state of your domain, or really anything that is written with Object Orientation in mind.

His solution to the problem is to use a object database or a document database for persisting the state of your object structure. And I do agree that this is a valuable approach to solve the problem.

But I would like to talk about a different approach, which is called [Event Sourcing](http://martinfowler.com/eaaDev/EventSourcing.html); a pattern thought of by Martin Fowler that, “Captures all changes to an application state as a sequence of events.”

Hey that is interesting, so instead of trying to store the object tree in a whole, we just store all the individual state changes that the object tree encapsulates; meaning all the state changes that have happened during the complete lifetime of the object tree. These state changes are being represented in the form of events. And such an event is nothing more then a [Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object), so not an actual .Net event.

And the objects are also re-constructed from the same events by applying all the state changes that they represent, and thus coming back to the previous state in the identical way that it came there originally.

Now the interesting part with respect to persistence is that these events are being serialized using a technique you like (Binary, JSon or custom) and this serialized event (object) is persisted in an event store, and this event store will threat all serialized events equally.

This event store can be based on an object database, document database, file system or even a RDBMS, you basically need to have one collection that describes all the different objects that the event store has persisted this includes the Id and the Version. Then another collection will contain all the serialized events for each different object and they should be retrievable by the object Id ordered by their Version. So to simplify this, in a RDBMS this would mean 2 tables, in total.

So there is no impendence mismatch between the domain (object structure) and the persistence layer anymore. Which would mean that it is should pass Rob’s criteria’s as well.

But hey it doesn’t stop there, you get a real [audit log](http://martinfowler.com/eaaDev/AuditLog.html) for free a well. And you have the ability to replay all the events to abstract new information about certain state changes. For example a web store is in business for 6 months and now they would like to know when and where an item is being removed from the shopping cart. With an event store you will have this information from the start of the system, and you didn’t have to think about it straight from the start.

And this also enables an easier swift to an Event Driven Architecture as well, because you can start publishing the events outside of the domain and have different behavior or processes react on them.

In my [example](http://github.com/MarkNijhof/Fohjin/tree/master/Fohjin.DDD.Example/) I use a combination of CQRS and Event Sourcing and this makes a very powerful solution. I would recommend that when you are when applying CQRS you would do that in combination with Event Sourcing to get a very flexible system without much more complexity then just applying CQRS.
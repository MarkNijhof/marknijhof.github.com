---
layout: post
title: CQRS Domain State
words: 360
characters: 2,089
readingTime: '00:01:48'
tags: [cqrs, design]
add_trailing_slash_for_disqus: true
---
This morning [Aaron Jensen](http://codebetter.com/blogs/aaron.jensen/default.aspx) asked a really interesting [question](http://twitter.com/aaronjensen/status/6454974718) on Twitter “Should Aggregate Roots en Entities always keep their state if it is not needed for business decisions? Is firing events and relying on the reporting store enough?”. He really made me think, Aaron thanks for that!

So for example you have some behavior on your domain that gets called when a customer moves, this behavior will publish a Customer Moved event containing the new address. If the domain does not use the address information for any decision making, does it then need to be persisted in the aggregate root?

I guess it would greatly depend on whether or not you are using [Event Sourcing](http://martinfowler.com/eaaDev/EventSourcing.html) to persist your published events (this is what I would suggest). If you do then I don’t see a problem in skipping storing the address information in the aggregate root. Because if at a later time you need the address information to make some decisions then it is easy to retrieve this from the events when reloading the aggregate root from the event store. You just create the address property and an internal event handler to process the Customer Moved event. Just make sure you delete the snapshots first.

But if you do not store all the events that you publish, but instead store only a snapshot then I would not skip storing the new address information, because in the end the domain is responsible for the state, not the reporting side. In other words, you can rebuild the reporting side from the domain, but you cannot necessarily do the same in reverse. So if you don’t store the address information and you need to rebuild the reporting store then you cannot do this.

So my conclusion is that the information should be available on the domain side. And when using CQRS and Event Sourcing to store the events instead of the internal state of the domain then that makes it possible to skip having the information in the domain structure, else not.
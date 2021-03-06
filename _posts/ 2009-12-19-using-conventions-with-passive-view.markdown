---
layout: post
title: Using conventions with Passive View
words: 1,197
characters: 6,836
readingTime: '00:05:59'
tags: [design]
add_trailing_slash_for_disqus: true
---
I was reading Ayende’s blog post about [building UI based on conventions](http://ayende.com/Blog/archive/2009/12/19/effectus-building-ui-based-on-conventions.aspx) and thought; hey I have something similar in my [CQRS example](http://github.com/MarkNijhof/Fohjin/tree/master/Fohjin.DDD.Example/). And since this is the least interesting part of the whole example I guess it will be missed by many, and I can’t let that happen.

##Passive View##

The example has a Win Forms application in there that is build accordingly to the [Passive View pattern](http://martinfowler.com/eaaDev/PassiveScreen.html), so my actual forms are being dumbed down to simple views without any logic in them, well almost no logic. Then I have presenters that have the actual logic in them, or delegate the logic to other responsible entities (services or whatever).

The reason for doing this is because you want to be able to test the behavioral parts of your code as simple as possible, and nothing is more simple then being able to unit test your behavior, hey even better drive your design / behavior through Test-Driven Design (TDD). Imagine how hard that would be to do when not abstracting these different responsibilities from each other (hehe I am sure some of you don’t even need to imagine this ;) ).

I realize the Win Forms is so not _in_ anymore and that nobody uses them, but perhaps what I show you here makes you think about other areas that this could be applied to.

##The View##

The view is responsible for displaying information to a user, capturing user requests, and … uhm no that’s it. Let’s take a look at a simple view.

<script src="http://gist.github.com/503862.js?file=1.cs"></script>

So what is happening here? As you can see there are two public events declared and there are two properties that provide access to some form controls. The interesting thing here is that the two public events are wired to two events from two form controls. But hey nothing is happening; no behavior, no calls to services or anything. Below here is the declaration of the interface that the form implements.

<script src="http://gist.github.com/503862.js?file=2.cs"></script>

Here are both the two public events and the two properties that provide access to two form controls, of course the implementation could be anything. This interface is used by the presenter to control the view.

##The Presenter##

So the presenter is responsible for controlling the view; which includes setting and retrieving data and executing behavior that is triggered by the user of the view. Below is the presenter that is responsible for managing the previous mentioned view.

<script src="http://gist.github.com/503862.js?file=3.cs"></script>

For simplicity I removed any other external dependencies and replaced the behavior with a comment “Do something”. Talking about external dependencies; the interface that is implemented by the view is injected into the presenter, this is interesting.

Look at the Display method, there you see that the view that is injected first gets its data loaded and then actually gets activated. This means that the view will not get its own data, but that the presenter will provide it to the view, for this the presenter will use the two properties that where declared in the interface. And it means that the presenter is responsible for activating the view. This is different from how Win Forms works out of the box.

For this to work I had to change the Program class, here take a look:

<script src="http://gist.github.com/503862.js?file=4.cs"></script>

Look at line 17 in there, the method Display is called on the presenter, which then prepares and activates the view.

But I can hear you think; what about these two events that where declared on that interface as well, you know “OnCreateNewClient” and “OnOpenSelectedClient”? I can see some obvious candidates “CreateNewClient” and “OpenSelectedClient” but they are not wired-up together. What gives?

##The Magic##

You must have noticed that the names are very similar and that they follow a certain pattern, this is the convention that I have chosen to use. Basically I call my event handlers the same as the events without the “On” prefix. Then I have a base presenter class without the word “Base” because that would be EVIL. And this class will wire-up the events with the event handlers for me.

<script src="http://gist.github.com/503862.js?file=5.cs"></script>

The abstract base class Presenter needs the view interface as the generic parameter of the view that the presenter controls. A reference to the actual view will be injected into the presenter class. Then the first thing that happens is that I get all the events declared from the provided interface. Then I get the actual events from the provided view, but only those that have been defined on the provided interface. This makes it possible to define multiple interfaces on the same view that get controlled by different presenters. After this I get all the public methods from the presenter.

Once I have the events form the view and the methods from the presenter then I can start matching them together. As I mentioned before in my case I use a very simple convention where the event is prefixed with “On” so in order to get the event handler I only need to search my event handler collection for a name of the event minus “On”. Then finally the event gets the event handler added to its collection of event handlers.

When there is no event handler for a provided event then I throw an exception, because I consider this to be a bug. There might be event handlers that does not have an event associated with it, but I consider that less harmful since this logic would not be called anyway. This exception will be visible in the unit tests for the presenter.

##Reflection##

Yes this relies very heavily on reflection, but for this scenario I don’t mind. Indeed it is slower, but the question is; will you notice this when displaying a form, and I don’t think you will. You could improve this code by for example making the WireUpEventAndEventHandler an action and cache those for the combination of the presenter and view interface, but I don’t think that is worth the effort.

##The Specifications##

I am going to leave you with the specifications that I have for the presenter that I used for this post. The whole presenter is being tested by setting data on the view and triggering events. I am not calling the methods directly on the presenter itself. And if you want to see more, then get the code from [GitHub](http://github.com/MarkNijhof/Fohjin/tree/master/Fohjin.DDD.Example/).

<script src="http://gist.github.com/503862.js?file=6.cs"></script>

<script src="http://gist.github.com/503862.js?file=7.cs"></script>

<script src="http://gist.github.com/503862.js?file=8.cs"></script>
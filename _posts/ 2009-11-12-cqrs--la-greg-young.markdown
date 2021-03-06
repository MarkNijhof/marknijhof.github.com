---
layout: post
title: CQRS à la Greg Young
words: 3,813
characters: 21,773
readingTime: '00:19:03'
tags: [cqrs, design]
add_trailing_slash_for_disqus: true
---
I have had the pleasure of spending a 2 day course and many geek beers with Greg Young talking about Domain-Driven Design specifically focussed on the Command and Query Responsibility Segregation (CQRS) pattern. Greg has taken Domain-Driven Design from how Eric Evans describes it in his book and has adapted mostly the technical implementation of it. Command Query Separation (CQS) was originally thought of by Bertrand Meyer and is applied at object level

> Bertrand defines CQS as: every method should either be a command that performs an action, or a query that returns data to the caller, but not both. In other words, asking a question should not change the answer.

Greg however takes this same principle but he applies it to the whole architecture of a system, clearly separating the write side (Commands) from the read side (Queries) of the system. The write side is what we already know as the domain, containing all the behavior which is what makes the system valuable. The read side is specialized towards the specific reporting needs, think for example about the application screens that enables the users to execute domain behavior, but also any traditional reporting needs are provided by the read database.

So lets take a quick look at the overall architecture before we dive into the details:

<center><img src="http://cre8ivethought.s3.amazonaws.com/images/CQRS/DDD-Overview.jpg" alt="CQRS Overview" /></center>


You have to excuse me for not using Visio to make this drawing, but I really didn’t feel like tackling yet an other layer of complexity tonight. At least I did the labels in typed text, so it is readable. I will discuss this architecture in 4 phases, the order I choose is what I believe is the natural way your would think about this. In the end you will see that the whole principle is rather simple. Really. 

<center><img src="http://cre8ivethought.s3.amazonaws.com/images/CQRS/DDD-Devision.jpg" alt="CQRS Devision" /></center>

1. Queries
2. Commands
3. Internal Events
4. External Events (Publishing)

In order to understand this type of architecture better I decided to build an example application; I will use this example in this post to demonstrate the different aspects of the architecture to you. The example has already been released in the wild (ok I meant the DDD group on Yahoo) and can be found here: [http://github.com/MarkNijhof/Fohjin](http://github.com/MarkNijhof/Fohjin)

##Queries (reporting)##

The first part I would like to discuss is the reporting needs of a system, Greg defines any need for data from the system as a reporting need; this includes the various application screens which the user uses to base his decisions on. This seemed like a strange statement at first, but the more I think about it the more sense it makes. This data is purely used to inform the user (or other systems) about the current state of the system in the specific context of the user so that they can make certain decisions and execute domain behavior.

These reports will never be updated directly by the consumer of these reports, the data represents the state of the domain, so the domain is responsible for updating it. So all we really do on this side is report the current state of the system to who or what ever needs it.

When an application is requesting data for an specific screen than this should be done in one single call to the Query layer and in return it will get one single DTO containing all the needed data. Now because of this specific use of the data it makes sense to order and group it in such a way that is determined by the needs of the system. So we de-normalizing the data trying to make a single table reflect a single screen or usage in the client application. The reason for this is that data is normally queried many times more than domain behavior is being executed, so by optimizing this you will enhance the perceived performance of the system.

Here you may choose to use an ORM like NHibernate to facilitate the reading from the database, but considering that you would only be using a very small percentage of the capabilities of a proper ORM you may not need to go that way at all. Maybe going for Linq2Sql of even as Greg suggested using reflection to assemble the SQL statements directly from the DTO’s (using reflection and Convention over Configuration makes this rather simple) is perhaps a better solution for the problem. This will be up to you and probably depends on specific scenario and what you feel comfortable with.

In the example that I created to demonstrate this type of architecture I choose for using reflection of the DTO’s because I wanted to put the emphasis on the CQRS implementation and not on the ORM implementation.

The more traditional reporting needs will also get its own database schema and the data there will be optimized for that need as well, so in the end we will end up with quite a bit of duplication of the data, but that is all right. The process that is responsible for updating the data in the different databases will make sure that this happens in the correct way, we will go over this in a later part of this post.

##Commands (executing behavior on the domain)##

Lets first consider what would normally happen after receiving a DTO; the user would make changes to the data and save this back on the DTO. This DTO then gets shipped back to the back-end, converted into an entity and the ORM will make sure that the changes are persisted into the database.

This would result in loosing some very valuable information; the Why did this change happen? You completely loose the intent the user had when he changed the data, and this is one of the things Greg’s implementation of CQRS is solving.

CQRS as the name indicates uses Commands, these commands are created on the client application and then send to the Domain layer. Lets take an example: A customer from a bank comes in the office and tells the person behind the desk that he needs to change his address. And instead of just getting the customer information and making the changes directly in the address fields, the bank employee firsts asks the question; Why would you want to change your address? The most likely response would be because the customer has moved, but it could also be because there is an error in his address and that all his mail ends up with his downstairs neighbor. Now these are two completely different reasons to update a customer address. Why could this be important? Well granted the example is rather silly, but lets assume the bank wants to know how many customers go to a competitor after moving? How loyal are our customers, should we keep sending them specific information after they have moved x miles away? Right this information is completely lost in our original way of working, but when using commands and events (more on events later) we maintain the original intent of the action. So after asking the question the customer answers that he has indeed moved and the bank employee selects the “Customer has moved" in the application and gets the ability to change only the address. When clicking save a CustomerMovedCommand will be created with only the changed address and is send to the domain.

We also get one other great benefit from using these commands and that is that these commands are easy to communicate with our client while building or working on the system. Because our clients would most likely use these types of behavior when explaining what they want to accomplish. Al do Greg thinks times have changed, “Our grand failure", but it should really be the case that our clients talk their own domain language. When using these commands we can start talking the same language even in the code.

That is what Domain-Driven Design is all about, instead of doing something technical like update client, it is actually describing the process that the user uses into the code like; the client has moved.

<script src="http://gist.github.com/503301.js?file=Command.cs"></script>

All these commands will be send to the Command Bus which will delegate each command to the command handler or command handlers. This also effectively means that there is only one entry point into the domain and that is via the Bus. The responsibility of these command handlers is to execute the appropriate behavior on the domain. Close to all of these command handlers will have the repository injected to provide the ability to load needed the Aggregate Root on which then the appropriate behavior will be executed. Usually only one Aggregate Root will be needed in a single command handler. Later we will also take a closer look at the repository as it is different from your ordinary DDD repository.

##Command Handler##

<script src="http://gist.github.com/503301.js?file=CommandHandler.cs"></script>

As you can see a command handler has only one responsibility and that is to handle the one particular command by executing the appropriate domain behavior. The command handler should not be doing any domain logic itself. If there is a need for this than that logic should be moved into a service of its own. An example of this is in my example code is an incoming money transfer, more about that later.

##Internal Events (capturing intent)##

So finally we have arrived at the actual domain, the client has requested a certain view of our domain, has received the appropriate report DTO and has made a decision which resulted into a command being published. The appropriate command handler has then loaded the correct Aggregate Root and executed the appropriate domain behavior. So now what?

Now we are going to separate the domain behavior from the state changes resulting from this domain behavior including the triggering of external behavior. This would not be much different from how you would do this now, first verify the normal guards, do what you have to do, but don’t set any internal state, and don’t trigger any external behavior (Ok the last part is more an optional thing to consider, state is the key here). Instead of writing these state changes directly to the internal variables you create an event and fire it internally. This event as well as the method name of the behavior should be descriptive in the Ubiquitous Language of the domain. Then the event will be handled inside the domain Aggregate Root which will set the internal state to the correct values. Remember that the event handler should not be doing any logic other then setting the state, the logic should be in the domain method.

##Domain Behavior##

<script src="http://gist.github.com/503301.js?file=DomainBehavior.cs"></script>

##Domain Event##

<script src="http://gist.github.com/503301.js?file=DomainEvent.cs"></script>

##Internal Domain Event Handler##

<script src="http://gist.github.com/503301.js?file=InternalDomainEventHandler.cs"></script>

The reason why we want these events is because they now become part of our persistence strategy, meaning that the only information we will be persisting of an Aggregate Root are the generated events. So if every state change is triggered by an event, and an internal event handler has no other logic then setting the correct state (and that means not even deriving other information from the data in the event), then what we can do then is load all historical events and have the Aggregate Root replay them all internally bringing back the original state of the Aggregate Root in exactly the same way it got there in the first place. It really is the same as replaying a tape.

One thing to note is that these events are write only, you will never add, alter or remove an event. So if you suddenly end up with a bug in your system which is generating wrong events, then the only way for you to correct this is to generate a new compensating event correcting the results of the bug. Of course you want to fix the bug as well. This way you have also tracked when the bug was fixed and when the effects of the bug where corrected.

By having this architecture we now basically solved the problem of loosing original intent, because we keep all events that have ever happened and these evens are intent revealing. An other very interesting thing is that now you have an audit log for free, because nothing will ever change state without an event and the events are stored and used in building up the Aggregate Roots they are guaranteed in sync with each other.

##The Domain Repository##

I mentioned before that the Domain Repository would be completely different from one that is normally the result of practicing DDD. Normally you would end up with very specific repositories allowing the request of all kinds of different information from the domain. But when using Greg’s implementation of CQRS your domain is completely write only, so the repository only has to be able to Get an Aggregate Root by its Id and it must be able to save the generated events. You also completely get rid of any impedance mismatch between the domain and the persistence layer.

##Domain Repository Contract##

<script src="http://gist.github.com/503301.js?file=DomainRepositoryContract.cs"></script>

The reporting repositories on the other hand would probably look much more like the traditional repositories from DDD.

So what happens when you have 100.000 events that need to be replayed every-time you load the Aggregate Root, that will slow down your system immensely. So to counter this effect you would use the Memento pattern to take a snapshot from the internal state of the Aggregate root every x number of events. Then the repository will first request the snapshot, load that in the Aggregate Root and then request all the events that have occurred after the snapshot, bringing it back to the original state. This is only an optimization technique you would not delete events that happened before the snapshot, that would pretty much defeat the purpose of this architecture.

##Data Mining##

One other really interesting fact about storing all the events is that you can replay these events at a later date and retrieve important business information from them. And you get this information from the start of the system, instead having to build-in some extra logging and wait a few months for reliable data.

##External Events (publishing, letting others know)##

Finally we are getting to the fourth part of this explanation (and I am saying this more for my own sake, pfff). So what happens here, looking back at what we have so far, we can read domain state and we can execute behavior and update the internal state of the domain. The obvious thing that is missing is a way to bring the reporting database in sync with the current state of the domain. The way we will be doing this is by publishing the internal domain events outside the domain. Then there are event handlers that pick up on those events and bring the reporting database in sync. This is a place where you could use an ORM, but in fact it is very easy to just generate the needed SQL statements and execute them.

Greg actually mentioned a really nice way of caching these SQL statements, he would batch them in a single batch and execute the batch if it gets older then x seconds, or (and this is the interesting part) whenever a read request came in. So when a read request comes in this SQL statement is appended to the batch and the whole thing is executed, ensuring that the read request will always have the latest data available to that part of the system. More on this below here.

The domain repository is responsible for publishing the events, this would normally be inside a single transaction together with storing the events in the event store.

Events are also used to communicate between different Aggregate Roots, I my example I am using a transaction from one account to an other account. Here I generate an event that money is being transfered to an other account, this event will reduce the balance of the current account. Later an event handler will make the same change in the reporting database. An other event handler will actually forward the transaction to a service which checks if the target account is a local account, else forwards a money transfer to a different bank (in my example the different bank is actually the same, but via a different route). But lets assume that the money transfer goes to an internal account, in that case the service will publish a money transfer received command in the command bus and the whole process continues in a different Aggregate Root. So in this case the command is not triggered from a GUI but from a different part in the system.

There is an other interesting fact in the scenario when money is received from an other bank, because a money transfer only has the account number to identify the target account and not the Aggregate Root Id (you can’t expect foreign systems to know this, yes I know you can make this a natural Id as well) the money received service first needs to do a query to the reporting database requesting an account DTO where the account number is the same as the target account. When this is successful it will use the Id from the account DTO and put that in the command to be published.

But it doesn’t stop there, as I mentioned before you could also have events that don’t have any state change information but for example indicate that a message (Email, SMS or what ever, depends on the event handler) needs to be send to an user. And because you are using Domain Events for all this, everything will be stored in the Event Store, so you keep your history.

##Eventual Consistency##

Normally when beginning to implement CQRS you would start with a direct publishing mechanism so that storing the events and updating the reporting database happen in the same thread. When using this approach you have no problems with eventual consistency.

But when you system starts to grow you might get some performance problems and then you could start by implementing a bus disconnecting the publishing of the events and handling of these events. This means that it is possible and likely that your event store and reporting database are not completely in sync with each other, they are eventual consistent. Which means that it is possible that the user sees old data on his screens.

Depending on how critical this really is you can have different counter measures for this, which I will be going into in a different post as this is also not something the example provides.

##Specifications##

The specifications that you can write using this architecture is something that I really like, what you would do is talk to your client and ask him things about how he wants his process to work. So a possible scenario could be:

Withdrawing money from an account

So how would that go? Well the clients needs to have opened an account with our bank and he needs to have some money transfered on it in order to be able to withdraw money from it. And when this happens the balance of the account must be lowered to the correct amount. Ok so, given an account was opened, and a cash was deposited, when making a cash withdraw then we get a cash withdrawn event.

<script src="http://gist.github.com/503301.js?file=Withdrawing_cash__.cs"></script>

Ok ok, but now we have the same story, only now there is not enough money on the account, then we should give an exception. 

<script src="http://gist.github.com/503301.js?file=Withdrawing_cash_.cs"></script>

The cool part here is that the whole domain is seen as a black box, you are bringing it to a certain state exactly the same way as it is used, then you publish a command just like your application would, and after that you verify that the domain publishes the correct events and that their values are correct. This means that you are never testing your domain in a state that it cannot naturally get to, which makes the tests more reliable.

Now imagine a parser that takes all the class names, underneath each class name it would print the events that occurred to get into the current state. Then you print the command that we are testing. and finally you print the method names that test the actual outcome. This would be a very nice readable specification that the client at least can understand.

##Some other benefits##

One last benefit I would like to highlight from using this type of architecture is how easy it is to split the work load between different team, more specifically between team with different hourly rates. The domain logic is something that needs to be right, this is where you would put your more expensive developers on, the ones that understand the business, understand good coding practices, you know what I mean. But the read side is not as important, sure it needs to be correct as well, but this is not where the value lies, this can be done quickly and in a year or two again. This is something you let the cheaper developers create, it doesn’t require much domain knowledge all that is really important is that they need to know how the GUI needs to work, what commands they can use and what events to expect.

I think this is of great value for the business, something that is easily overlooked.

##Finally##

As you can see this is all very simple and straightforward. It is a different mindset, but once you enter this mindset you will notice that your applications will be much more behavioral versus CRUD. And hopefully our clients can move back into thinking about their business logic instead of the thinking about the CRUD way we have forced upon them. Also I would like to thank Greg Young for providing me with so much information and putting up with all my weird questions, and thank Jonathan Oliver and Mike Nichols for some improvements on the technical side.

---
layout: post
title: In The Enterprise
add_trailing_slash_for_disqus: true
---
I quite often see comments like; **"We are only allowed to use Microsoft Technologies"** and every-time I think **"Why?"**. What is the reason behind such a standpoint. What is won by limiting their choices in such a radical way?

##Customer Support##

Support from Microsoft is often the reason that I hear when I ask these questions, but is this really **that** valuable? 

When you are a gold partner or something then indeed you can ask Microsoft to help you out with some difficult issues that you are experiencing, and they will help you as much as they can. I also understood from [Kenneth](http://twitter.com/sphair) that when indeed a bug is found in the Microsoft code then that they can create a temporarily patch which will also end up in a next release. And besides this that it will also be made available to others in the form of a KB article. This is however not how I have experienced it.

One other thing is that Microsoft will not accept patches from us. For most of their software this is not a problem because it it closed source anyway, but they won't even accept if for the "Sorta Open Source" software they have.

Now all tho it sounds pretty great that you can ask Microsoft to help you out, is this something that you would be missing from using other solutions like Open Source Software?

###The OSS Alternative###

When we for example take a look at [NHibernate](http://nhforge.org/Default.aspx) (just to stay on the Microsoft platform) then you have a great community that can help you out with any sort of question you may have. The contributors can point you to the right recourses and even provide example code. In my experience the help you get is also very fast, but it is true, you can't just call a guy (all tho I have actually done that a few times as well) and tell them that you need help. You will need to help them to help you by providing good information and means to reproduce the problem.

And you also have full access to the source code, so you can look inside to see what is going wrong. After you have identified the problem you can fix it yourself as well. And when you submit a patch then they happily accept it from you. And when done properly it will be applied in the next release. Releases are most often also more frequent so the wait won't be that long. 

And finally, the community actually cares about your input, if you have an issue with the framework and have a great idea on how to do something different then they are more then happy to listen to you. And if it makes sense then it will be incorporated as well.

**So what did you really win here by skipping the alternatives?**

##Framework Support##

Ok here is another reason; Microsoft will keep supporting their software and keep improving it. 

Well this is just not true, sure for many software solutions this is true, especially the ones you have to pay for. But there are also many cases where Microsoft suddenly decided to not continue developing the solution. There have been many people that have lost a lot of time in for example; [Linq to SQL](http://ayende.com/Blog/archive/2008/10/31/microsoft-kills-linq-to-sql.aspx), [M](http://msdn.microsoft.com/en-us/library/dd285282.aspx) and the last one I heard about [IronRuby](http://blog.jimmy.schementi.com/2010/08/start-spreading-news-future-of-jimmy.html). Now true IronRuby will hopefully be picked up by the community and Microsoft will release the source code, but this is not what happened with the other products. 

And even if Microsoft releases the source code and the community picks up the work then you are perhaps not stuck anymore, but you won't be using Microsoft technology anymore either. But history has proven us that most of the times this won't be a concern anyway because the code will never be released.

###The OSS Alternative###

For this let us take a look at the [Ruby](http://ruby-lang.org/) community, this community is to my knowledge 100% Open Source. There are now [75.000+ Gems](http://twitter.com/qrush/status/22463676137) stored on [RubyGems.org](http://rubygems.org/) now **that** is a community. And of-course there will be many gems that are discontinued or not supported, but you will always have the source available. So you will never get stuck with some software that is not supported and you cannot touch. 

Oh and framework support is pretty impressive on some of these solutions, take a look at the most well know framework; Ruby on Rails. The first version was released on 13th of December 2005 and it has steadily gone from 1.0, 1.2. 2.0, 2.1, 2.2, 2.3 till now [3.0 just released on 29th of August 2010](http://weblog.rubyonrails.org/2010/8/29/rails-3-0-it-s-done). These are 3 major released in 5 years, and the minor releases in between where significant as well.

Another good thing is that there are usually different approaches to solving the same problem, which means that if one approach doesn't really fit your needs then you can try to find a different approach. Think for example about [Rails](http://rubyonrails.org/), [Padrino](http://www.padrinorb.com/) and [Sinatra](http://www.sinatrarb.com/) all are frameworks to provide web content, but where one is very complete there the other is very bare bone. So depending on your needs you choose one.

**So what did you really win here by skipping the alternatives?**

##It Is What We Know##

All our infrastructure and existing software is .Net and the guys building and maintaining it don't know anything else, so your new solution has to fit in there as well.

This is a scary argument, but most likely the most valid one (it is so scary because it is valid in many places). This is why you see that each and every project is using Microsoft SQL Server and that everything is build up from ASP.NET. This "It is what we know" argument even prevents many from using something like [ASP.NET MVC](http://www.asp.net/mvc), which compared to ASP.NET is a major step forwards.

And it is not only management deciding that this is how things work, I have heard developers as well choosing a technology that wasn't the right tool for the job just because they felt comfortable with it.

###The OSS Alternative###

The alternative is that you actively encourage your employees / college's to learn other things. Because they will learn different things and start understanding that the de-facto is not always the most appropriate choice, you will end up with better software. At least there is a good chance that your software solutions will be better. Even if only because by exposing them to the OSS world they also get a feel of something that Microsoft is very good at hiding from you; **proper software design and testability**.

At the very least by having more versatile people adapting to a new Microsoft technology will be much easier, but I am hoping for more.

**So what did you really win here by skipping the alternatives?**

##Finally##

I am really hoping for your input here; how do we get to open up the "enterprises" to accept that there is much more then only Microsoft. I am not even saying we should all be using Ruby, Closure or Scala to solve our problems (all tho that would be great). But even on the Microsoft platform there are many good alternatives to solve business problems in a better way.

Also if you have reasons valid or not of why an organization would be staying with purely Microsoft software then I would love to hear about them as well.
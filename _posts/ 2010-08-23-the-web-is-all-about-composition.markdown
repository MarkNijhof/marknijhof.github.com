---
layout: post
title: The Web is all about Composition
add_trailing_slash_for_disqus: true
---
With new technologies we also get new ways of solving problems, and of-course this very much applies to the web. In the good old days we had static web pages that contained all the information we wanted to display. Web browsers would do one request per page and several parallel or sequential requests for images and style sheets. But time has changed this. 

Now we have browsers that know how to execute JavaScript and are able to make sequential requests triggered by JavaScript from the original request. This can also be triggered by the user; for example by clicking on a link or button. This additional requested content can then be placed anywhere on the page, even after the page has already been displayed to the user. 

##Composition##

So this means that we can start composing our web pages out of multiple resources, each resource responsible for one part of the web page. Do I hear someone say [SRP](http://en.wikipedia.org/wiki/Single_responsibility_principle)? 

Let's take a quick look at one very well know website: Amazon.com

<center><img src="http://cre8ivethought.s3.amazonaws.com/images/general/The-Web-is-all-about-Composition-1-Amazon.jpg" alt="Amazon" /></center>

On this page I count at least 11 different resources or components, each component has one single responsibility. And perhaps even more important, the different components on the page are not depending upon each other. I highlighted them for you:

<center><img src="http://cre8ivethought.s3.amazonaws.com/images/general/The-Web-is-all-about-Composition-2-Amazon.jpg" alt="Amazon" /></center>

Now when we for example look at the "Inspired by Your Wish List" item and "Netbooks Resellers" item, these two different content items do not relate to each other, at least not directly. There may be different rules at play that do take into account what content you are looking at, but these rules would mostly be affected by the main component of the page i.e. what the visitor is actually interested in, and who the visitor is.

##Layout##

So what would happen to the page if the rules engine decided that I should not see the "Inspired by Your Wish List" item? Well nothing drastically the "Netbooks Resellers" item would just move up and all other content would not be affected by this. 

This means that the layout of the web page needs to be flexible enough to display different configurations of different components. If we look at the Amazon site we clearly see three different columns, the two on the side always pretty much stay the same size. Content in these columns can be pretty fixed for this size. But the middle column scales to the width of the window, and Amazon is smart about it. When the window is small it only shows for example 3 items in the "Inspired by Your Wish List" item, but when you make it wider it will start showing more items, dynamically. This makes me think that Amazon has decided that a particular component can be as wide as it gets space, but can only be a certain height.

So the layout of the web page is important, but it is also important that the different components either know their configuration or can adapt to the environment. 

##Small Applications##

The next step is to think of each one of these components as their own little web application, they do one thing but they do them well. 

Why is this interesting, well it lets you iteratively add new functionality to your website without it affecting other parts of you site. It also lets you display different configurations to different visitors. For example when you know who is visiting you, you may want to show him/her some custom content. 

Now this would be very hard or become extremely complex if there was one single process that builds a web page, but when we split all these components into their own little applications then suddenly it is only one part in the code that is affected. 

Also all these components can also be tested independently from each other!

##JavaScript##

I really like to use JavaScript to load the different components onto the web page, the actual requested content is just a place holder. On the Amazon site this would be the top bar and the three columns and the footer. Then the web page will start requesting the different components from the server and adding them into the correct placeholders. For the end user the page is nicely responsive, content just starts popping up on the page. You would prioritize the main content of-course.

Then there are two ways that you can request the components, either request full html and load that into a placeholder or request Json and combine this with a template and load that into the placeholder. It would depend on the content which would be preferred. 

For example take a book description on Amazon, considering how often this is shown it might be a good idea to only request Json for each specific book, and make a separate request for the template. The template can be cached for a long time, so after the first book this piece of html is already on the client. And with a popular site as Amazon, it is very likely that this template was already at one of the nodes close by, so it wouldn't even have asked the Amazon servers for it the first time anyway. Then combine this cached template with the unique data and you are done. 

On second thought a book can probably be cached quit long as well, so perhaps a template is not the preferred option here. I think the deciding factor would be how often the content would change independently from the way it is presented. It all depends, just keep in mind that there are multiple ways and each way has different justifications.

Now I know Amazon doesn't use JavaScript to load the different components on their website, instead they let the web server aggregate the different components, more about that [here](http://highscalability.com/amazon-architecture), but I do believe that most of us could just use JavaScript to accomplish the same flexibility. 

##HTML 5##

I am just going to say here that we are facing interesting times when this becomes common ground!

##Shown; To be or not to be##

So who decides? Well first of all if the main request doesn't request the other components then they won't be shown, simple, but each component can also decide that it doesn't want to be visible to the particular visitor. It can for example return nothing and in that case the layout will just move the next components up. It can also decide that instead of displaying the actual component content it redirects the request to an ad service or something else.

I like to have the main request be responsible for the different components it should be able to show, usually this depends very much on the main content of that web page. I would keep this process as dumb as possible, one exception is whether or not the user is know. Because this is usually a drastic change of content. But if there is a beta program then this beta component should just always be requested. 

Then the different components themselves can decide if they should be visible or not, in case of the beta program maybe it is only shown for 5 procent of the users in Norway, that complexity I like to have at the component itself.

Then again, a site like Amazon probably has some sort of standardized logic for this, a pre-filter as it where to deal with this stuff.

##Finally##

Whether or not JavaScript is a good choice here really depends, but I am convinced that it is a good starting point. The beauty of this all is that you have started to think about your website in a different way. Instead of pages you start thinking about resources and aggregators of these resources. Then if later you need to move to aggregating these on the server instead of on the client it will not be such a huge job. 

Personally I think that the JavaScript approach can be pushed for a very long way. Maybe next time I'll show some code :)



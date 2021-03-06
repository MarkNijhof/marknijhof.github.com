---
layout: post
title: TDD Naming Styles
add_trailing_slash_for_disqus: true
---
Just recently I have been discussing a different approach to our naming convention for our test projects at work. A commonly accepted way is to append the project names with “Tests” or “Specifications” suffix. Like the following example shows us three different projects: 

<script src="http://gist.github.com/503707.js?file=projects_traditional.cs"></script>

But I don’t really like how the namespaces look like when you are appending the Tests or Specifications suffixes to the base namespaces. Take a look at the following namespaces in the three different projects: 

<script src="http://gist.github.com/503707.js?file=projects__traditional.cs"></script>

I would like to suggest an other approach, instead of appending a suffix I would like to prefix the project names with “Tests” or “Specifications”. Because this will result in the following namespaces: 

<script src="http://gist.github.com/503707.js?file=projects_my_proposition.cs"></script>

As you can see the namespaces in the Test and Specification projects are not interrupted with the words Tests or Specifications anymore. I know it is not a huge deal but I like how this reads just a little bit better. Also if you have multiple test or specification projects they are nicely grouped together, something I used to do using solution folders. Well actually still do that :)

##TDD class names##

Now if we have specific projects for our tests do we still need to append our classes with “Test”? I mean it is obvious that anything in these projects are related to testing. So I would say that that is not needed anymore.

So what do you say?
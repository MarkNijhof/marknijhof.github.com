---
layout: post
title: The .rvmrc File
add_trailing_slash_for_disqus: true
---
So after you have read my [last post](http://cre8ivethought.com/blog/2010/08/10/my-terminal-prompt/) I am sure you have been looking into [RVM](http://rvm.beginrescueend.com/) and if you haven't perhaps you should. And you have seen how much power and simplicity RVM can bring to your development workflow. Take for example my current situation, I have some projects in Ruby 1.8.7 and also in JRuby 1.4.0. Now switching between these is still a little bit of a pain, because I have to remember to tell RVM that I need a different Ruby version depending on the project I am working in.

So that is where the **.rvmrc** files come into play. Just add on of these files to your root directory of the project and specify the rvm command inside this file (f.ex. **"rvm 1.8.7"** or **"rvm jruby-1.4.0"**). Then every-time you hit the root of your project RVM will notice the **.rvmrc** file and execute the commands inside. Thus switching to the correct Ruby version for you.

RVM also allows you to group installed Gems together based on a tag, so if you f.ex. have two different projects that need different Gems but both of them are on Ruby 1.8.7 then you can specify a sort of group to each of these projects and then **"gem install xyz"** will install this gem only for that particular group. I'll go a bit deeper into this in a following post looking at both [Bundler](http://gembundler.com/) and [Gemsets](http://rvm.beginrescueend.com/gemsets/).
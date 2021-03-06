---
layout: post
title: Gemsets, Bundler and Versioning
add_trailing_slash_for_disqus: true
---
One of the things I like about Ruby versus .Net is how easy it is to work with different versions of a specific Gem, for example you wanted to upgrade a specific gem in a project from version 1.2.0 to 1.2.3, then all you needed to do is **gem install the_gem -v=1.2.3**. This command will just install the new version side by side with the previous version. Then in the project you only need to specify that you want to use the new version of the gem. If you don't specify a version then the latest installed version will be used automatically (so you want to make sure you specify a version). For example in Rails you can specify the version by adding the line: **config.gem 'the_gem', :version => '1.2.3'** in the environment.rb file, but there are also other ways.

So far this looks pretty much like how it is done in the .Net world, where we can have multiple different versions of a single DLL side by side in the GAC. But as many .Net developers already know, the GAC ain't a great place to be. And the same applies to the Ruby world, you would for example end up with many different Gems which each have many different versions. A complete nightmare to maintain, what project is using which Gem of what version? To solve this problem I want to introduce you to [Gemsets](http://rvm.beginrescueend.com/gemsets/) and [Bundler](http://gembundler.com/).

##Gemsets##

We will start with exploring [Gemsets](http://rvm.beginrescueend.com/gemsets/) a bit, this is a solution that comes with [RVM](http://rvm.beginrescueend.com/) and works at the lowest level. What Gemsets does is that it creates different folders for different named projects and installs the gems inside there. Then when you switch projects also the gems folder is switched. Thus nicely separating the different dependencies of each project you have on your machine. A great way to automatically switch to the correct project is to add this **rvm 1.8.7@project_name** to your [.rvmrc file](http://cre8ivethought.com/blog/2010/08/18/the-rvmrc-file/). 

You can also add **rvm --create use default@project_name > /dev/null** which will create the Gemsets project folder if it doesn't already exists, which is great for sharing your projects with team members. Or **export rvm_gemset_create_on_use_flag=1** to the root **~/.rvmrc** file, but that won't help team members that don't have that :) take a look [here](http://bcardarella.com/post/699582642/rvm-gemsets-bundler-awesome) for some background on this.

Now even tho this is very project orientated this is actually still very specific to the machine it is setup on, meaning that the project dependencies are very isolated from the other projects, but they are not managed by the projects themselves. In other words, if I get a new project from someone then I still have to setup all the Gems myself in the specific project location. 

You can import all declared dependencies from the **.gems** file by using **rvm gemset import**, but unlike Bundler this won't resolve dependency conflicts.

##Bundler##

And that is where [Bundler](http://gembundler.com/) comes into play, but before looking at the dependency resolution I like to talk about the fact that by using Bundler you have now also moved the dependency declaration towards the project. All you need to do is **gem install bundler** and create a **Gemfile** file in the root of your project. Inside this file you declare your project dependencies like **gem 'padrino', '0.9.10'**. There are more cool options like directly pulling the source from a GitHub repository, but you can read about that on the [Bundler](http://gembundler.com/) site. Now once you have declared all your dependencies you need to run **bundle install** which will start downloading all the required Gems and it will place them in a global cache or a project specific folder. 

Now so far it is not to different from how plain Ruby works, except that now all Gems are actually managed by the projects themselves. You could just delete all the Gems on your system, install Bundler and then let Bundler get all necessary Gems again.

Except there is more, you can and should now also run your Ruby code through Bundler. When you for example want to execute [Cucumber](http://cukes.info/) then you would normally run **cucumber** but this will just load the gems from you gem location without looking at the specified dependencies. But when you run **bundle exec cucumber** then Bundler will create an isolated environment in which it will run cucumber, meaning that if you have forgotten to add a Gem to the **Gemfile** then it won't run, even if this gem is installed on your system. 

Rails 3 will by default use Bundler, other frameworks may need some configuration in order to use it directly. 

Now before you start saying what a pain this is, think about the scenario where you need to deploy your application to a server, and instead of having to manually check all the different Gems needed, you just execute **bundle install** and you are done. And because you run your development environment also through Bundler you won't forget to add this needed dependency. 

[Heroku](http://www.heroku.com/) will do this all for you, if it detects a **Gemfile** in your GitHub repository then it automatically uses Bundler to resolve the dependencies, how cool is that!

##Dependency Resolution##

And now it is getting really interesting, we continue with Bundler. Let's say we have two different Gems, **gem_a** and **gem_b** now both of these Gems depend on **gem_c** but they both have different restrictions on the actual version of **gem_c**. **gem_a** can take any version of **gem_c** above 1.0.0, but **gem_b** only works with versions 1.0.* of **gem_c**. Now lets say **gem_c** has a version 1.1.0, this will work nicely for **gem_a** but not for **gem_b**. 

Bundler will resolve these issues nicely for you. Much more including this example can be found at [Yehuda Katz blog who is the creator of Bundler](http://yehudakatz.com/2010/04/12/some-of-the-problems-bundler-solves/).

##Combining Gemsets and Bundler##

I think that everything except the Bundler gem should be defined in the Bundler configuration (i.e. **Gemfile**) and that the Bundler gem itself should be managed by Gemsets. Because now all dependencies except for the Bundler version are managed by the project it self, and there is no chance that you forget to add a dependency because it was already installed on your system, this also includes Gems needed to execute the tests. But would of course exclude any System Gems.

##Reflecting on .Net##

There are currently some great efforts going on in the .Net world to solve the problems that the above solutions have solved for the Ruby world, think about [OpenWrap](http://github.com/openrasta/openwrap), [NU](http://github.com/phatboyg/nu) and [Bricks](http://bricksproject.org/) which is absolutely great! 

But I feel that what needs to be done first is that all .Net projects start implementing a good versioning policy, because without that nothing will solve this issue. And I truly hope that these three initiatives can at least come-up with **one** versioning scheme, but so far I haven't heard too much about that.




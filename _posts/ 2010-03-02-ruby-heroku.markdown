---
layout: post
title: Ruby Heroku
add_trailing_slash_for_disqus: true
---
When I started looking into Ruby more seriously I wanted to have an actual hosting provider that would host the site that I am working on. A friend of mine recommended me to look into [Heroku](http://www.heroku.com/) and I have to say that I am very happy that he did.

Heroku offers a unique way of deploying your Ruby web applications, you push them!
git push heroku master

Even do it is the first thing you see when visiting the site I still want to walk you through the process of setting up Heroku and deploying your Ruby application.

1. $ sudo gem install heroku
2. $ heroku create &gt;your application name&lt;
3. $ git push heroku master
4. $ open http://&gt;your application name&lt;.heroku.com/

Oh did I mention yet that the initial plan is free? Free as in you don’t have to pay anything! Of course when your application starts to attract visitors and you need more performance then you will need to start paying. But you have full control over this and I believe you can push it pretty far.

There are also many [addons](http://addons.heroku.com/) that you can enable (for some you need to pay for some not) providing a lot of functionality.

Now this looks like a commercial, but until someone told me about it I didn’t know, so now I am telling you. If you are doing some playing around or are actually working on a serious project. If it is in Ruby then this is worth checking out.

They are also offering some beta Node.js support!

More Ruby and other web stuff coming soon!
---
layout: post
title: Test Driven Spikes (TDS)
words: 1,218
characters: 6,366
readingTime: '00:06:05'
tags: [design, testing]
add_trailing_slash_for_disqus: true
---
I have had a discussion with a college of mine about spike code and how to bring the chosen solution into production code and quality. Jeremy [wrote about it a while back](http://codebetter.com/blogs/jeremy.miller/archive/2008/11/11/don-t-check-in-spike-code.aspx) stating to never check-in spiked code, and I was wondering if I could challenge that a little bit.

Now guys, don’t look at me in that way, I mean it well …

##What is spike code?##

[A spike solution is a very simple program to explore potential solutions](http://www.extremeprogramming.org/rules/spike.html). Well I like to add that I think a spike is relatively small, if it becomes very large I am wondering if we are not talking about prototyping instead? In my experience a spike is perhaps a days worth of coding, but then that it is rather much, more normal is an hour or two. Within this time there could be several attempts to get the desired result.

##There is spike code and there is SPIKE code##

Jeremy is talking about spike code to learn an API or something like that, in that case you don’t have a real use case that you need to solve so there I completely agree and it should be deleted as soon as you have learned what you needed to know (or perhaps checked-in into a special spike area). I would not really consider this writing spike code anyway.

Also code that is really hacked together and not following any good design principles, there adding tests afterwards would not give you anything, if at all possible. Just throw away that code and start over again.

##Test Driven Spikes (TDS)##

First of all sorry for another three letter acronym.

When I write a spike I almost always drive the execution of this code through tests, tests are dead easy to fire-of and they give you instant feedback. It is also very easy to have several tests next to each other exploring different angles or approaches. So while creating my spikes they are sort of driven by tests. And I use tests to verify that my spike code does what I expect from it. It has been a very long time ago that I created a console application to test or try-out some code.

But those are not spikes!

Well what is different here from actual Test Driven Development Design (can we just start calling TDD; Test Driven design?) is that I do take shortcuts, not all angles are covered correctly and my tests are not very descriptive and readable. So it is not production ready code. Also the actual code should be cleaned-up, different naming and removing the new keywords if they are any at all.

For example: I would write a specification (or test if you will) that dictates that I want such and so to happen when doing this and that. I do this from a fairly high level, so most likely not directly on the class that does the specific work, but it could be. Anyway while trying to get this to pass I run into a little problem. The solution is not very straightforward and I am stuck.

So for me that is when the spike begins, the existing test remains, but I ignore it for now. I probably create a similar test but now much closer to the actual problem itself. And from there I start playing with the code.

I would also try to take small subsets of the problem and try to fix that independently from the bigger problem area, again using tests. So when  have a few of these smaller subsets working I try to put them together. I refactor the code and tests to get my code working the way I need it to work.

When the spike goes bad I sometimes completely delete all the spike code and start over again, but more often I delete subsets of the spike and redo those. Then when I get the functionality that was needed in the initial specification I hook it up, and verify that it works.

When it does then I go back to the spike code and start cleaning it up as mentioned before, then I could also add some more tests for some edge cases, but more likely is that those edge cases would be discovered by sequential specifications, meaning actual use cases that are needed.

##Also I am a bit weird##

I am really bad at really hacking code together or I am just too stupid, but if I don’t clean-up after myself I lose the bigger picture or overview. Even in spikes, if I have the same code in a few places it annoys me, so I refactor it into a single method or into its own class. The same goes for naming, I don’t use variable names like a, b and c that makes no sense to me, also ReSharper makes it a no brainer anyway. Having unclean code just feels wrong to me.

Its the same with other things in my daily live, some stuff just needs to be true, like the car keys need to be on the desk. I can place them somewhere else, but next time I see it I move them to the right place. The volume knob in my car needs to be in a certain position, if it is not I use the volume on my steering wheel to adjust the knob without the volume. So yes I might have issues!

I think good design in spike code is important as well because it gives a better overview of what is going on, and if there is a place where you really need that then it is in code that you don’t really understand yet, i.e. spike code.

So in the end my spike code might actually get a much fine-grained test suite then the code that was not spiked. And this might actually make the test suite more brittle around this code, so it is not always a good thing, but for the time being I leave them where they are. Which is in a different place from the actual specifications so they are recognizable as spike tests.

##From memory##

Update: This paragraph came to me after having slept for the night.

I often hear that when deleting the spike code and starting over again you come-up with a much nicer solution then you finished with in the spike.

Now lets say you work like this and delete the whole spike code and started from scratch, don’t you think that the tests you will write are very much towards the already thought of solution? Can you really disconnect yourself from your previous attempt enough to come-up with better tests and potential better solution? I doubt it. Perhaps this will be true if you indeed really cut al corners, but even then the solution will be to close in memory.

##Conclusion##

So maybe we can conclude that I don’t know how to write spike code? Or that I am doing it all wrong? I would love to hear your opinions about this.
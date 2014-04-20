---
layout: post
category : design
title: Observer pattern. In most cases. wrong.
tagline: "design"
tags : [akka, EventSystem, scala, concurrent, asynchronous]
---
{% include JB/setup %}





## Observer pattern. In most cases: wrong.
- The thing being observed should not need to know about who is observing it.
- If you want to replace the thing being observed, your code is tighty bound to that specific implementation that you need to rewrite it for the new implementation.
- The larger the number of observers for thing "A", the larger the performance bottleneck becomes at thing "A" because thing "A" must now, sequentially notify each observer individually of each event they've registered to observe. Imagine if television worked this way, that is, for every show, they must individually stream that show to each person individually, in sequence. 

**_If you're architecting a system where this thing deals with the input and then this thing has to do the next part of the job, well if thing "A" calls thing "B", you've just complected it. Now you have a when and where thing because now "A" needs to know where "B" is in order to call "B" and when that happens is whenever "A" does it. Stick a queue in there. Queues are the way to just get rid of this problem. If you're not using queues extensively then you should start, right away, like right after this talk._**
-- <cite>[Rich Hickey - Simple Made Easy][1]</cite>

[1]:http://www.infoq.com/presentations/Simple-Made-Easy

**Summary:** Observer pattern is synchronous and sequential, complects unnecessarily and doesn't scale.

So let's look at an alternative that'll suit most cases from small toy application to large scale system. 




abstraction
- who, what, when, where, why, how
- i don't know, i don't want to know
00:54:50
when and where
strenuously avoid complecting these with anything in the design



##Publish/Subscribe

http://programmers.stackexchange.com/questions/144327/synchronous-vs-asynchronous-for-publish-subscribe-communication-between-javascr

http://www.dalnefre.com/wp/2011/05/actors-make-better-observers/


The publish/subscribe pattern can also be used within applications to provide scalability as an alternative to the more traditional Observable/Observer pattern as it offers some distinct advantages which I will address in a future article.


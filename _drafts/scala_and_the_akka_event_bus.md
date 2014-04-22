---
layout: post
category : examples
title: Publish/Subscribe using Scala and Akka EventBus
tagline: "example"
tags : [akka, EventBus, scala, concurrent, asynchronous, Publish/Subscribe, beginner, example, tutorial]
---
{% include JB/setup %}



<div class="intro">
<div class="intro-txt">
<p>
This post in the second in a series on implementing publish/subscribe with <span markdown="span">[Akka][2]</span> and <span markdown="span">[Scala][1]</span>. The first post, <span markdown="span">[Publish/Subscribe using Scala and Akka EventStream]({% post_url 2014-04-18-scala-and-the-akka-eventstream %})</span> covers some facets of Scala and Akka that'll be skipped over in this article, so it may well be worth a look if things aren't clear.
</p>
<p>
Publish/Subscribe (aka: pub/sub) is a messaging pattern that helps decouple<span markdown="span">[^1]</span> the sender and receiver of messages. Generally, this pattern is used across network(s) and provides ease of scalability and dynamic network topologies. 
</p>
<p>
The publish/subscribe pattern can also be used within applications to provide scalability as an alternative to the more traditional Observable/Observer pattern as it offers some distinct advantages which I will address in a future article.
</p>
</div> 
<div class="intro-img"><img class="article-image" src="{{ASSET_PATH}}/bootstrap/img/eventbus_250.jpg"/></div>
</div>
<br />
<br />
<br />


#### TL;DR
Just give me the code: [GitHub][4]
<br/>
<br/>






#### Notes
[^1]: Decoupling as far as space and time is concerned. Publish/Subscribe introduces a different type of coupling, namely: semantic coupling.

[1]:http://www.scala-lang.org/
[2]:http://akka.io/

[4]:https://github.com/benhowell/examples/tree/master/AkkaEventBus






















In an upcoming article, I will demonstrate the publish/subscribe pattern using the Akka EventBus andLookupClassification


http://www.razko.nl/blog/2013/10/10/using-akka-event-bus/


/*
  http://doc.akka.io/docs/akka/snapshot/scala/actors.html#Send%20messages

  Messages are sent to an Actor through one of the following methods.

! means “fire-and-forget”, e.g. send a message asynchronously and return immediately. Also known as tell.
? sends a message asynchronously and returns a Future representing a possible reply. Also known as ask.


Tell: Fire-forget
This is the preferred way of sending messages. No blocking waiting for a message. This gives the best concurrency and scalability characteristics.

actorRef ! message
If invoked from within an Actor, then the sending actor reference will be implicitly passed along with the message and available to the receiving Actor in its sender(): ActorRef member method. The target actor can use this to reply to the original sender, by using sender() ! replyMsg.

If invoked from an instance that is not an Actor the sender will be deadLetters actor reference by default.

   */
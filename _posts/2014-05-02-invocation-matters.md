---
layout: post
title: "Invocation Matters"
description: "Invocation Matters"
tagline: "design"
category: design
tags : [reactive, scalable, concurrent, asynchronous, patterns, design, publish/subscribe]
article_img: bootstrap/img/Gustave_dore_crusades_invocation_to_muhammad.jpg
article_img_title="Invocation by Gustave Doré."
---
{% include JB/setup %}
<div class="intro">
<div class="intro-txt">

<span markdown="span">
**Invocation**
</span>

/ˌɪnvə(ʊ)ˈkeɪʃ(ə)n/

<p>
<span markdown="span">_To activate. The action of invoking someone or something._</span>
<br/>
From the Latin verb invocare "to call on, invoke, to give".
</p>
  <p>
  Function invocation is such a fundamental exercise in our daily programming lives that we barely give it a second thought. That is, unless we have to. Issues such as scalability, portability, modularity and concurrency amongst others, sometimes force us to look for alternatives to the regular way of doing things. Indeed some libraries and languages actually force us to do so.
  </p>
  <p>
  In this article, we'll take a look the following patterns and outline the pros and cons of each and where to use them:
  <ul>
  <li>Plain Old Function Call</li>
  <li>Observer Pattern and Observables</li>
  <li>Message Queue</li>
  <li>Event Bus</li>
  </ul>
  
  </p>
  <br/>
  </div>
  <div class="intro-img-border">
  <div class="intro-img-bevel">
  <div class="intro-img">
  <img class="article-image" title={{page.article_img_title}} src="{{ASSET_PATH}}/{{page.article_img}}"/>
  </div>
  </div>
  </div>
</div>

<br/>
<br/>

Invocation forms the scaffolding between our otherwise disparate bits of code allowing us to compose solutions to problems with software. When I say "invocation", I'm talking about you, the omnipresent programmer, kicking some action off in code. Therefore the term "invocation" and/or "call", for the purposes of this article will cover both responsive and reactive methods of execution. I'll also be referring to functions, however the same arguments can be applied to <span markdown="span">methods[^1]</span> as well. Oh yeah, I may use any (or any part thereof) of the following terms and they are to be treated as synonyms: "publisher and subscriber", "producer and consumer", "caller and callee", "observer and observable".
<br/>
<br/>

#### Plain Old Function Call
`X.call(Y)`. This is a very straightforward, synchronous strategy to use and couldn't be simpler. Very succinct and no thought needed.

Pros:

 * good for calling functions where no logical [separation of concerns][2] exists.
 
 * good if the thing that performs the invocation needs to stop immediately and await the result of the call before being able to continue executing.
 
 
Cons:

 * tightly couples the caller and callee in both space and time.
 
 * inefficient and unmanageable when more than a small number of "other parties" need to be notified of events (i.e. a one-to-many relationship). 
 
 * isn't great for responsive systems (UI thread, game loop, long running listener or polling tasks, operating system, etc.) where the time bound of the invoked function(s) is unknown or susceptible to slow down in the calling of dependent functions. In other words, if the time bound of the functions being called within the loop are unknown or expensive compared to the loop execution speed itself, you may not be able to process events as quickly as they are generated.
 
Use:

 * when you need immediate feedback from the callee before being able to continue execution.
<br/>
<br/>
 
#### Observer Pattern and Observables
The [observer pattern][3] is similar to the "plain old function call" above, in that it is also a synchronous call and whilst allowing for [separation of concerns][2], still suffers the same cons. Generally, in observer/observable implementations, observers register their interest (using a callback) with certain events executed on the observable. The observable maintains this list of observer callbacks and each time an event occurs, the observable iterates over its list and calls each callback function in sequence. The observable is not tightly coupled with the entities observing it and doesn't care if it is not being observed at all. 

Pros:
 
 * provides a mechanism for implementing [open/closed principle][5] (OCP). In other words, if we can add more functionality to our system (e.g. another observer) without changing the functionality of the thing that generates events (i.e. the observable) then we have OCP. _Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification_ -- <cite>[Meyer, Bertrand (1988)][6]</cite>[^2].
 
 * removes the need for the observable to know how to call its individual observers (other than by the event handling function registered by the observer).
 
Cons:

 * does not execute tasks concurrently
 
 * isn't great for responsive systems (UI thread, game loop, long running listener or polling tasks, operating system, etc.) where the time bound of the invoked function(s) is unknown or susceptible to slow down in the calling of dependent functions. In other words, the larger the number of observers for thing "X", the larger the performance bottleneck becomes at thing "X" because thing "X" must now sequentially execute each observers callback individually each time the event they've registered to observe occurs.
 
 * if for any reason the observer dies or stops executing, the observable needs to explicitly handle the exception.
 
 * code may be harder to follow due to an inversion of control flow.
 
 * a lot of boilerplate is required for each implementation for things such as events, event listener interfaces (containing the observables callback handlers) and the event trigger functionality to iterate over and call all the callbacks. This boilerplate grows linearly with each new event type. Of course, YMMV a bit depending on language.
 
 * more complex to implement than other patterns (see "Message Queue" below). 
 
Use:

 * when an event or state change in one entity is acted upon by another and you do not want tight coupling between them.
 
 * when you have a one to many relationship, so that when an event or state change in an entity occurs, it can be acted upon by many others.
 
 * when you wish to leave open the possibility of extending the functionality of your software by using events generated by an entity in some future case that is yet to be coded. 
<br/>
<br/>

**A Brief Interlude...**

Both the "Plain Old Function Call" and "Observer Pattern" above are synchronous in nature. Synchronicity requires function invocation be done in a _where_ and _when_ fashion.

_If you're architecting a system where this thing deals with the input and then this thing has to do the next part of the job, well if thing "A" calls thing "B", you've just complected it. Now you have a when and where thing because now "A" needs to know where "B" is in order to call "B" and when that happens is whenever "A" does it. Stick a queue in there. Queues are the way to just get rid of this problem. If you're not using queues extensively then you should start, right away, like right after this talk._
-- <cite>[Rich Hickey - Simple Made Easy][4]</cite>

In systems where you can describe something happening as an "event" or state change (e.g. input has been parsed, button has been pressed, some process has completed, a new data point has arrived, whatever), then synchronous execution may not be good enough. We wouldn't want, for example, our UI thread to hang and become unresponsive while another piece of code handles the button press event, nor would we want to wait until a data point has been processed before being able to accept another data point in the stream. 

...which brings us to our asynchronous messaging patterns...
<br/>
<br/>

#### Message Queue
The [message queue][7] is an asynchronous messaging pattern that uses a first in, first out (FIFO) queue as a buffer between caller and callee. When an event or state change occurs, a caller can enqueue a message on the message queue, and, at a later point in time, a consumer can process that message or event by removing it from that queue. The consumer will process messages in the order of arrival (FIFO). Message queues decouple caller and callee in both space and time, that is "X" doesn't care where "Y" is or when (or if) "Y" will act on an event or message. The callee can process messages from the queue when it's good and ready which means the producer is no longer in control. Message queues may also have rules for message expiry and the like but that's beyond the scope of this article. The message queue pattern is a version of the publish/subscribe pattern, and can be implemented using a simple queue. It can also be implemented using actors as shown in a previous article: [Publish/Subscribe using Scala and Akka EventStream]({% post_url 2014-04-18-scala-and-the-akka-eventstream %})

Pros:

 * decouples caller and callee in both space and time.

 * efficient and manageable (in development terms) when many "other parties" need to be notified of events (i.e. a one-to-many relationship).
 
 * allows concurrent execution
 
 * good for responsive systems (UI thread, game, long running listener or polling tasks, etc.) as execution is not blocked within the loop. As soon as an event or message is pushed onto the message queue, execution continues.
 
 * the number of consumers can increase without modification to caller.
 
 * execution speed of the caller is not affected by a growth in consumers or in the time taken to execute messages in the queue.
 
 * simple to implement.

Cons:

 * no guarantees that the message or event will be executed in a timely manner, or even at all.
 
 * not good when you need immediate feedback from the callee before being able to continue execution.

Use:

 * when you want to decouple caller and callee in both space and time.
 
 * when you need to maintain a responsive system (UI thread, game loop, long running listener or polling tasks, operating system, etc.).
 
 * when not needing a guarantee that an event or message will be acted upon in a timely manner.
 
 * when one entity needs to notify another entity or entities of an event but doesn't require a response.
<br/>
<br/>

#### Event Bus
The [event bus][8] is another asynchronous messaging pattern that uses a first in, first out (FIFO) queue as a buffer between caller and callee and is also a version of the publish/subscribe pattern. Typically, only a single bus is implemented within a system and all events and messages from all parts of the system are published to that bus (in contrast to systems using message queues where there are typically numerous, specialised event queues). These days, the distinction between an event bus and message queue (as above) is largely non-existent. Historically, a "message queue" was a term used for intra application/process/system communication and "event bus" was a term used for inter application/process/system communication. For the purpose of this pattern, I will assume the event bus is the sole bus in the application. The event bus pattern can also be implemented using actors as shown in a previous article: [Publish/Subscribe using Scala and Akka EventBus]({% post_url 2014-04-23-scala_and_the_akka_event_bus %})

Pros:

 * decouples callers and callees in both space and time.

 * efficient and manageable (in development terms) when many "other parties" need to publish and be notified of events (i.e. a many-to-many relationship).
 
 * allows concurrent execution
 
 * good for responsive systems (UI thread, game, long running listener or polling tasks, etc.) as execution is not blocked within the loop. As soon as an event or message is published onto the event bus, execution continues.
 
 * the number of producers and consumers can increase without modification to either.
 
 * execution speed of the caller is not affected by a growth in consumers or in the time taken to execute messages in the queue.
 
 * simple to implement.
 
 * simpler to maintain than event queues (as there is only a single bus).

Cons:

 * no guarantees that the message or event will be executed in a timely manner, or even at all.
 
 * not good when you need immediate feedback from the callee before being able to continue execution.
 
 * may provide a single point of failure or bottleneck (e.g. high frequency producers and slow consumers).

Use:

 * when you want to decouple caller and callee in both space and time.
 
 * when you need to maintain a responsive system (UI thread, game loop, long running listener or polling tasks, operating system, etc.).
 
 * when not needing a guarantee that an event or message will be acted upon in a timely manner.
 
 * when one entity needs to notify another entity or entities of an event but doesn't require a response.
 
 * when publishers and consumers of various subsystems within your application are not impedance mismatched.
 
 * when global consumers are needed (e.g. loggers). 
<br/>
<br/>

#### Conclusion

The choice of invocation should be a considered one. Sometimes we need an immediate response before we can continue, sometimes we don't require a response but need immediate execution and for these situations a synchronous invocation pattern might be best. On the other hand if we want a responsive process and don't require a response from events or state changes, then an asynchronous publish/subscribe pattern may be best. Other considerations like the amount of inter-process communication may mean we implement a single event bus, but then again, we might want to use individual message queues for high velocity parts of our system to avoid any possible bottlenecks with slow consumers on the event bus. Perhaps we need concurrency for some reason or another? Also there's decoupling, reuse, extensibility and amount of boilerplate to consider as well!

Ultimately, the best solution could be a mixture of all the above patterns, both synchronous and asynchronous, or indeed the (infinitely) many other patterns not covered here. 
<br/>
<br/>

[1]:http://en.wikipedia.org/wiki/Message_passing
[2]:http://en.wikipedia.org/wiki/Separation_of_concerns
[3]:http://en.wikipedia.org/wiki/Observer_pattern
[4]:http://www.infoq.com/presentations/Simple-Made-Easy
[5]:http://en.wikipedia.org/wiki/Open/closed_principle
[6]:http://www.amazon.com/Object-Oriented-Software-Construction-CD-ROM-Edition/dp/0136291554
[7]:http://en.wikipedia.org/wiki/Message_queue
[8]:http://en.wikipedia.org/wiki/Event_monitoring


#### Notes
[^1]: Functions are routines for which all needed data is passed explicitly as parameters upon invocation. Methods are merely functions that reside within objects, and are able to operate on data within those objects without the need for that data to be passed explicitly upon invocation. 
[^2]: Meyer, Bertrand (1988). Object-Oriented Software Construction. Prentice Hall. ISBN 0-13-629049-3.

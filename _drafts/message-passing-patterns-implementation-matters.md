---
layout: post
title: "Invocation matters"
description: "hello"
tagline: "design"
category: design
tags : [concurrent, asynchronous, pattern, design]
---
{% include JB/setup %}
<div class="intro">
  <div class="intro-txt">
<h3> Invocation</h3>
/ˌɪnvə(ʊ)ˈkeɪʃ(ə)n/
<p>
<span markdown="span">_To activate. One usually speaks of invoking a function or routine in a program. In this sense, the term invoke is synonymous with call._</span>
</p>
  <p>
  Function invocation is such a fundamental exercise in our daily programming lives that we barely give it a second thought. That is, until we have to. Issues such as scalability and concurrency, amongst others, sometimes force us to look for alternatives to the regular <span markdown="span">`X.call(Y)`</span> way of doing things. Indeed some libraries and languages actually force us to do so.
  </p>
  <p>
  In this article, we'll take a look at some invocation strategies and outline the pros and cons of each.
  </p>
  <br/>
  </div>
  <div class="intro-img-border">
  <div class="intro-img-bevel">
  <div class="intro-img">
  <img class="article-image" title="Invocation by Gustave Doré." src="{{ASSET_PATH}}/bootstrap/img/Gustave_dore_crusades_invocation_to_muhammad.jpg"/>
  </div>
  </div>
  </div>
</div>
<br/>
<br/>






#### Fundamental 
Function invocation forms the scaffolding between our otherwise disparate program modules (provided we're [separating our concerns][2]) allowing us to compose solutions to problems in software. I will be using the term "invocation" throughout the article rather loosely and both reactive and responsive methods of executing code are encompassed by this term. I'll also be referring to functions, however the same arguments can be applied to <span markdown="span">methods[^1]</span> as well.
<br/>
<br/>


#### Plain Old Function Call
`X.call(Y)`. This is a very straight forward, synchronous strategy to use and couldn't be simpler. Very succinct and no thought needed.

Pros:

 * good for invoking functions within the same module
 
 * good for calling functions where no logical [separation of concern][2] exists
 
 
Cons:

 * cannot execute tasks concurrently
 
 * is no good for loops (UI, game, long running task, etc.) where the time bound of the invoked function(s) is unknown or susceptible to slow down in the calling of dependent functions
 

 
 
 
<br/>
<br/>
 
#### Observer, Observable
The [Observer pattern][3] is similar to the "Plain Old Function Call" above, in that it is also a synchronous call and whilst allowing for separation of concerns, still suffers the same cons. Generally, in observer/observable implmentations, `observers` register their interest (using a callback function) with certain events executed on the `observable`. The `observable` maintains this list of `observers` callbacks and each time an event occurs, the `observable` iterates over it's list and calls each callback function in sequence.

Pros:

 * good for invoking functions within the same module
 
 * good for calling functions where a [separation of concern][2] exists
 
 
Cons:

 * cannot execute tasks concurrently
 
 * is no good for loops (UI, game, long running task, etc.) where the time bound of the invoked function(s) is unknown or susceptible to slow down in execution speed due to an increase in observers, e.g. when the execution time spent collectively calling the observers callbacks exceeds the loop time interval. In other words, the larger the number of observers for thing "X", the larger the performance bottleneck becomes at thing "X" because thing "X" must now, sequentially notify each observer individually of each event they've registered to observe.
 
 * observers must be persistant for the life time of the observable or be handled by the observable (e.g. if for any reason the observer dies, or stops executing or whatever, the observable needs to explicitly handle the exception)
 
 * control flow is harder to understand due to an iversion of control flow.
 
 * a lot of boilerplate is required for each implementation such as events, event listener interfaces (containing the observables callback handlers) and the event trigger functionality to iterate over and call all the callbacks.
<br/>
<br/>

 
#### Message Queue
<br/>
<br/>

#### Mediator?
<br/>
<br/>



#### Message Bus
<br/>
<br/>




[1]:http://en.wikipedia.org/wiki/Message_passing
[2]:http://en.wikipedia.org/wiki/Separation_of_concerns
[3]:http://en.wikipedia.org/wiki/Observer_pattern


















http://www.marco.panizza.name/dispenseTM/slides/exerc/eventNotifier/eventNotifier.html




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





[message passing][1]










The observer pattern is a software design pattern in which an object, called the subject, maintains a list of its dependents, called observers, and notifies them automatically of any state changes, usually by calling one of their methods. It is mainly used to implement distributed event handling systems. The Observer pattern is also a key part in the familiar model–view–controller (MVC) architectural pattern.[1] In fact the observer pattern was first implemented in Smalltalk's MVC based user interface framework.[2] The observer pattern is implemented in numerous programming libraries and systems, including almost all GUI toolkits.

Related patterns: Publish–subscribe pattern, mediator, singleton.







The Observer Pattern

The Observer pattern [GoF] documents a design through which an object can notify its dependents of a change, while keeping that object decoupled from its dependents so that the object works just fine no matter how many dependents it has, even none at all. Its participants are a Subject—the object announcing changes in its state—and Observers—objects interested in receiving notification of changes in the Subject. When a subject's state changes, it sends itself Notify(), whose implementation knows the list of observers and sends Update() to each of them. Some observers may not be interested in this state change, but those who are can find out what the new state is by sending GetState() to the subject. The subject must also implement Attach(Observer) and Detach(Observer) methods that the observers use to register interest.

Observer provides two ways to get the new state from the subject to the observer: the push model and the pull model. With the push model, the Update call to each observer contains the new state as a parameter. Thus interested observers can avoid having to call GetState(), but effort is wasted passing data to uninterested observers. The opposite approach is the pull model, where the subject sends basic notification and each observer requests the new state from the subject. Thus each observer can request the exact details it wants, even none at all, but the subject often has to serve multiple requests for the same data. The push model requires a single, one-way communication—the Subject pushes the data to an Observer as part of the update. The pull model requires three one-way communications—the Subject notifies an Observer, the Observer requests the current state from the Subject, and the Subject sends the current state to the Observer. As we'll see, the number of one-way communications affects both the design-time complexity and the runtime performance of the notification.

The easiest way to implement a Subject's Notify() method is with a single thread, but that can have undesirable performance implications. A single thread will update each observer one-at-a-time, in sequence; so those at the end of a long list of observers may need to wait a long time for updates. And a subject spending a long time updating all of its observers isn't accomplishing anything else. Even worse, an observer may well use its update thread to recact to the update, querying the subject for state and processing the new data; such observer work in the update thread makes the update process take even longer.

Thus the more sophisticated way to implement a Subject's Notify() method is to run each Update() call in its own thread. Then all observers can be updated concurrently, and whatever work each may do in its update thread does not delay the other observers or the subject. The downside is that implementing multithreading and handling thread-management issues is more complex.

Distributed Observer

The Observer pattern tends to assume that the Subject and its Observers all run in the same application. The pattern's design supports distribution, where the Observers run in a seperate memory space from the Subject and perhaps from each other, but the distribution takes work. The Update() and GetState() methods, as well as the Attach and Detach methods, must be made remotely accessible (see Remote Procedure Invocation). Because the Subject must be able to call each Observer and vise versa, each object must be running in some type of ORB environment that allows the objects it contains to be invoked remotely. And because the update details and state data will be passed between memory spaces, the applications must be able to serialize (e.g., marshall) the objects they are passing.

Thus implementing Observer in a distributed environment can get rather complex. Not only is a multi-threaded Observer somewhat difficult to implement, but then making methods remotely accessible—and invoking them remotely—adds more difficulty. The can be a lot of work just to notify some dependents of state changes.

Another problem is that a Remote Procedure Invocation only works when the source of the call, the target, and the network connecting them are all working properly. If a Subject announces a change and a remote Observer is not ready to process the notification or is disconnected from the network, the Observer looses the notification. While the Observer may work fine without the notification in some cases, in other cases the lost notification may cause the Observer to become out of sync with the Subject—the whole problem the Observer pattern is designed to prevent.

Distribution also favors the push model over the pull model. As discussed earlier, push requires a single one-way communication whereas pull requires three. When the distribution is implemented via RPC's (remote procedure calls), push requires one call (Update()) whereas pull requires at least two calls (Update() and GetState()). RPC's have more overhead than non-distributed method invocations, so the extra calls required by the push approach can quickly hurt performance.

Publish-Subscribe

A Publish-Subscribe Channel implements the Observer pattern, making the pattern much easier to use amongst distributed applications. The pattern is implemented in three steps:

The messaging system administrator creates a Publish-Subscribe Channel. (This will be represented in Java applications as a JMS Topic.)
The application acting as the Subject creates a TopicPublisher (a type of MessageProducer) to send messages on the channel.
Each of the applications acting as an Observer (e.g., a dependent) creates a TopicSubscriber (a type of MessageConsumer) to receive messages on the channel. (This is analogous to calling the Attach(Observer) method in the Observer pattern.)
This establishes a connection between the subject and the observers through the channel. Now, whenever the subject has a change to announce, it does so by sending a message. The channel will ensure that each of the observers receives a copy of this message.

Here is a simple example of the code needed to announce the change:





#### Notes
[^1]: Functions are routines for which all needed data is passed explicitly as parameters upon invocation. Methods are merely functions that reside within objects, and are able to operate on data within those objects without the need for that data to be passed explicitly upon invocation. 










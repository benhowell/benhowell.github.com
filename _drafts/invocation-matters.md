---
layout: post
title: "Invocation Matters"
description: "Invocation Matters"
tagline: "design"
category: design
tags : [reactive, scalable, concurrent, asynchronous, patterns, design, publish/subscribe]
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
  In this article, we'll take a look the following patterns and outline the pros and cons of each:
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
  <img class="article-image" title="Invocation by Gustave Doré." src="{{ASSET_PATH}}/bootstrap/img/Gustave_dore_crusades_invocation_to_muhammad.jpg"/>
  </div>
  </div>
  </div>
</div>

<br/>
<br/>

Invocation forms the scaffolding between our otherwise disparate bits of code allowing us to compose solutions to problems with software. When I say "invocation", I'm talking about you, the omnipresent programmer, kicking some action off in code. Therefore the term "invocation" and/or "call", for the purposes of this article will cover both responsive and reactive methods of execution. I'll also be referring to functions, however the same arguments can be applied to <span markdown="span">methods[^1]</span> as well.
<br/>
<br/>

#### Plain Old Function Call
`X.call(Y)`. This is a very straightforward, synchronous strategy to use and couldn't be simpler. Very succinct and no thought needed.

Pros:

 * good for calling functions where no logical [separation of concern][2] exists.
 
 * if the thing that performs the invocation needs to stop immediately and await the result of the call before being able to continue executing.
 
 
Cons:

 * tightly couples the caller and callee in both space and time.
 
 * isn't great for loops (UI, game, long running listener or polling tasks, etc.) where the time bound of the invoked function(s) is unknown or susceptible to slow down in the calling of dependent functions. In other words, if the time bound of the functions being called within the loop are unknown or otherwise relatively expensive compared to the loop execution speed itself, you may not be able to process events as quickly as they arrive or are generated.
 
<br/>
<br/>
 
#### Observer Pattern and Observables
The [observer pattern][3] is similar to the "plain old function call" above, in that it is also a synchronous call and whilst allowing for [separation of concern][2], still suffers the same cons. Generally, in observer/observable implmentations, observers register their interest (using a callback function) with certain events executed on the observable. The observable maintains this list of observers callbacks and each time an event occurs, the observable iterates over its list and calls each callback function in sequence.

Pros:
 
 * provides a mechanism for implementing [open/closed principle][5] (OCP). In other words, if we can add more functionality to our system (e.g. another observer) without changing the functionality of the thing that generates events (i.e. the observable) then we have OCP, that is software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification[^2].
 
 * removes the need for the observable to know how to call its individual observers (other than by the event handling function registered by the observer)
 
 
Cons:

 * does not execute tasks concurrently
 
 * isn't great for loops (UI, game, long running task, etc.) where the time bound of the invoked function(s) is unknown or susceptible to slow down in the calling of dependent functions. In other words, the larger the number of observers for thing "X", the larger the performance bottleneck becomes at thing "X" because thing "X" must now sequentially execute each observers callback individually each time the event they've registered to observe occurs.
 
 * if for any reason the observer dies or stops executing, the observable needs to explicitly handle the exception.
 
 * code is harder to understand due to an inversion of control flow (not to mention the extra boilerplate, especially in Java and C#).
 
 * a lot of boilerplate is required for each implementation such as events, event listener interfaces (containing the observables callback handlers) and the event trigger functionality to iterate over and call all the callbacks. This boilerplate grows linearly with each new event type. Of course, YMMV depending on language.
 
 
Uses:

 * when you have a one to many relationship
 
 The observer pattern is used when:
the change of a state in one object must be reflected in another object without keeping the objects tight coupled.
the framework we are writing needs to be enhanced in future with new observers with minimal changes.
Event management - This is one of the domains where the Observer patterns is extensively used. Swing and .Net are extensively using the Observer pattern for implementing the events mechanism.
 
 
<br/>
<br/>

Now, to do this we have to call some kind of method. We don't want the Observable class to be tightly coupled with the classes that are interested in observing it. It doesn't care who it is as long as it fulfils certain criteria. (Imagine it is a radio station, it doesn't care who is listening as long as they have an FM radio tuned on their frequency). To achieve that we use an interface, referred to as the Observer.















**A Brief Interlude...**

Both the "Plain Old Function Call" and "Observer Pattern" above are synchronous in nature. Synchronicity requires function invocation be done in a _where_ and _when_ fashion.

Rich Hickey explains it nicely:

_If you're architecting a system where this thing deals with the input and then this thing has to do the next part of the job, well if thing "A" calls thing "B", you've just complected it. Now you have a when and where thing because now "A" needs to know where "B" is in order to call "B" and when that happens is whenever "A" does it. Stick a queue in there. Queues are the way to just get rid of this problem. If you're not using queues extensively then you should start, right away, like right after this talk._
-- <cite>[Rich Hickey - Simple Made Easy][4]</cite>

In sytems where you can describe something happening as an "event" (e.g. input has been parsed, button has been pressed, some process has completed, a new data point has arrived, whatever), then synchronous execution may not be good enough. We wouldn't want, for example, our UI to hang and become unresponsive while another piece of code handles the button press event and the actions that entails, nor would we want to wait until a data point has been processed before being able to accept another data point in the stream. In fact, most software would benefit by the reduction in entanglement asynchronous messaging patterns provide. To conclude, **unless the thing that generates the event needs to stop immediately and await the result of some other process that uses that event before being able to continue executing, then you'll benefit from asynchronous messaging** (and if this _is_ the case I would seriously consider refactoring your design).

...which brings us to our asynchronous messaging patterns...
<br/>
<br/>







The Pattern

A queue stores a series of notifications or requests in first-in, first-out order. Sending a notification enqueues the request and returns. The request processor then processes items from the queue at a later time.

Requests can be handled directly, or routed to interested parties. This decouples the sender from the receiver both statically and in time.

When to Use It
If you just want to decouple who receives a message from its sender, patterns like Observer and Command will take care of you with less complexity. You only need a queue when you want to decouple something in time.

I mention this in nearly every chapter, but it’s worth emphasizing. Complexity slows you down, so treat simplicity as a precious resource.
I think of it in terms of pushing and pulling. You have some code A that wants another chunk B to do some work. The natural way for A to initiate that is by pushing the request to B.

Meanwhile, the natural way for B to process that request is by pulling it in at a convenient time in its run cycle. When you have a push model on one end and a pull model on the other, you need a buffer between them. That’s what a queue provides that simpler decoupling patterns don’t.

Queues give control to the code that pulls from it: the receiver can delay processing, aggregate requests, or discard them entirely. But it does this by taking control away from the sender. All it can do is throw a request on the queue and hope for the best. This makes queues a poor fit when the sender needs a response.













#### Message Queue


implemented with actors is a type of message passing system. something about alan kay, something about smalltalk and something about mathematical models proving that message passing is no different to invocation.


Message queues provide an asynchronous communications protocol, meaning that the sender and receiver of the message do not need to interact with the message queue at the same time. Messages placed onto the queue are stored until the recipient retrieves them. Message queues have implicit or explicit limits on the size of data that may be transmitted in a single message and the number of messages that may remain outstanding on the queue.

Many implementations of message queues function internally: within an operating system or within an application. Such queues exist for the purposes of that system only.[1][2][3]






An application may need to notify another that an event has occurred, but does not need to wait for a response.
In publish/subscribe systems, an application "publishes" information for any number of clients to read.
In both of the above examples it would not make sense for the sender of the information to have to wait if, for example, one of the recipients had crashed.

Applications need not be exclusively synchronous or asynchronous. An interactive application may need to respond to certain parts of a request immediately (such as telling a customer that a sales request has been accepted, and handling the promise to draw on inventory), but may queue other parts (such as completing calculation of billing, forwarding data to the central accounting system, and calling on all sorts of other services) to be done some time later.

In all these sorts of situations, having a subsystem which performs message-queuing (or alternatively, a broadcast messaging system) can help improve the behaviour of the overall system.


1. Decoupling heavy-weight processing from a live user request. Essentially a way to say "do this work as soon as possible" when you don't need to block for the answer. One motivation for this may be to allow message processors to run at full tilt (100% cpu utilization or whatever) without impact to the latency sensitive message producer. Note that this only actually makes sense if the cost of sending the message to the queue is lower than the cost of processing it (which is often not true in these days of plentiful cpu).

2. As a buffer to help batch up messages for some kind of bulk processing--say a JDBC batch insert into a database or adding to an HDFS file. In each of these cases adding one message at a time would be very inefficient, so you need a temporary holding place.

3. Dividing up work among multiple worker nodes that feed off the queue as a way to balance work over multiple machines to get a poor man's distributed processing.

You don't need a special purpose queue, though, to accomplish many of these things. You often see people just polling for new records in a database as a simple way to build a queue, and in many cases there is nothing wrong with that. Likewise many other uses can be avoided by just co-locating processing steps and using a simple in-memory queue to buffer in between.

Any time you have a task to do that is not part of the base task the user is having on your website. Here's a few examples of what this could mean:

Picture resize: your user uploads pictures to your website. They need to be resized, or maybe you need to create thumbnails of those to show a preview somewhere. The user does not "care" about this, he has finished uploading his stuff, he should not have to wait for you to process them before having a response. So you queue that task to somewhere else. The result of the resizing task does not impact the response (meaning that on the contrary, validating that a pdf doc does not contain profanity before accepting it, should not be done in a queue because you need that info to generate your response).
Video encoding: if you're building Youtube, you probably are not going to ask your user to wait until his video has been converted from avi to flash / x264 / webM before telling him his file was uploaded correctly.
Sending emails
Search engine indexing: say you're building a CRM, your user modifies the info of a client company. That the info is correctly saved in DB is enough to send a reply to the user. Integrating that new info in your search engine index can happen minutes later without it being an issue.
Pushing a tweet: if your user has set that his account on your app (let's say a blog engine) has to be integrated with his twitter account, if his blog post has been saved and published, it's enough for you to tell him "everything went fine", and then put the tweet to be sent in a message queue so an other server will do that. Here it's no big deal if you run your blogs as single instances, but if this is part of the response generation, then what happens when twitter is down? Can you still save your post? If yes, then the tweet won't be sent... If it's in a message queue, even with twitter down (or you exceeding your twitter api limit), the message will stay there until it can actually be consumed. Also if your blog platform becomes the next tumblr, you'll have so many tweets to send that it's going to become a big deal and you'll need to completely have it separated from the base app.

So any time something is not part of the most basic transaction the user is trying to do, and the result does not impact the response you're sending to the user, there is a potential use case for a message queue!

UPDATE: I didn't see the second part of the question at first. For sending emails in the app we're building, the basic info regarding the email to be sent is saved into DB, then every X amount of time, we check the DB for new records and send emails accordingly. Doing it this way allows us to do some grouping of emails. For example if say a contract has been created for company XYZ, then the services A and B were added to that contract. Then later in the afternoon, B was removed and C was added. Then it went from a "proposal" contract to a "request for quotation". The guy who needs to approve it and decided to receive updates only once a day does not want to receive notifications about each of those steps. So having a DB where you'll store maybe the contract's id will allow you to group these, where on the opposite a message queue usually won't.

UPDATE 2:
Just read an article this morning that tells how it works in the case of Twitter for example.
When you connect to your twitter timeline, the timeline is not built everytime you connect by querying the tweets from people you were following and sorting them by time of tweet as this would not be efficient for response time.
Instead what they do is everytime someone posts a tweet, they will write this tweet to the timelines of all the followers. Timelines are kept in cache and this way they are always pre-built and available without much computing (less efficient on the storage side, more efficient on the computing needs side).

Now if you consider a user with about 1 million followers, that person does not want to wait until twitter has finished saving his tweet 1 million times. Therefore a message queue is used. Like a big pipe sending to different servers that "this tweet just happened, need to update the appropriate timelines now". The process could take half a millisecond or a minute, there would be no difference for the person who sent the tweet. The only person this could make a difference to are the 1 million followers because some of them will have the new tweet pushed to their timelines before others.



<br/>
<br/>




#### Event Bus

Some people like it because it is the embodiment of the Facade or Mediator pattern. It centralizes cross-cutting activities like logging, alerting, monitoring, security, etc.

Some people don't like it because it is often a Singleton point of failure. Everything has to know about it.


I am considering using a In memory Event Bus for my regular java code and my rationale is as follows

Each object in the system can listen to each message, and I think it's bad, it breaks the Encapsulation principle (each object knows about everything)

I am not sure if this is really true, I class needs to register with the event bus to start with, similar to observer pattern, Once a class has registered with the Event Bus, only the methods which have the appropriate signature and annotation are notified.

and Single Responsibility principle (eg when some object needs to a new type of message, event bus often needs to be changed for example to add a new Listener class or a new method in the Listener class).

I totally disagree with

event bus often needs to be changed

The event bus is never changed

I agree with

add a new Listener class or a new method in the Listener class
How does this break SRP ?, I can have a BookEventListener which subscribes to all events pertaining to my Book Entity, and yes I can add methods to this class but still this class is cohesive ...

Why I plan to use it ? It helps me model the "when" of my domain ....

Usually we hear some thing like send a mail "when" book is purchased

we go write down

book.purchase();
sendEmail()
Then we are told add a audit log when a book is purchased , we go to the above snippet

book.purchase();
sendEmail();
**auditBook();**
Right there OCP violated

I Prefer

book.purchase();
EventBus.raiseEvent(bookPurchasedEvent);
Then keep adding handlers as needed Open for Extension Closed for Modification


<br/>
<br/>




[1]:http://en.wikipedia.org/wiki/Message_passing
[2]:http://en.wikipedia.org/wiki/Separation_of_concerns
[3]:http://en.wikipedia.org/wiki/Observer_pattern
[4]:http://www.infoq.com/presentations/Simple-Made-Easy
[5]:http://en.wikipedia.org/wiki/Open/closed_principle
















http://www.marco.panizza.name/dispenseTM/slides/exerc/eventNotifier/eventNotifier.html








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
[^2]: Meyer, Bertrand (1988). Object-Oriented Software Construction. Prentice Hall. ISBN 0-13-629049-3.









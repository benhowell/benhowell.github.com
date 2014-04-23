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
This post in the second in a series on implementing publish/subscribe with <span markdown="span">[Akka][2]</span> and <span markdown="span">[Scala][1]</span>. The first post, <span markdown="span">[Publish/Subscribe using Scala and Akka EventStream]({% post_url 2014-04-18-scala-and-the-akka-eventstream %})</span> covers some facets of Scala and Akka that'll be skipped over in this article, so it may be worth a look if things aren't clear.
</p>
<p>
In <span markdown="span">[Akka][2]</span> based publish/subscribe systems, publishers post messages to an <span markdown="span">[event bus][3]</span>, and subscribers register subscriptions with that <span markdown="span">[event bus][3]</span>. The bus takes care of filtering messages and delivering specific messages to those who have registered an interest in the channel that message is broadcast to. Subscribers can register or deregister their subscriptions for any particular channel(s) at any time.
</p>
<p>
In this article we'll implement an event bus using lookup classifiers with subchannel classification.
</p>
</div> 
<div class="intro-img"><img class="article-image" src="{{ASSET_PATH}}/bootstrap/img/eventbus_subchannel_250.jpg"/></div>
</div>
<br/>
<br/>

#### TL;DR
Just give me the code: [GitHub][4]
<br/>
<br/>



### Subchannel Classification
The [Akka docs][3] describe subchannel classification as follows: 
**_If classifiers form a hierarchy and it is desired that subscription be possible not only at the leaf nodes, this classification may be just the right one._**

If you're familiar with [REST][5] then it's probably easiest explained this way: `/event/42` is a subchannel of `/event`, therefore any subscription to `/event/42` will only receive that event, whereas subscriptions to `/event` will receive all "events". **Note:** I may be referring to events, payloads and/or messages throughout this article and they are to be treated as synonyms.
<br/>
<br/>

Enough of that. Let's build our subchannel classification event bus using [Akka][2] [EventBus][3].

**SCEventBus.scala**
{% highlight scala linenos=table %}
import akka.util.Subclassification
import akka.actor.ActorRef
import akka.event.{EventBus, SubchannelClassification}

object SCEventBus extends EventBus with SubchannelClassification {
  type Event = (String, Any, ActorRef)
  type Classifier = String
  type Subscriber = ActorRef

  override protected def classify(event: Event): Classifier = event._1

  protected def subclassification = new Subclassification[Classifier] {
    def isEqual(x: Classifier, y: Classifier) = x == y
    def isSubclass(x: Classifier, y: Classifier) = x.startsWith(y)
  }

  override protected def publish(event: Event, subscriber: Subscriber): Unit =
    subscriber.tell(event._2, event._3)
}
{% endhighlight %}



#### Please explain.
Here we've created our Subchannel classifying event bus by mixing in the [EventBus][3] traits and the [SubchannelClassification][6] trait. Next (lines 6, 7, 8) we must define the abstract types: 
*	`Event` is the type of all events published on that bus
*	`Subscriber` is the type of subscribers allowed to register on that event bus
*	`Classifier` defines the classifier to be used in selecting subscribers for dispatching events

There's a lot of _magic_ going on in the background which requires this type aliasing. If you're intertested in what's happening behind the scenes, take a look at the Akka EventBus code on [GitHub][7].

On line 6 we are defining our event, which in this case is a tuple with three items consisting of:
* `String` representing the channel the event is to be published to (e.g. `/event/42`)
* `Any` representing the actual payload to be published
* `ActorRef` representing the sender of the event (EventBus does not preserve the publisher of events)

On line 10 we're simply defining how our messages will be classified, and in this case, we're supplying the first item of the event tuple `event._1`, which as explained above is our channel parameter.

On line 12 we've got a simple function that determines how to classify sub events.

Finally, on line 17 we have the `publish` function.

{% highlight scala %}
override protected def publish(event: Event, subscriber: Subscriber): Unit =
    subscriber.tell(event._2, event._3)
{% endhighlight %}

`publish` takes an `Event` (our `(String, Any, ActorRef)` tuple and a `Subscriber` (our `ActorRef` who's subscribed to the channel the event is being published to). **Note:** _Magic_. The `Subscriber` parameter is supplied to the `publish` function in the background (meaning that you don't supply it in your call to `publish`), which will become apparent a bit later on in the article. Again, If you're intertested in the _magic_, take a look at the Akka EventBus code on [GitHub][7]. Therefore `publish` must be being called ( _magic_ ) for every subscriber listening to the channel the event is being published to.

Now, we simply send our event stuff to the subscriber using the `tell`[^1] function which means "fire and forget" sending a message asynchronously and returns immediately.[^2]

The last thing to mention here is that our choice to use `tell` rather than `!` is a deliberate one[^1].
<br/>
<br/>

Now, let's create a file containing some helper functions to build our actors.

**Actors.scala**
{% highlight scala linenos=table %}
import akka.actor.{Actor, ActorRef, Props, ActorSystem}

sealed class Subscription(f: (Any, Subscription, ActorRef) => Unit) extends Actor {
  override def receive = { case (payload: Any) => f(payload, this, sender) }
}

object Actors {

  val system = ActorSystem()

  def create(actorType: Class[_], name: String, args: AnyRef): ActorRef = {
    val props = Props(actorType, args)
    val actor = system.actorOf(props, name = name)
    actor
  }

  // receive handlers
  def onReceive = (payload: Any, receiver: Any, sender: ActorRef) => {
    println(s"$sender -> $receiver: $payload")
  }

  def onReceive(log: (String) => Unit) =
    (payload: Any, receiver: Any, sender: ActorRef) => {
      log(s"$sender -> $receiver: $payload")
      println(s"$sender -> $receiver: $payload")
  }
}
{% endhighlight %}


#### Please explain.




**Logger.scala**
{% highlight scala linenos=table %}
import java.io.{PrintStream, IOException}

object Logger {

  val log = (ps: PrintStream, msg: String) => {
    try {
      ps.println(msg)
    }
    catch {
      case ioe: IOException => println("IOException: " + ioe.toString)
      case e: IOException => println("Exception: " + e.toString)
    }
  }

  def stop(ps: PrintStream) = ps.close()
}
{% endhighlight %}


#### Please explain.




**Main.scala**
{% highlight scala linenos=table %}
import java.io.{File, FileOutputStream, PrintStream}

object Main extends App{

  // set up logger
  val ps = new PrintStream(new FileOutputStream(new File("output.txt")))
  val log = Logger.log(ps, _: String)

  // create subscribers
  val rootSubscriber = Actors.create(
    classOf[Subscription], "rootSubscriber", Actors.onReceive)

  val eventSubscriber = Actors.create(
    classOf[Subscription], "eventSubscriber", Actors.onReceive)

  val itemSubscriber = Actors.create(
    classOf[Subscription], "itemSubscriber", Actors.onReceive(log))
  
  // set up subscriptions
  SCEventBus.subscribe(rootSubscriber, "/")
  SCEventBus.subscribe(eventSubscriber, "/event")
  SCEventBus.subscribe(itemSubscriber, "/event/42")

  // create event publisher
  val eventPublisher = Actors.create(
    classOf[Subscription], "eventPublisher", Actors.onReceive)

  // generate some events
  SCEventBus.publish(("/", "payload A", eventPublisher))
  SCEventBus.publish(("/event", "payload B", eventPublisher))
  SCEventBus.publish(("/event/42", "payload C", eventPublisher))

  // clean up
  Logger.stop(ps)
}
{% endhighlight %}


#### Please explain.















<br />
<br />
<br />

#### Notes
[^1]: You'll often see `tell` represented in an alternative form, namely `!`. e.g. `subscriber ! event.payload`. **Note:** _Magic_. Normally, when the information is available, the `!` call silently sends a second parameter containing the `ActorRef` of the `Actor` sending the event, along with the payload. e.g `subscriber ! event.payload` is the same as `subscriber.tell(event.payload, sender)`, **however**, [EventBus][3] does not preserve the sender of the published messages. For this reason we are explicitly wrapping our sender in our event and then using the explicit `tell` call. 
[^2]: Another function call: `ask` (rather than tell) sends a message asynchronously and returns a Future representing a possible reply. Ask can also be represented as `?`. e.g. `subscriber ? event.payload`.






[1]:http://www.scala-lang.org/
[2]:http://akka.io/
[3]:http://doc.akka.io/docs/akka/snapshot/scala/event-bus.html
[4]:https://github.com/benhowell/examples/tree/master/AkkaEventBus
[5]:http://en.wikipedia.org/wiki/Representational_state_transfer
[6]:http://doc.akka.io/api/akka/snapshot/index.html#akka.event.SubchannelClassification
[7]:https://github.com/akka/akka/blob/master/akka-actor/src/main/scala/akka/event/EventBus.scala




















In an upcoming article, I will demonstrate the publish/subscribe pattern using the Akka EventBus andLookupClassification


http://www.razko.nl/blog/2013/10/10/using-akka-event-bus/
Subchannel classification

The subchannel specification is a EventBus which supports a hierachy of channels or classifiers. This first example focuses on a simple string based approach which allows to listen to a top level channel and their leafs.

The example below shows subcribers and publishers which become increasingly more specific, and that previous subscribers which are higher up the hierachy will catch the upcoming more specific publishers.

Downfalls

The sender is not preserved when receiving a message which passed through a EventBus, to solve this you need to pass the ActorRef manually





Pub Sub is more simple to manage in many ways – something publishes, something subscribes, and that’s all there is to it. However, it breaks the idea of encapsulation really thoroughly. Event Emitter on the other hand has a higher barrier to entry – you have to “know” about the thing you want to listen to events on, but it does keep things much more contained with known boundaries.

The question then is, what are you building? If you’re sticking very strictly to something like Nikolas Zakas’ sandboxed model (which seems to be a very nice fit for the Yahoo homepage, but not every kind of web page) then you can stick to Event Emitter and you don’t need to worry about much else. If you’re building a complex, rich, interconnected UI you’re almost certainly going to get some benefit from using Pub Sub.

I would also suggest that a lot of things that happen “globally” could be done very nicely with Pub Sub. Take logging for example – whether you’re using console.log or other global logging method, it might make much more sense to publish on a logging topic – and allow anything that wants to subscribe to it, whether that’s a user notification system, or for debugging, or whatever. Another example might be network activity – by publishing individual network events globally, it becomes much easier to manage user notification (for example with a single compound spinner / loading bar).





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
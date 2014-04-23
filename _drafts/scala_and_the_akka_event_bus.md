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

If you're familiar with [REST][5] then it's probably easiest explained this way: `/event/42` is a subchannel of `/event`, therefore any subscription to `/event/42` will only receive that event, whereas subscriptions to `/event` will receive all "events".
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
[^1]: Decoupling as far as space and time is concerned. Publish/Subscribe introduces a different type of coupling, namely: semantic coupling.

[1]:http://www.scala-lang.org/
[2]:http://akka.io/
[3]:http://doc.akka.io/docs/akka/snapshot/scala/event-bus.html
[4]:https://github.com/benhowell/examples/tree/master/AkkaEventBus
[5]:http://en.wikipedia.org/wiki/Representational_state_transfer





















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
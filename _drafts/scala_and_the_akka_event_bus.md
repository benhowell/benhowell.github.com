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
<br/>

#### Please explain.
Here we've created our Subchannel classifying event bus by mixing in the [EventBus][3] traits and the [SubchannelClassification][6] trait. Next (lines 6, 7, 8) we must define the abstract types:

 * `Event` is the type of all events published on that bus
 * `Subscriber` is the type of subscribers allowed to register on that event bus
 * `Classifier` defines the classifier to be used in selecting subscribers for dispatching events
<p>
There's a lot of _magic_ going on in the background which requires this type aliasing. If you're intertested in what's happening behind the scenes, take a look at the Akka EventBus code on [GitHub][7].
</p>
<p>
On line 6 we are defining our event, which in this case is a tuple with three items consisting of:

 * `String` representing the channel the event is to be published to (e.g. `/event/42`)
 * `Any` representing the actual payload to be published
 * `ActorRef` representing the sender of the event (EventBus does not preserve the publisher of events)
</p>
<p>
On line 10 we're simply defining how our messages will be classified, and in this case, we're supplying the first item of the event tuple `event._1`, which as explained above is our channel parameter.
</p>
<p>
On line 12 we've got a simple function that determines how to classify sub events.
</p>
<p>
Finally, on line 17 we have the `publish` function.
</p>
{% highlight scala %}
override protected def publish(event: Event, subscriber: Subscriber): Unit =
    subscriber.tell(event._2, event._3)
{% endhighlight %}

`publish` takes our `(String, Any, ActorRef)` tuple as `Event` and a `Subscriber` (our `ActorRef` who's subscribed to the channel the event is being published to)[^3].

Now, we simply send our event payload and sender (2nd and 3rd tuple parameter) to the subscriber using the `tell`[^1] function which enacts a "fire and forget" strategy of sending a message asynchronously and returning immediately[^2].

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
<br/>
<br/>

#### Please explain.
On line 3 we define our `Subscription` actor who will be able to subscribe to and publish events on the event bus. `Subscription`. Subscription takes a function `f: (Any, Subscription, ActorRef)` as a parameter argument and retuns `Unit` (equivalent to Java void). Within our `Subscriber` we need to override the `receive` function from `Actor` to tell our subscription to execute the function passed in at contruction time when receiving a message matching `(payload: Any)`. 

Next, on line 9, we create an `ActorSystem` which is used to supervise top level actors. Only one of these can be built per application.

On line 11 we define a function to create our actors. The first parameter is the `Class[_]` parameter defining the class type that implements the actor we wish to construct. This particular function implementation is a generic function which has other potential uses outside this example which is why we have simply used the "place holder" `_` in our Class[] parameter as we don't wish to hardcode it into the function definition. In our case we will be passing the `Subscription` type, i.e. `ClassOf[Subscription]` to this function. The next parameter, `name` is simply the name of the actor/subscriber and `args` contains the arguments needed by the constructor that implements the actor we create (e.g. the construction arguments needed by `Subscription` - a function with the signature `f: (Any, Subscription, ActorRef)`. This function then goes about constructing the actor in the [usual way][8].

A final thing of note here is line 14. In scala, the return keyword is redundant as whatever is on the last line of the function is returned.

Finally, on lines 18 and 22, we define our various receive functions that'll be used by subscribers (remember, we inject these into `Subscription` upon creation, which we will see an example of soon).



Now we'll create a simple logger to complement our demo.

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
<br/>
<br/>


All that's left to do is write a test program to demonstrate our subchannel classification publish/subscribe system.

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

Not much to tell here other than a few cursory notes.

On line 7 you'll notice a call to `Logger.log(ps, _: String)`. We have passed a placeholder `_` as our second parameter to this function creating what's called a "partially applied function". This function basically takes the parameters we have supplied and returns a new function that only takes the parameters we have omitted. Now the `log` val contains a function that takes a `String` only. 

Lines 20, 21, 22 are calls to publish that take a `(channel, payload, sender)` tuple as it's only parameter[^3]. 
<br />
<br />

That's it for now.
<br />
<br />
<br />

#### Notes
[^1]: You'll often see `tell` represented in an alternative form, namely `!`. e.g. `subscriber ! event.payload`. **Note:** _Magic_. Normally, when the information is available, the `!` call silently sends a second parameter containing the `ActorRef` of the `Actor` sending the event, along with the payload. e.g `subscriber ! event.payload` is the same as `subscriber.tell(event.payload, sender)`, **however**, [EventBus][3] does not preserve the sender of the published messages. For this reason we are explicitly wrapping our sender in our event and then using the explicit `tell` call. 

[^2]: Another function call: `ask` (rather than tell) sends a message asynchronously and returns a Future representing a possible reply. Ask can also be represented as `?`. e.g. `subscriber ? event.payload`.

[^3]: **Note:** _Magic_. The `Subscriber` parameter is supplied to the `publish` function in the background (meaning that you don't supply it in your call to `publish`), which will become apparent a bit later on in the article. Again, If you're intertested in the _magic_, take a look at the Akka EventBus code on [GitHub][7]. Therefore `publish` must be being called ( _magic_ ) for every subscriber listening to the channel the event is being published to.


[1]:http://www.scala-lang.org/
[2]:http://akka.io/
[3]:http://doc.akka.io/docs/akka/snapshot/scala/event-bus.html
[4]:https://github.com/benhowell/examples/tree/master/AkkaEventBus
[5]:http://en.wikipedia.org/wiki/Representational_state_transfer
[6]:http://doc.akka.io/api/akka/snapshot/index.html#akka.event.SubchannelClassification
[7]:https://github.com/akka/akka/blob/master/akka-actor/src/main/scala/akka/event/EventBus.scala
[8]:http://doc.akka.io/docs/akka/snapshot/scala/actors.html

---
layout: post
category : examples
title: Publish/Subscribe using Scala and Akka EventStream
tagline: "guide"
tags : [akka, EventSystem, scala, concurrent, asynchronous, publish/subscribe, beginner, example, tutorial, guide]
article_img: bootstrap/img/eventbus_250.jpg
article_img_title: "Event bus by Anonymous"
---
{% include JB/setup %}
<div class="intro">
<div class="intro-txt">
<p>
Hello, this is my first post. Hurrah!
</p>
<p>
Publish/Subscribe (aka: pub/sub) is a messaging pattern that helps decouple<span markdown="span">[^1]</span> the sender and receiver of messages. Generally, this pattern is used across network(s) and provides ease of scalability and dynamic network topologies. 
</p>
<p>
The publish/subscribe pattern can also be used within applications to provide scalability as an alternative to the more traditional Observable/Observer pattern as it offers some distinct advantages which I will address in a future article.
</p>
<p>
In the next few articles we'll look at some using <span markdown="span">[Scala][1] and [Akka][2]</span>, such as this: <span markdown="span">[Publish/Subscribe using Scala and Akka EventBus]({% post_url 2014-04-23-scala_and_the_akka_event_bus %})</span>
</p>
</div>
<div class="intro-img-border">
<div class="intro-img-bevel">
<div class="intro-img">
<img class="article-image" title="{{page.article_img_title}}" src="{{ASSET_PATH}}/{{page.article_img}}"/>
</div>
</div>
</div>
</div>
<br />
<br />
<br />


#### TL;DR
Just give me the code: [GitHub][4]
<br/>
<br/>

### EventStream
Let's build our first example using the [Akka][2] main event bus: [EventStream][3][^2].
<br />

**EventStream.scala**
{% highlight scala linenos=table %}
import akka.actor.{Actor, Props, ActorSystem}

sealed class Subscriber(f: (String, Any) => Unit) extends Actor {
  override def receive = { case (topic: String, payload: Any) => f(topic, payload) }
}

object EventStream{

  // ActorSystem is a heavy object: create only one per application
  // http://doc.example.io/docs/example/snapshot/scala/actors.html
  val system = ActorSystem("actorsystem")

  def subscribe(f: (String, Any) => Option[Unit], name: String) = {
    val props = Props(classOf[Subscriber], f)
    val subscriber = system.actorOf(props, name = name)
    system.eventStream.subscribe(subscriber, classOf[(String, Any)])
  }

  def publish(topic: String, payload: Any) {
    system.eventStream.publish(topic, payload)
  }
}
{% endhighlight %}

Congratulations, you have now created your entire publish/subscribe infrastructure!
<br />
<br />

#### Please explain.
First we create our `Subscriber` who will listen for messages on the event stream bus. This [Actor][5] will act on any messages matching the class `(String, Any)`, which is simply a tuple of `String` and `Any`[^3], however you can create any class of message you desire[^4]. Within our `Subscriber` we need to override the `receive` function from `Actor` to tell our subscriber what to do when receiving a message matching the `(String, Any)` class. Upon construction of our subscriber we pass in the function `f: (String, Any) => Option[Unit]` which will be executed by the receive function each time a new message is received. The `sealed` keyword means that this class can only be referred to within the file it is declared in, in this case, only EventStream.scala. One last thing worth mentioning here is that in the [Scala][1] language, `object` declares a singleton. 

{% highlight scala linenos=table %}
sealed class Subscriber(f: (String, Any) => Unit) extends Actor {
  override def receive = { case (topic: String, payload: Any) => f(topic, payload) }
}
{% endhighlight %}
<br/>

Next, we create an `ActorSystem` which is used to supervise top level actors. As the comments suggest, only one of these can be built per application.
<br/>

{% highlight scala linenos=table %}
// ActorSystem is a heavy object: create only one per application
// http://doc.example.io/docs/example/snapshot/scala/actors.html
val system = ActorSystem("actorsystem")
{% endhighlight %}
<br/>

Now we define our subscribe function which takes a function `f: (String, Any) => Option[Unit]` and a `name` to represent the entity creating the subscription. Create our `props` [Props][6] object which is merely a configuration class for the creation of Actors. Our `props` constructor takes the `Subscriber` type class we defined earlier, plus the function argument `f` which we in turn use to create our subscriber. Finally, we register this subscriber with the `system.eventStream` and start listening for messages. 

{% highlight scala linenos=table %}
def subscribe(f: (String, Any) => Option[Unit], name: String) = {
  val props = Props(classOf[Subscriber], f)
  val subscriber = system.actorOf(props, name = name)
  system.eventStream.subscribe(subscriber, classOf[(String, Any)])
}
{% endhighlight linenos=table %}

Our work here is done.
<br/>
<br/>

Now to demo the system, we'll create a couple of simple subscribers.

**Foo.scala**
{% highlight scala linenos=table %}
object Foo {
  val onEvent = (topic: String, payload: Any) => Some(topic) collect {
    case "topic A" => println("Foo received: topic = " + topic + ", payload = " + payload)
  }
}
{% endhighlight %}
<br/>

**Bar.scala**
{% highlight scala linenos=table %}
object Bar {
  val onEvent = (topic: String, payload: Any) => Some(topic) collect {
    case "topic B" =>
      println("Bar received: topic = " + topic + " payload = " + payload)
      EventStream.publish("topic C", "payload C")
  }
}
{% endhighlight %}
<br/>

#### Please explain.
In Foo.scala and Bar.scala above, we've declared the function `(topic: String, payload: Any) => Some(topic)` and assigned it to a val[^5]. We are defining the return type `Some(topic)` which represents any valid topic (i.e. is not `None`), and the `collect` pattern match which will only return a result where `topic` matches one of the following `case`s. Note: `Some(value)` and `None` are the two possible return types for the `Option` monad. 
<br/>
<br/>

Lastly, we'll finish off with a subscriber that requires extra parameters for its `onEvent` function.

**Logger.scala**
{% highlight scala linenos=table %}
import java.io._

object Logger {

  def onEvent(ps: PrintStream) = (topic: String, payload: Any) => Option[Unit] {
      try {
        println("Logger received: topic = " + topic + ", payload = " + payload)
        ps.println("Logger [" + topic + "] [" + payload + "]")
      }
      catch {
        case ioe: IOException => println("IOException: " + ioe.toString)
        case e: IOException => println("Exception: " + e.toString)
      }
  }

  def stop(ps: PrintStream) = ps.close()
}
{% endhighlight %}

To do this, we will define a function that takes the extra parameter(s), in this case `(ps: PrintStream)` which itself returns a function that [closes over][10] the `ps` parameter with signature `(topic: String, payload: Any) => Option[Unit]`.
<br/>
<br/>

All that's left to do is write a test program to demonstrate our publish/subscribe system.

{% highlight scala linenos=table %}
import java.io.{File, FileOutputStream, PrintStream}

object Main {

  val ps = new PrintStream(new FileOutputStream(new File("output.txt")))

  def main(args: Array[String]) {

    // setup subscriptions
    EventStream.subscribe(Foo.onEvent, "foosubscriber")
    EventStream.subscribe(Bar.onEvent, "barsubscriber")
    EventStream.subscribe(Logger.onEvent(ps), "loggersubscriber")

    run
  }

  def run {
    EventStream.publish("topic A", "payload A")
    EventStream.publish("topic B", "payload B")
    stop
  }

  def stop = Logger.stop(ps)

}
{% endhighlight %}

That's all for now. 
In an upcoming article, I will demonstrate the publish/subscribe pattern using the [Akka][2] [EventBus][11] and `LookupClassification`. Edit, the article in question is now up: [Publish/Subscribe using Scala and Akka EventBus]({% post_url 2014-04-23-scala_and_the_akka_event_bus %})
<br/>
<br/>
<br/>

#### Notes
[^1]: Decoupling as far as space and time is concerned. Publish/Subscribe introduces a different type of coupling, namely: semantic coupling.

[^2]: EventStream is NOT a distributed solution and is only intended to be implemented within a single application.

[^3]: Scala type `Any` is roughly equivalent to Java Object, that is, the root type that all others derive from.

[^4]: e.g. `Subscriber(f: Message => Unit)` where `class Message(topic: String, payload: Any)` however, wrapping plain values this way is bad practice and should be avoided.

[^5]: We could also design our system to do full pattern matching, however each `onEvent` type function would need to return a specific type (i.e. `Unit`) and explicitly deal with the default case where no pattern could be matched.
<br/>


[1]:http://www.scala-lang.org/
[2]:http://akka.io/
[3]:http://doc.akka.io/docs/akka/snapshot/scala/event-bus.html#event-stream
[4]:https://github.com/benhowell/AkkaEventStreamExample
[5]:http://doc.akka.io/docs/akka/snapshot/scala/actors.html
[6]:http://doc.akka.io/docs/akka/snapshot/scala/actors.html#props
[7]:http://www.haskell.org/haskellwiki/Haskell
[8]:http://www.haskell.org/haskellwiki/Maybe
[9]:http://www.scala-lang.org/api/2.10.4/index.html#scala.Option
[10]:http://en.wikipedia.org/wiki/Closure_(computer_programming)
[11]:http://doc.akka.io/docs/akka/snapshot/scala/event-bus.html
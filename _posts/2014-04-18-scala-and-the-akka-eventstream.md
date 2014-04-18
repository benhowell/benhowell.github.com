---
layout: post
category : examples
title: Publish/Subscribe using Scala and Akka EventStream
tagline: "Example"
tags : [akka, EventSystem, scala, concurrent, asynchronous, Publish/Subscribe, beginner, example, tutorial]
---
{% include JB/setup %}

Hello, this is my first post. Hurrah!
<br/>

Publish/Subscribe (aka: pub/sub) is a messaging pattern that helps decouple{1} the sender and receiver of messages. Generally, this pattern is used across network(s) and provides ease of scalability and dynamic network topologies. The publish/subscribe pattern can also be used within applications to provide scalability as an alternative to the more traditional Observable/Observer pattern as it offers some distinct advantages which I will address in a future article.
<br />
<br />

In the next few articles we'll look at some alternative implementations using [Scala][1] and [Akka][2]
<br />
<br />

### EventStream
Let's build our first example using the Akka main event bus: [EventStream][3]{2}.
<br />

**EventStream.scala**
{% highlight scala %}
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

Congratulations, you have now created your entire publish/subscribe infrasturcture!
<br />
<br />

##### Please explain.
First we create our `Subscriber` who will listen for messages on the event stream bus. This [Actor][5] will act on any messages matching the class `(String, Any)`, which is simply a tuple of `String` and `Any`, however you can create any class of message you desire{3}. Within our `Subscriber` we need to override the `receive` function from `Actor` to tell our subscriber what to do when receiving a message matching the `(String, Any)` class. Upon construction of our subscriber we pass in the function `f: (String, Any) => Option[Unit]` which will be executed by the receive function each time a new message is received. The `sealed` keyword means that this class can only be referred to within the file it is declared in, in this case, only EventStream.scala. One last thing worth mentioning here is that in the [Scala][1] language, `object` declares a singleton. 

{% highlight scala %}
sealed class Subscriber(f: (String, Any) => Unit) extends Actor {
  override def receive = { case (topic: String, payload: Any) => f(topic, payload) }
}
{% endhighlight %}
<br/>

Next, we create an `ActorSystem` which is used to supervise top level actors. As the comments suggest, only one of these can be built per application.
<br/>

{% highlight scala %}
// ActorSystem is a heavy object: create only one per application
// http://doc.example.io/docs/example/snapshot/scala/actors.html
val system = ActorSystem("actorsystem")
{% endhighlight %}
<br/>

Now we define our subscribe function which takes a function `f: (String, Any) => Option[Unit]` and a `name` to represent the entity creating the subscription. Note that we are using the [Option][9] monad as a return type here (equivalent to [Haskell][7] [Maybe][8]). Create our `props` [Props][6] object which is merely a configuration class for the creation of Actors. Our `props` constructor takes an instance of the `Subscriber` class we defined earlier, plus the function argument `f` which we in turn use to create our subscriber. Finally, we register this subscriber with the `system.eventStream` and start listening for messages. 

{% highlight scala %}
def subscribe(f: (String, Any) => Option[Unit], name: String) = {
  val props = Props(classOf[Subscriber], f)
  val subscriber = system.actorOf(props, name = name)
  system.eventStream.subscribe(subscriber, classOf[(String, Any)])
}
{% endhighlight %}

Our work here is done.
<br/>
<br/>

Now to demo the system, we'll create a couple of simple subscribers.

**Foo.scala**
{% highlight scala %}
object Foo {
  val onEvent = (topic: String, payload: Any) => Some(topic) collect {
    case "topic A" => println("Foo received: topic = " + topic + ", payload = " + payload)
  }
}
{% endhighlight %}
<br/>

**Bar.scala**
{% highlight scala %}
object Bar {
  val onEvent = (topic: String, payload: Any) => Some(topic) collect {
    case "topic B" =>
      println("Bar received: topic = " + topic + " payload = " + payload)
      EventStream.publish("topic C", "payload C")
  }
}
{% endhighlight %}
<br/>
<br />

##### Please explain.
In Foo and Bar above, we've declared the function `(topic: String, payload: Any) => Some(topic)` and assigned it to a val{4}. We are defining the return type `Some(topic)` which represents any valid topic (i.e. is not `None`), and the `collect` pattern match which will only return a result where `topic` matches one of the following `case`s. Note: `Some(value)` and `None` are the two possible return types for the `Option` monad. 




and lastly, we'll finish off with a subscriber that requires extra parameters.

**Logger.scala**
{% highlight scala %}
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

To do this, we will define a function that takes the extra parameter(s), in this case `(ps: PrintStream)` which itself ecloses and returns the function with signature `(topic: String, payload: Any) => Option[Unit]` satisfying 
















{% highlight scala %}
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



### TL;DR
Full example code is available on my [GitHub][4]

#### Notes
{1}: Decoupling as far as space and time is concerned. Publish/Subscribe introduces a different type of coupling, namely: semantic coupling.

{2}: EventStream is NOT a distributed solution and is only intended to be implemented within a single application.

{3}: e.g. `Subscriber(f: Message => Unit)` where `class Message(topic: String, payload: Any)` however, wrapping plain values this way is bad practice and should be avoided.

{4}: We could also design our system to do full pattern matching, however each `onEvent` type function would need to return a specific type (i.e. `Unit`) and explicitly deal with the default case where no pattern could be matched. For example:

{% highlight scala %}
val onEvent = (topic: String, payload: Any) => topic match {
  case "Hello" => println("Goodbye")
  case _ => println("no match")
}
{% endhighlight %}
<br/>
<br />


[1]:http://www.scala-lang.org/
[2]:http://akka.io/
[3]:http://doc.akka.io/docs/akka/snapshot/scala/event-bus.html#event-stream
[4]:https://github.com/benhowell/examples/tree/master/AkkaEventStream
[5]:http://doc.akka.io/docs/akka/snapshot/scala/actors.html
[6]:http://doc.akka.io/docs/akka/snapshot/scala/actors.html#props
[7]:http://www.haskell.org/haskellwiki/Haskell
[8]:http://www.haskell.org/haskellwiki/Maybe
[9]:http://www.scala-lang.org/api/2.10.4/index.html#scala.Option
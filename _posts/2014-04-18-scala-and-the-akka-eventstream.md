---
layout: post
category : examples
title: Publish/Subscribe using Akka and Scala 
tagline: "Example"
tags : [akka, EventSystem, scala, concurrent, asynchronous, Publish/Subscribe]
---
{% include JB/setup %}

Hello, this is my first post. Hurrah!

##Publish/Subscribe
Publish/Subscribe (aka: pub/sub) is a messaging pattern that helps decouple{1} the sender and receiver of messages. Generally, this pattern is used across network(s) and provides ease of scalability and dynamic network topologies. The pub/sub pattern can also be used within applications as an alternative to the more traditional Observable/Observer pattern as it offers some advantages which I will address in a future article.
<br />
<br />

Let's look at a few alternative implementations using [Scala][1] and [Akka][2]
<br />
<br />

###EventStream
Let's build our first example using the Akka main event bus: [EventStream][3].

*_EventStream.scala_*
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

Congratulations, you have now created your publish/subscribe backbone!

#####Break it down.

{% highlight scala %}
sealed class Subscriber(f: (String, Any) => Unit) extends Actor {
  override def receive = { case (topic: String, payload: Any) => f(topic, payload) }
}
{% endhighlight %}






{% highlight scala %}
object Foo {
  val onEvent = (topic: String, payload: Any) => Some(topic) collect {
    case "topic A" => println("Foo received: topic = " + topic + ", payload = " + payload)
  }
}
{% endhighlight %}


{% highlight scala %}
object Bar {
  val onEvent = (topic: String, payload: Any) => Some(topic) collect {
    case "topic B" =>
      println("Bar received: topic = " + topic + " payload = " + payload)
      EventStream.publish("topic C", "payload C")
  }
}
{% endhighlight %}


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



###TL;DR
Full example code is available on my [GitHub][4]

####Notes
{1}: Decoupling as far as space and time is concerned. Publish/Subscribe introduces a different type of coupling, namely: semantic coupling.



[1]:http://www.scala-lang.org/
[2]:http://akka.io/
[3]:http://doc.akka.io/docs/akka/snapshot/java/event-bus.html#event-stream
[4]:https://github.com/benhowell/examples/tree/master/AkkaEventStream

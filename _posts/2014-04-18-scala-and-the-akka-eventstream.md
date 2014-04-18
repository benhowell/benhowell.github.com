---
layout: post
category : examples
title: Publish/Subscribe using Akka and Scala 
tagline: "Example"
tags : [akka, EventSystem, scala, concurrent, asynchronous]
---
{% include JB/setup %}

Hello, this is my first post. Hurrah!

###Publish/Subscribe
Publish–subscribe is a messaging pattern that helps decouple[1]({% post_url 2014-04-18-scala-and-the-akka-event-stream.md %}) the sender and receiver of messages. 

, called publishers, do not program the messages to be sent directly to specific receivers, called subscribers. Instead, published messages are characterized into classes, without knowledge of what, if any, subscribers there may be. Similarly, subscribers express interest in one or more classes, and only receive messages that are of interest, without knowledge of what, if any, publishers there are.



####Notes
[1]:http://www.infoq.com/presentations/Simple-Made-Easy




Pub/sub is a sibling of the message queue paradigm, and is typically one part of a larger message-oriented middleware system. Most messaging systems support both the pub/sub and message queue models in their API, e.g. Java Message Service (JMS).

This pattern provides greater network scalability and a more dynamic network topology.


http://doc.akka.io/docs/akka/snapshot/java/event-bus.html#event-stream

Here is some code!

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







Congratulations, you have now created your entire EventSystem!



##TL;DR
Full example code is available here: https://github.com/benhowell/examples/tree/master/AkkaEventStream


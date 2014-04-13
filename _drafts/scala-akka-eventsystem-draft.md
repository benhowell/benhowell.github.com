---
layout: post
category : example
tagline: "Example"
tags : [akka, EventSystem, scala, concurrent, asynchronous]
---
{% include JB/setup %}


Hello, this is my first post. Hurrah! Now write something about Akka, Scala and making a little publish/subscribe, concurrent, asynchronous event bus oriented program ;-)




http://www.infoq.com/presentations/Simple-Made-Easy

abstraction
- who, what, when, where, why, how
- i don't know, i don't want to know
00:54:50
when and where
strenuously avoid complecting these with anything in the design

when and where you have to avoid complecting this with anything
mostly when people design systems with directly connected objects
if you're architecting a system where this thing deals with the input and then this thing has to do the next part of the job. well if thing "A" calls thing "B", you've just complected it.
now you have a when and where thing. cause now "A" needs to know where "B" is in order to call "B" and when that happens is whenever "A" does it. Stick a queue in there. Queues are the way to just get rid of this problem. If you're not using queues extensively then you should start, right away, like right after this talk.






wiki says:
In software architecture, publish–subscribe is a messaging pattern where senders of messages, called publishers, do not program the messages to be sent directly to specific receivers, called subscribers. Instead, published messages are characterized into classes, without knowledge of what, if any, subscribers there may be. Similarly, subscribers express interest in one or more classes, and only receive messages that are of interest, without knowledge of what, if any, publishers there are.

Pub/sub is a sibling of the message queue paradigm, and is typically one part of a larger message-oriented middleware system. Most messaging systems support both the pub/sub and message queue models in their API, e.g. Java Message Service (JMS).

This pattern provides greater network scalability and a more dynamic network topology.


Here is some code!

{% highlight scala %}
sealed class Subscriber(f: (String, Any) => Unit) extends Actor {
  override def receive = { case (topic: String, payload: Any) => f(topic, payload) }
}
{% endhighlight %}

Here is more code!

{% highlight scala %}
object EventStream{

  // ActorSystem is a heavy object: create only one per application
  // http://doc.akka.io/docs/akka/snapshot/scala/actors.html
  val system = ActorSystem("actorsystem")

  def subscribe(f: (String, Any) => Unit, name: String) = {
    val props = Props(classOf[Subscriber], f)
    val subscriber = system.actorOf(props, name = name)
    system.eventStream.subscribe(subscriber, classOf[(String, Any)])
  }

  def publish(topic: String, payload: Any) {
    system.eventStream.publish(topic, payload)
  }
}
{% endhighlight %}

Congratulations, you have now created your entire EventSystem!
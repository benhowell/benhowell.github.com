---
layout: post
category : example
tagline: "Example"
tags : [akka, EventSystem, scala, concurrent, asynchronous, publish, subscribe]
---
{% include JB/setup %}


Hello, this is my first post. Hurrah! Now write something about Akka, Scala and making a little publish/subscribe, concurrent, asynchronous event bus oriented program ;-)

wiki says:
In software architecture, publish–subscribe is a messaging pattern where senders of messages, called publishers, do not program the messages to be sent directly to specific receivers, called subscribers. Instead, published messages are characterized into classes, without knowledge of what, if any, subscribers there may be. Similarly, subscribers express interest in one or more classes, and only receive messages that are of interest, without knowledge of what, if any, publishers there are.

Pub/sub is a sibling of the message queue paradigm, and is typically one part of a larger message-oriented middleware system. Most messaging systems support both the pub/sub and message queue models in their API, e.g. Java Message Service (JMS).

This pattern provides greater network scalability and a more dynamic network topology.


Here is some code!

```scala
final case class Message(topic: String, payload: Any)
```

Here is some more code!

```scala
class Subscriber(f: Message => Unit) extends Actor {
  override def receive = { case msg: Message => f(msg) }
}
```

Here is yet more code!

```scala
object EventStream{

  // ActorSystem is a heavy object: create only one per application
  // http://doc.akka.io/docs/akka/snapshot/scala/actors.html
  val system = ActorSystem("actorsystem")

  def subscribe(f: Message => Unit, name: String) = {
    val props = Props(classOf[Subscriber], f)
    val subscriber = system.actorOf(props, name = name)
    system.eventStream.subscribe(subscriber, classOf[Message])
  }

  def publish(msg: Message) {
    system.eventStream.publish(msg)
  }
}
```

Congratulations, you have now created your entire EventSystem!
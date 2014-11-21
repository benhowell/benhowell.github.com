---
layout: post
title: "Multithreaded Javascript using Nashorn"
description: "A comprehensive walkthough guide teaching how to develop a performant multithreaded system in Javascript using the Nashorn engine"
tagline: "guide"
category: guide
tags: [nashorn, javascript, jsr-223, multithreaded, beginner, example, tutorial, guide]
article_img: bootstrap/img/garlic_braid.jpg
article_img_title: Multithreaded Garlic
article_img_alt: "An image depicting a multithreaded garlic plait for an article about multithreaded javascript"
---
{% include JB/setup %}
<div class="intro">
<div class="intro-txt">
<p>
Javascript has no native threading of its own, but it is possible to craft performant multithreaded code using Java threads inside javascript, in fact this has been possible since Java 6 with the initial release of the javax.scripting.ScriptEngine supporting the Rhino Javascript engine. 
</p>
<p>
In this article, we'll build a full multithreaded example application for the <span markdown="span">[Nashorn Javascript engine][1]</span> bundled with Java 8. The techniques we establish here can be readily applied in UI applications with JavaFX, as presented in: <span markdown="span">[Scripted user interfaces with Nashorn and JavaFX]({% post_url 2014-05-28-scripted-user-interfaces-with-nashorn-and-javafx %})</span> as well as myriad other scenarios, such as plugin scripts for Java applications and cron jobs and shell scripts, not dissimilar in style to that of Python or Ruby in place of Bash.
</p>
<p>
It's worth noting that we can access <span markdown="span">_anything_</span> from Java in Javascript including third party libraries and our own Java code making up for any deficiencies of the Javascript language (file I/O for example).
</p>

</div>
<div class="intro-img-border">
<div class="intro-img-bevel">
<div class="intro-img">
<img class="article-image" alt="{{page.article_img_alt}}" title="{{page.article_img_title}}" src="{{ASSET_PATH}}/{{page.article_img}}"/>
</div>
</div>
</div>
</div>
<br/>
<br/>


#### TL;DR
Just give me the code: [GitHub][2]
<br/>
<br/>

First, let's look at how to create and start a thread.

{% highlight javascript linenos=table %}

// import and alias Java Thread and Runnable classes
var Thread = Java.type("java.lang.Thread");
var Runnable = Java.type("java.lang.Runnable");

// declare our thread
this.thread = new Thread(new Runnable(){
  run: main()
});

// start our thread
this.thread.start();
return;

{% endhighlight %}

So far, so good. Line 7 determines which function shall be run inside the thread and in this case we've called it `main` but it could be named anything we choose. Our runnable function (in this case, `main`) should have a structure similar to this:

{% highlight javascript linenos=table %}
function main(){
  function inner(){
    //do stuff
  }
  return inner;
};

{% endhighlight %}

Now, there are many ways to skin a cat, so I'm not saying this is the _only_ way to do things.
<br/>
<br/>

#### How about a proper working example?
Righto, let's get underway and write our first threaded object called `Sleeper`. As the name suggests, Sleeper sleeps! However it does provide us with the added functionality of being interruptable (i.e. we can wake Sleeper while it is sleeping). Here we will also introduce locks, in particular the [ReentrantLock][3]. ReentrantLock offers quite a few benefits over synchronized (which has even been dropped entirely in Java 8), such as lock timeouts, non-blocking lock aquisition and interruptibility. Interruptibility is something we will take advantage of in our sleeper, which would prove handy in cases where you would want to cleanly shut down a service that contained a particularly long sleep cycle.

**sleep.js**
{% highlight javascript linenos=table %}
/**
* An interruptable sleep routine.
*/

function Sleeper(){
  var self = this;
  this.lock = new java.util.concurrent.locks.ReentrantLock();
  this.wake = this.lock.newCondition();

  /**
  * Starts a thread containing a sleep routine.
  * @param interval the sleep interval in seconds.
  */
  this.sleep = function(interval){
    self.thread = new Thread(new Runnable(){run: self.sleeper(self, interval)});
    self.thread.start();
    self.lock.lock();
    self.wake.await();
    self.lock.unlock();
  };


  /**
  * Interruptable sleep thread.
  * @param self a reference to our containing self who spawned this thread
  * routine (i.e. Sleeper().this).
  * @param interval the sleep interval in seconds
  * @return the inner function declaration.
  */
  this.sleeper = function(self, interval){
    function inner(){
      try {
	Thread.sleep(interval * 1000);
      }
      catch (e) {
	if (!(e instanceof java.lang.InterruptedException)) {
	  print("Unexpected error " + e.toString())
	}
      }
      finally{
	self.lock.lock();
	self.wake.signalAll();
	self.lock.unlock();
      }
    }
    return inner;
  };


  /**
  * Interrupts the sleep thread. This breaks sleep without waiting for sleep
  * interval to complete.
  */
  this.waken = function(){
    self.thread.interrupt();
  };
};
{% endhighlight %}

Hopefully the code comments explain things well enough, if not, hit me up for more detail in the comments section below. 

Noteworthy:

 * Unlike its predecessor Rhino, Nashorn **does not** wrap exceptions in a Javascript javaException object.
<br/>
<br/>
 

#### Ok, how about a proper working application?
Alrighty, let's build a toy application. We'll build a stupidly simple threaded webservice which:

 * takes a url (endpoint) as input
 * prints to the console what it receives when calling the endpoint
 * sleeps for a random period between 0 and 10 seconds
 
To make this webservice complete, we'll also add a `run` function and `shutdown` function.

**webservice.js**
{% highlight javascript linenos=table %}
/**
 * Crude example script.
 * 
 * Javascript has no native threading so there is a bit of cool hackery
 * going on in this script using Java threads and concurrency.
 *
 */

load('sleep.js')

var Thread = Java.type("java.lang.Thread");
var Runnable = Java.type("java.lang.Runnable");

function WebService(endpoint){
  this.endpoint = endpoint;
  this.sleeper = new Sleeper();
};

/**
* This is the entry point for this script.
* Return the running script instance.
*/
WebService.prototype.run = function(){
  var self = this;
  this.threadCancelled = false;
  this.thread = new Thread(new Runnable(){
    run: self.main(self.sleeper, self.endpoint)
  });
  this.thread.start();
  return;
};


/**
* Allows script to perform its own cleanup routine before shutting down.
*/
WebService.prototype.shutdown = function(){
  this.threadCancelled = true;
  this.sleeper.waken();
  //block while waiting for thread to terminate
  while (this.thread.isAlive()){
    try {
      Thread.sleep(1000);
    }
    catch (e) {
      self.threadCancelled = true;
    }
  }
  return true;
};


WebService.prototype.main = function(sleeper, endpoint){
  var self = this;
  var JavaScanner = new JavaImporter(
    java.util.Scanner,
    java.net.URL
  );
  function inner(){
    while(!self.threadCancelled){
      with(JavaScanner){
	var scanner = new Scanner(new URL(endpoint).openStream(), "UTF-8");
	while (scanner.hasNextLine()) {
	  print(scanner.nextLine());
	}
	scanner.close();
      }
      sleeper.sleep(Math.random()*10); // just wait around a bit...
    }
  }
  return inner;
};

{% endhighlight %}

Noteworthy:

 * on line 9 you'll see `load('sleep.js')`. `load` allows us to import scripts.
 * on line 55 you'll see a "scoped import". We can use this scope to then call packages and/or classes directly by wrapping them in a `with` block as shown on line 61.
<br/>
<br/>

Now we have our little webservice which uses our little sleeper, we can write a little script to demonstrate everything :)

**example.js**
{% highlight javascript linenos=table %}
load('webservice.js')

var Thread = Java.type("java.lang.Thread");

var automeme = new WebService('http://api.automeme.net/text?lines=1');
var bitcoin = new WebService('https://www.bitstamp.net/api/ticker/');
var weather = new WebService('http://api.openweathermap.org/data/2.5/weather?q=Hobart,au');

print("starting weather thread...");
weather.run();
print("starting bitcoin thread...");
bitcoin.run();
print("starting automeme thread...");
automeme.run();

//run for 15 seconds
Thread.sleep(15000);

//shut down weather
print("stopping weather thread...");
weather.shutdown();

//run for 15 seconds
Thread.sleep(15000);

//shut down bitcoin
print("stopping bitcoin thread...");
bitcoin.shutdown();

//run for 10 seconds
Thread.sleep(10000);

//shut down automeme
print("stopping automeme thread...");
automeme.shutdown();

print("goodbye :)");
{% endhighlight %}

That's all there is to it. To execute, just do this in the shell:
{% highlight bash linenos=table %}
jjs example.js
{% endhighlight %}
<br/>

#### Shebang
If you want to run your scripts as system executables, you can use the shebang (#!) at the beginning of a script file. Just add `#!/usr/bin/jjs` as the first line in your script. To execute:
{% highlight bash linenos=table %}
./example.js
{% endhighlight %}
<br/>

**Concluding remarks**

If you really, really want to write multithreaded code with Javascript, you can and there are many legitimate reasons you may choose to do so. Those reasons could include [scripted user interfaces with JavaFX]({% post_url 2014-05-28-scripted-user-interfaces-with-nashorn-and-javafx %}), [scripted plugins for a Java application or system]({% post_url 2014-05-24-java-scripting-plugin-framework %}), cron and shell scripts. Even if just for no reason at all, mashing languages together to produce new functionality is fun and educational. I hope you enjoyed this article, and as ever, if you have comments or questions, please leave them below and I'll be happy to reply.

For similar articles, please see:

 * [Scripted user interfaces with Nashorn and JavaFX]({% post_url 2014-05-28-scripted-user-interfaces-with-nashorn-and-javafx %})
 * [Java Plugin Scripting Architecture]({% post_url 2014-05-24-java-scripting-plugin-framework %})



[1]:http://docs.oracle.com/javase/8/docs/technotes/guides/scripting/nashorn/intro.html
[2]:https://github.com/benhowell/NashornMultithreadedExample
[3]:http://docs.oracle.com/javase/8/docs/api/java/util/concurrent/locks/ReentrantLock.html
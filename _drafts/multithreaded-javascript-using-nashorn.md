---
layout: post
title: "Multithreaded Javascript using Nashorn"
description: "Multithreaded Javascript using Nashorn"
tagline: "guide"
category: guide
tags: [nashorn, javascript, jsr-223, multithreaded, beginner, example, tutorial, guide]
article_img: bootstrap/img/garlic_braid.jpg
article_img_title: Multithreaded Garlic
---
{% include JB/setup %}
<div class="intro">
<div class="intro-txt">
<p>
Javascript has no native threading of it's own, but it is possible to craft performant multithreaded code using Java threads inside javascript, in fact this has been possible since Java 6 with the initial release of the javax.scripting.ScriptEngine for the Rhino engine. 
</p>
<p>
In this article, we'll build a full working multithreaded example application for the <span markdown="span">[Nashorn Javascript engine][1]</span> bundled with Java 8. The techniques we establish here can readily be applied in UI applications with JavaFX, as presented in my article: <span markdown="span">[Scripted user interfaces with Nashorn and JavaFX]({% post_url 2014-05-28-scripted-user-interfaces-with-nashorn-and-javafx %})</span> as well as myriad other scenarios, such as cron jobs and shell scripts, similar in style to that of Python or Ruby in place of bash.
</p>
<p>
Before we get underway, it's worth noting that we can access _anything_ from Java in Javascript including third party libraries and our own Java code making up for any deficiencies of the language (file I/O for example).
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

<br/>
<br/>


#### TL;DR
Just give me the code: [GitHub][2]
<br/>
<br/>




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





**example.js**
{% highlight javascript linenos=table %}
load('random_meme_generator.js')

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






Shebang
You can use the shebang (#!) at the beginning of a script file to enable the script file to run as a shell executable. If you specify the path to the jjs tool in the shebang, then when you execute the script, the shell runs the jjs tool instead, and passes the script file to it.

#!/usr/bin/jjs
mv example.js example
chmod 755 example
./example


#### Notes


[1]:http://docs.oracle.com/javase/8/docs/technotes/guides/scripting/nashorn/intro.html
[2]:https://github.com/benhowell/NashornMultithreadedExample
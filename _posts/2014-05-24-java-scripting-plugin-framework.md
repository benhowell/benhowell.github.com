---
layout: post
title: "Java Plugin Scripting Architecture"
description: "Java Plugin Scripting Architecture"
tagline: "guide"
category: guide
tags: [java, python, jsr 223, plugin, framework, architecture, beginner, example, tutorial, guide]
article_img: bootstrap/img/plugin_250.jpg
article_img_title: Yes, it has a hairy lead by Anonymous
---
{% include JB/setup %}
<div class="intro">
  <div class="intro-txt">
  <p>
    Plugins are a great way to allow extensions and customisation to be added to your application over time. There are many software use cases that can benefit from a plugin architecture, such as stream processing engines (e.g. new data sources, filters, processing algorithms) and software that involves content creation and editing such as text editors (e.g. font effects, layout management) and photo editors (e.g. lens effects, colourisation methods, file formats) to name but a few.
  </p>
  <p>
    In this article, we will take a comprehensive walk through of creating our own, complete plugin framework in Java and then jump to the scripting side and implement some plugins to perform asynchronous tasks. For this article, we will be writing our plugins in python and javascript, however, the architecture will cater for any implemented language engine<span markdown="span">[^1]</span>.
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
Just give me the code: [GitHub][1]
<br/>
<br/>


#### Why write your own framework?
Granted, there are plenty of Java plugin frameworks out there already, but there's no reason why you shouldn't build a simple plugin architecture yourself, and in many cases this may be the better solution as it allows you to customise the plugin system to precisely your requirements. Your custom design allows you to strictly define the API between your application and plugins which helps you avoid unnecessarily complecting your application to suit a particular plugin framework or library, and, allows you to simplify how plugin providers write their components. Provided you've designed the API for your plugins thoughtfully, you'll be surprised at how quick and easy writing your framework can be.
<br/>
<br/>

#### Let's get on with it then!
Let's get started right at the core of our application with our script manager. We will use an instance of ScriptManager for each plugin added to the system. ScriptManager will provide a number of execution mechanisms whilst providing for two-way communication with our plugins. 


**ScriptManager.java**
{% highlight java linenos=table %}
package net.benhowell.example;

import java.io.*;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import javax.script.Compilable;
import javax.script.CompiledScript;
import javax.script.Invocable;
import javax.script.ScriptEngine;
import javax.script.ScriptEngineFactory;
import javax.script.ScriptEngineManager;
import javax.script.ScriptException;

/**
 * Created by Ben Howell [ben@benhowell.net] on 22-May-2014.
 */
 
public class ScriptManager {

  private ScriptEngineManager manager;
  private String engineName;
  private String name;
  private String script;
  private ScriptEngine engine;
  private CompiledScript compiledScript;
  private Invocable invocable;

  /**
   * Constructor. Creates script engine manager, loads script and sets delegate
   * parameter before script is parsed.
   * @param engineName the engine name that runs the script (e.g. "python",
   * "javascript", etc).
   * @param scriptPath the plugin path
   * @param delegate the delegate containing application functions callable
   * from scripts.
   */
  public ScriptManager(String engineName, File scriptPath, ScriptDelegate delegate) {
    this.manager = new ScriptEngineManager();
    this.engineName = engineName;
    this.name = scriptPath.getName();
    this.script = scriptPath.getAbsolutePath();
    this.setEngine(engineName);
    this.setParameter("delegate", delegate);
    this.loadScript();
  }

  /**
   * Sets a global variable in the script engine. This variable will be
   * callable from anywhere in the script.
   * @param name parameter name.
   * @param value parameter value.
   */
  public void setParameter(String name, Object value) {
    this.engine.put(name, value);
  }

  /**
   * Convenience function for setting a number of parameters.
   * @param parameters HashMap of parameter name/value pairs.
   */
  public void setParameters(HashMap<String, Object> parameters) {
    for (Map.Entry<String, Object> entry : parameters.entrySet()){
      this.engine.put(entry.getKey(), entry.getValue());
    }
  }
 
  /**
   * Returns the available engine types supported.
   * @return the names of all engines supported.
   */
  public List<String> getAvailableEngines() {
    List<String> engines = new ArrayList<String>();
    for (ScriptEngineFactory factory : manager.getEngineFactories()) {
      engines.add(factory.getLanguageName());
    }
    return engines;
  }
 
  /**
   * Sets the engine type for this ScriptManager.
   * @param engineName the name of the engine to set.
   */
  private void setEngine(String engineName) {
    this.engine = manager.getEngineByName(engineName);
  }

  /**
   * Executes a function within the currently set script.
   * @param function the function to execute.
   * @param parameters the parameters to set.
   * @return the result.
   */
  public Object executeFunction(String function, HashMap<String, Object> parameters){
    this.setParameters(parameters);
    return this.executeFunction(function);
  }

  /**
   * Executes a function within the currently set script.
   * @param function the function to execute.
   * @return the result.
   */
  public Object executeFunction(String function){
    //if script engine is invocable, invoke method directly
    if(this.isInvocable()) {
      try {
        return invocable.invokeFunction(function);
      }
      catch (ScriptException e) {
        System.out.println("ScriptException error: " + e.toString());
      }
      catch (NoSuchMethodException e) {
        System.out.println("function: " + function + " not found: " + e.toString());
      }
    }
    //else pass function argument and execute script
    else {
      Object obj;
      this.setParameter("func", function);
      if(this.isCompilable()) {
        obj = this.execute(this.compiledScript);
      }
      else {
        obj = this.execute(this.script);
      }
      //reset function argument
      this.setParameter("func", null);
      return obj;
    }
    return null;
  }

  /**
   * Returns true if script is Invocable, else false.
   * @return true if script is Invocable, else false.
   */
  private Boolean isInvocable() {
    return invocable != null;
  }

  /**
   * Returns true if script is Compilable, else if script is Invocable or not
   * Compilable returns false.
   * @return true if script is Compilable, else if script is Invocable or not
   * Compilable returns false.
   */
  private Boolean isCompilable() {
    return compiledScript != null;
  }

  /**
   * Loads the script. Attempts to make script Invocable or Compilable as
   * necessary.
   */
  private void loadScript() {
    this.setScript(this.script);

    //set invocable if available
    this.setInvocable(this.script);

    //if not invocable, set compilable if available
    if(!this.isInvocable())
      this.setCompilable(this.script);

    //if not invocable or compilable, script will be interpreted
    // every time it is called.
  }

  /**
   * Streams the script from file.
   * @returns entire script as a String.
   */
  private static String convertStreamToString(InputStreamReader is) {
    java.util.Scanner s = new java.util.Scanner(is).useDelimiter("\\A");
    return s.hasNext() ? s.next() : "";
  }


  /**
   * Retrieves the script and stores it as a String for further use.
   * @param scriptPath the script itself or the location of the script.
   */
  private void setScript(String scriptPath) {
    if(scriptPath != null) {
      try {
        BufferedInputStream bis;
        bis = new BufferedInputStream(new FileInputStream(scriptPath));
        InputStreamReader in = new InputStreamReader(bis);
        this.script = convertStreamToString(in);
      }
      catch (FileNotFoundException e) {
        System.out.println("error whilst retrieving script from file: " + e.toString());
      }
    }
    else {
      System.out.println("scriptPath is null");
    }
  }

  /**
   * Attempts to interpret and set the given script to Invocable.
   * @param script the script to make Invocable.
   */
  private void setInvocable(String script) {
    //if available, set invocable interface
    this.invocable = null;
    if(script == null) {
      System.out.println("script code has not been set");
    }
    if(this.engine instanceof Invocable) {
      this.execute(script); //parse script
      invocable = (Invocable) this.engine;
    }
    else {
      System.out.println("Invocable is not implemented by engine: " + this.engineName);
    }
  }

  /**
   * Attempts to compile the given script.
   * @param script the script to compile.
   */
  private void setCompilable(String script) {
    this.compiledScript = null;
    if(script == null) {
      System.out.println("script code has not been set");
    }
    if(this.engine instanceof Compilable) {
      try {
        Compilable compilable = (Compilable) engine;
        this.compiledScript = compilable.compile(script);
        compiledScript.eval(); //parse script
      }
      catch (ScriptException e) {
        System.out.println("exception thrown whilst setting compilable: " + e.toString());
      }
    }
    else {
      System.out.println("Compilable is not implemented by engine: " + this.engineName);
    }
  }

  /**
   * Interprets and executes a script.
   * @param script the script to execute.
   * @return the result (if any).
   */
  public Object execute(String script){
    try {
      return this.engine.eval(script);
    }
    catch (ScriptException e) {
      System.out.println("error whilst executing script: " + e.toString());
    }
    return null;
  }

  /**
   * Executes a compiled script.
   * @param script the script to execute.
   * @return the result (if any).
   */
  private Object execute(CompiledScript script){
    try {
      return script.eval();
    }
    catch (ScriptException e) {
      System.out.println("error whilst executing compiled script: " + e.toString());
    }
    return null;
  }
}
{% endhighlight %}

ScriptManager allows execution of scripts such as JavaScript, Python, Ruby, Groovy, Judoscript, Haskell, Tcl, Awk and PHP amongst others. Java8 currently has over 30 different language engines developed for it. This implementation uses dynamic invocation of individual functions and objects within scripts using Invocable where available, and where not available, implements a graceful fallback strategy to compilation of script and passing the function name to execute to the script (if available). Where neither Invocable nor Compilable are available, scripts are interpreted each time they are run.

We can expose our application objects, values and variables as global variables to a script. The script can access each variable and can call public methods on it. The syntax to access Java objects, methods and fields is dependent on the scripting language used.

We can pass any object, value or variable to the script using the following call: `ScriptManager.setParameter("parameterName", Object);`
We can access any object, value or variable passed to or created by the script itself from java using the following call: `ScriptManager.getParameter("parameterName");`

Hopefully the comments in the file above have explained almost everything else, if not, please pose me questions in the comments section below as I'll be happy to go into further detail.
<br/>
<br/>

Now, let's define a set of delegate methods for our scripts. The content of the delegate will be determined by whatever API you want your application to expose to external plugins. They are by no means restricted in any way so anything you want to expose from within your application is fair game. 

**ScriptDelegate.java**
{% highlight java linenos=table %}
package net.benhowell.example;

import java.util.List;

/**
 * Created by Ben Howell [ben@benhowell.net] on 22-May-2014.
 */
public class ScriptDelegate {

  public void dispatch(String data){
    System.out.println("new message: ");
    System.out.println(" " + data);
  }

  public void dispatch(List<String> data){
    System.out.println("new messages: ");
    for(String d: data)
      System.out.print(" " + d);
  }
}
{% endhighlight %}

The delegate above exposes some trivial methods as a demonstration of what a delegate may look like.
<br/>
<br/>

Now we have all the infrastructure in place, we can write a little application to dynamically load our plugins and perform some task.




**Main.java**
{% highlight java linenos=table %}

package net.benhowell.example;

import java.io.File;
import java.util.ArrayList;
import java.util.List;

/**
 * Created by Ben Howell [ben@benhowell.net] on 22-May-2014.
 */
public final class Main {
  public static void main(String[] args) {

    String filePath = new File("").getAbsolutePath();
    List<ScriptManager> managers = loadPlugins(filePath+"/plugins/");

    System.out.println("Available script engines");
    for(String engine: managers.get(0).getAvailableEngines()){
      System.out.println("- engine: " + engine);
    }

    for(ScriptManager m: managers){
      Object instance = m.executeFunction("run");
      m.setParameter("instance", instance);
      System.out.println(m.getName() + " running = " + m.executeFunction("isRunning"));
    }

    //whatever else your app does... probably an event loop or something...
    try {
      //run for 30 seconds
      Thread.sleep(1000 * 30);
    }
    catch(InterruptedException e){}

    for(ScriptManager m: managers){
      System.out.println("shutting down: " + m.getName() + "...");
      m.executeFunction("shutDown");
    }

    System.exit(0);
  }

  public static List<ScriptManager> loadPlugins(String directoryName) {
    List<ScriptManager> managers = new ArrayList<ScriptManager>();
    File directory = new File(directoryName);
    File[] files = directory.listFiles();
    for (File file : files) {
      if (file.isDirectory()) {
        System.out.println(file.toString());
        for (File f : file.listFiles()) {
          System.out.println(f.toString());
          if(f.getAbsolutePath().endsWith(".py")){
            managers.add(new ScriptManager("python", f, new ScriptDelegate()));
          }
          else if(f.getAbsolutePath().endsWith(".js")){
            managers.add(new ScriptManager("javascript", f, new ScriptDelegate()));
          }
          // else: whatever other engines you want to support
        }
      }
    }
    return managers;
  }
}
{% endhighlight %}

That's all there is to it. Seriously. We've just built our plugin framework. 

Noteworthy here is:

 * line 22 we are returned an instance of the plugin called (e.g. instantiated class object from our script).
 
 * line 23 we set that instance as a parameter in the script engine so we can make calls similar to `self` or `this` within the running script itself.
<br/>
<br/>

**Concluding remarks**

For this particular implementation of a plugin framework, we've favoured convention over configuration.

 * We assume that all plugins are contained within the plugins directory. 
 
 * We assume that the plugin itself is contained in a folder and it's file name is consistent with the language it is implemented in (i.e. a python plugin will be named `*.py`).
 
 * We have determined that each plugin script must at least contain the following 3 functions: `run()`, `isRunning()`, and `shutDown()`.
 
Doing all of this allows us to dynamically load plugins at run time without prior knowledge of those plugins[^2]. Defining the call hooks for your API (either at the application or plugin side) should be a well thought through design choice and should be documented accordingly. The plugin developer documentation should very clearly spell out this binding contract (i.e. the required functions in their plugin). We could of course build tests into our application that checks for these mandatory functions at plugin load time, however that is beyond the scope of this article. Similarly, our delegate API(s) need to be well documented and supplied as part of the plugin developer documentation.
<br/>
<br/>

OK, let's now jump to the other side and develop a plugin for our application. This will be a rather trivial bit of asynchronous code that simply runs in the background and pushes data into our application every few seconds. In this example, we will be streaming data about the current bitcoin market price.

**bit_coin_price.py**
{% highlight python linenos=table %}
"""
* Crude example script.
* Script is passed a ScriptDelegate which is accessed via the "delegate" variable.
"""

import time
from threading import Thread, InterruptedException
from urllib import urlopen
from java.lang import Boolean

def run():
  """
  This is the entry point for this script.
  Returns the running script instance.
  """
  return BitCoinPriceWatch()


def isRunning():
  """
  Return true if running, else false.
  """
  if instance is not None:
    if instance.is_running():
      return Boolean("True")
  return Boolean("False")


def shutDown():
  """
  Allows script to perform its own cleanup routine before shutting down.
  """
  instance.shut_down()


class BitCoinPriceWatch():
  def __init__(self):
    """
    Constructor.
    """
    self.thread_cancelled = False
    self.thread = Thread(target=self.run)
    self.thread.start()


  def run(self):
    """
    Main loop.
    """
    while not self.thread_cancelled:
      try:
        for line in urlopen('https://www.bitstamp.net/api/ticker/'):
          delegate.dispatch(line + '\n')
        time.sleep(4) # just wait around a bit...
      except InterruptedException:
        self.thread_cancelled = True


  def is_running(self):
    """
    Returns true if main loop is running.
    """
    return self.thread.isAlive()


  def shut_down(self):
    """
    Performs cleanup routine.
    Return true after shutdown.
    """
    self.thread_cancelled = True
    while self.thread.isAlive():
      time.sleep(1)
    return True
{% endhighlight %}

Noteworthy items here include: 

 * the function definitions: `run()`, `isRunning()`, and `shutDown()` as required by our application.
 
 * the use of the `instance` object for calling of internal methods from `isRunning()`, and `shutDown()`
 
 * the use of the the delegate throughout the script.
<br/>
<br/>

This is another plugin almost identical to the one above and is used for the purpose of demonstrating multiple plugins running at once.

**weather_watch.py**
{% highlight python linenos=table %}
"""
* Crude example script.
* Script is passed a ScriptDelegate which is accessed via the "delegate" variable.
"""

import time
from threading import Thread, InterruptedException
from urllib import urlopen
from java.lang import Boolean

def run():
  """
  This is the entry point for this script.
  Returns the running script instance.
  """
  return WeatherWatch()


def isRunning():
  """
  Return true if running, else false.
  """
  if instance is not None:
    if instance.is_running():
      return Boolean("True")
  return Boolean("False")


def shutDown():
  """
  Allows script to perform its own cleanup routine before shutting down.
  """
  instance.shut_down()


class WeatherWatch():
  def __init__(self):
    """
    Constructor.
    """
    self.thread_cancelled = False
    self.thread = Thread(target=self.run)
    self.thread.start()


  def run(self):
    """
    Main loop.
    """
    while not self.thread_cancelled:
      try:
        for line in urlopen('http://api.openweathermap.org/data/2.5/weather?q=Hobart,au'):
          delegate.dispatch(line + '\n')
        time.sleep(5) # just wait around a bit...
      except InterruptedException:
        self.thread_cancelled = True


  def is_running(self):
    """
    Returns true if main loop is running.
    """
    return self.thread.isAlive()


  def shut_down(self):
    """
    Performs cleanup routine.
    Return true after shutdown.
    """
    self.thread_cancelled = True
    while self.thread.isAlive():
      time.sleep(1)
    return True
{% endhighlight %}

To wrap things up, there is also a multithreaded javascript plugin in the [GitHub repository][1] if you're interested.

That brings to a conclusion our development of a custom plugin architecture. If you have any questions please leave them in the comments below and I'll be happy to reply.

For the complete application source code please get it from my [GitHub][1].
<br/>
<br/>


#### Notes
[^1]:Java scripting API implements a JSR 223 compliant framework for embedding script engines. The last time I looked, there were over 30 engines implemented, however the functionality of those engines and their maintenance and development vary so YMMV. Some of the more common languages are: awk, javascript, python and ruby. 
[^2]:Bear in mind that if you want to allow for complex plugins with more than one file (e.g. a python plugin with many files, and perhaps module directories), then the simple loader and conventions presented here are of course inadequate.





[1]:https://github.com/benhowell/JavaPluginScripting









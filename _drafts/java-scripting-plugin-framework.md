---
layout: post
title: "Java Plugin Scripting Architecture"
description: ""
tagline: "guide"
category: guide
tags: [java, python, jsr 223, plugin, framework, architecture, beginner, example, tutorial, guide]
article_img: bootstrap/img/qn.png
article_img_title: Question Mark by Anonymous
---
{% include JB/setup %}
<div class="intro">
  <div class="intro-txt">
  <p>
    Plugins are a great way to allow extensions and customisation to be added to your application over time. There are many software use cases that can benefit from a plugin architecture, such as stream processing engines (e.g. new data sources, filters, processing algorithms) and software that involves content creation and editing such as text editors (e.g. font effects, layout management) and photo editors (e.g. lens effects, colourisation methods, file formats) to name but a few.
  </p>
  <p>
    In this article, we will take a comprehensive walk through of creating our own, complete plugin framework in Java and then jump to the scripting side and implement some plugins to perform asynchronous tasks. For this article, we will be writing our plugins in python, however, the architecture will cater for any implemented language engine<span markdown="span">[^1]</span>.
  </p>
  </div>
<div class="intro-img-border">
<div class="intro-img-bevel">
<div class="intro-img">
<img class="article-image" src="{{ASSET_PATH}}/{{page.article_img}}"/>
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


#### Why write my own?
Granted, there are plenty of Java plugin frameworks out there already, but there's no reason why you shouldn't build a simple plugin architecture yourself, and in many cases this may be the better solution as it allows you to customise the plugin system to precisely your requirements. Your custom design allows you to strictly define the API between your application and plugins which helps you avoid unneccessarily complecting your application to suit a particular plugin framework or library, and, allows you to simplify how plugin providers write their components. Provided you've designed the API for your plugins thoughtfully, you'll be suprised at how easy and succinct writing your framework can be.
<br/>
<br/>

#### Let's get on with it then!
Let's get started right at the core of our application with our script manager. We will use an instance of ScriptManager for each plugin added to the system. ScriptManager will provide a number of execution mechanisms whilst providing for two-way communication with our plugins. 


**ScriptManager.java**
{% highlight java linenos%}
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

public class ScriptManager {

  private ScriptEngineManager manager;
  private String engineName;
  private String name;
  private String script;
  private ScriptEngine engine;
  private CompiledScript compiledScript;
  private Invocable invocable;

  public ScriptManager(String engineName, File scriptPath, ScriptDelegate delegate) {
    this.manager = new ScriptEngineManager();
    this.engineName = engineName;
    this.name = scriptPath.getName();
    this.script = scriptPath.getAbsolutePath();
    this.setEngine(engineName);
    this.setParameter("delegate", delegate);
    this.loadScript();
  }

  public void setParameter(String name, Object value) {
    this.engine.put(name, value);
  }

  public void setParameters(HashMap<String, Object> parameters) {
    for (Map.Entry<String, Object> entry : parameters.entrySet()){
      this.engine.put(entry.getKey(), entry.getValue());
    }
  }

  public Object executeFunction(String function, HashMap<String, Object> parameters){
    this.setParameters(parameters);
    return this.executeFunction(function);
  }

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
        System.out.println("function: " + function + " not found in script: " + e.toString());
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

  private Boolean isInvocable() {
    return invocable != null;
  }

  private Boolean isCompilable() {
    return compiledScript != null;
  }

  private void setEngine(String engineName) {
    this.engine = manager.getEngineByName(engineName);
  }

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

  private static String convertStreamToString(InputStreamReader is) {
    java.util.Scanner s = new java.util.Scanner(is).useDelimiter("\\A");
    return s.hasNext() ? s.next() : "";
  }

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

  public Object execute(String script){
    try {
      return this.engine.eval(script);
    }
    catch (ScriptException e) {
      System.out.println("error whilst executing script: " + e.toString());
    }
    return null;
  }

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

ScriptManager allows execution of scripts such as JavaScript and Python Ruby, Groovy, Judoscript, Haskell, Tcl, Awk and PHP amongst others. Java8 supports over 30 different language engines. This implementation uses dynamic invocation of individual functions and objects within scripts using Invocable where available, and where not available, implements a graceful fallback strategy to compilation of script (if available) and passing the function name to execute to the script. Where neither Invocable nor Compilable are available, scripts are interpreted each time they are run.

We can expose our application objects as global variables to a script. The script can access each variable and can call public methods on it. The syntax to access Java objects, methods and fields is dependent on the scripting language used.

We can pass any object, value or variable to the script using the following: ScriptManager.setParameter("parameterName", Object);
We can access any object, value or variable passed to or created by the script itself from java using the following: ScriptManager.getParameter("parameterName");

Hopefully the comments in the file above have explained almost everything, if not, please pose me questions in the comments section below.
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

The delegate above exposes some trivial methods

<br/>
<br/>






**Main.java**
{% highlight java linenos %}

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


<br/>
<br/>








**bit_coin_price.py**
{% highlight python linenos %}
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

<br/>
<br/>






#### Notes
[^1]:Java scripting API implements a JSR 223 compliant framework for embedding script engines. The last time I looked, there were over 30 engines implemented, however the functionality of those engines and their maintenance and development vary so YMMV. Some of the more common languages are: awk, javascript, python and ruby. 






[1]:https://github.com/benhowell/examples/tree/master/JavaPluginScripting









---
layout: post
title: "Java Plugin Scripting Architecture"
description: ""
tagline: "guide"
category: guide
tags: [java, plugin, framework, architecture, beginner, example, tutorial, guide, jsr 223]
article_img: bootstrap/img/qn.png
article_img_title: Question Mark by Anonymous
---
{% include JB/setup %}
<div class="intro">
  <div class="intro-txt">
  <p>
    Plugins are a great way to allow extensions and customisation to be added to your application over time. There are many software use cases that can benefit from a plugin architecture, such as stream processing engines (e.g. new data sources, filters, processing algorithms) and software that involves content creation and editing such as text editors (e.g. font effects, layout management) and photo editors (e.g. lens effects, colourisation methods) to name but a few.
  </p>
  <p>
    Java scripting API implements a JSR 223 compliant framework for embedding script engines. The last time I looked, there were over 30 engines implemented[^1].
  </p>
  <p>
    In this article, we will take a comprehensive walk through of creating our own, complete plugin framework and then jump to the scripting side and implement some plugins to perform asynchronous tasks.
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










The common examples are the plug-ins used in web browsers to add new features such as search-engines, virus scanners, or the ability to utilize a new file type such as a new video format. Well-known browser plug-ins include the Adobe Flash Player, the QuickTime Player, and the Java plug-in, which can launch a user-activated Java applet on a web page to its execution a local Java virtual machine.

Add-on (or addon) is the general term for what enhances an application. It comprises snap-in, plug-in, theme and skin.[1] An extension add-on tailors the core features of an application by adding an optional module, whereas a plug-in add-on would tailor the outer layers of an application to personalize functionality.

A theme or skin add-on is a preset package containing additional or changed graphical appearance details, achieved by the use of a graphical user interface (GUI) that can be applied to specific software and websites to suit the purpose, topic, or tastes of different users to customize the look and feel of a piece of computer software or an operating system front-end GUI (and window managers).







There are plenty of plugin frameworks out there already, but you can build a simple plugin architecture yourself, and in some cases this may well be the better solution as it allows you to customise the plugin system to precisely your requirements, allows you to strictly define the API interface between your application and plugins which helps you avoid unneccessarily complecting your application to suit a common plugin framework library and allows you to simplify how plugin providers write their components. 









Applications support plug-ins for many reasons. Some of the main reasons include:

to enable third-party developers to create abilities which extend an application
to support easily adding new features
to reduce the size of an application
to separate source code from an application because of incompatible software licenses.
Specific examples of applications and why they use plug-ins:

Audio editors use plug-ins to generate, process and/or analyse sound (Ardour, Audacity)
Email clients use plug-ins to decrypt and encrypt email (Pretty Good Privacy)
Graphics software use plug-ins to support file formats and process images (Adobe Photoshop, GIMP)
Media players use plug-ins to support file formats and apply filters (foobar2000, GStreamer, Quintessential, VST, Winamp, XMMS)
Microsoft Office uses plug-ins (better known as add-ins) to extend the abilities of its application by adding custom commands and specialized features
Packet sniffers use plug-ins to decode packet formats (OmniPeek)
Remote sensing applications use plug-ins to process data from different sensor types (Opticks)
Smaart, an audio spectrum analysis application which accepts plug-ins for third-party digital signal processors
Software development environments use plug-ins to support programming languages (Eclipse, jEdit, MonoDevelop)
Web browsers use plug-ins (often implementing the NPAPI specification) to play video and presentation formats (Flash, QuickTime, Microsoft Silverlight, 3DMLW)








/**

ScriptManager allows execution of scripts such as JavaScript and Python
with Java6. Java7 supports ruby, groovy, judoscript, haskell, tcl, awk, 
e4x and, php as well.

Scripts can be called from within any source, processor, timertask or
listener, or can be a first class source, processor, timertask or listener in
their own right.

This current SMG scripting implementation uses dynamic invocation of 
individual functions and objects within scripts using Invocable where 
available, and where not available, implements a graceful fallback strategy 
to compilation of script (if available) and passing the function name to 
execute to the script (implementation of the handling of this parameter is 
left to the script developers). Where neither Invocable nor Compilable are 
available, scripts are interpreted each time they are run.

NOTE: Unlike javax.script.ScriptEngineManager, the SMG ScriptManager will 
only control a single ScriptEngine and script. Therefore, each script 
requires a ScriptManager of it's own.

We can expose our application objects as global variables to a script. The 
script can access each variable and can call public methods on it. The syntax
to access Java objects, methods and fields is dependent on the scripting 
language used.

We can pass any object or var to the script using the following:
@code
ScriptManager.setParameter("parameterName", Object);
@endcode
e.g.
@code
scriptManager.setParameter("dispatcher", dispatcher);
@endcode
We can also pass a collection of parameters:
@code
ScriptManager.setParameters(parameters);
@endcode
e.g.
@code
HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("parameterName", Object);
...
scriptManager.setParameters(parameters);
@endcode


We can access any object or var passed to or created by the script itself from java using the following:
@code
ScriptManager.getParameter("parameterName");
@endcode
We must always cast objects when pulling them from a script. 
@code
Type myObject = (Type)ScriptManager.getParameter("parameterName");
@endcode
e.g.
@code
String myString = (String)scriptManager.getParameter("myString");
@endcode


Preferably, we should pass a delegate to the ScriptManager constructor.
e.g.
@code
public interface MyScriptDelegate {
	abstract void print(String s);
	...
}
	
MyScriptDelegate myScriptDelegate = new MyScriptDelegate() {	
	public void print(String s) {
		System.out.println(s);
	}
};
	
ScriptDelegate delegate = new ScriptDelegate("delegate", myScriptDelegate);
ScriptManager scriptManager = new ScriptManager(scriptConfiguration, delegate);

//meanwhile in your script (let's use javascript)...
...
var s = "Scripting is fun!";
delegate.print(s);
...
@endcode
	
For concrete examples see:
- au.csiro.ict.tasman.source.ScriptSource
- au.csiro.ict.tasman.processor.ScriptProcessor
- au.csiro.ict.tasman.timertask.ScriptTimerTask
- <a href="gateway_8properties_8json_source.html">examples/scripting/marvlis/gateway.properties.json</a>
- <a href="derwentbridgesource_8js_source.html">examples/scripting/marvlis/derwentbridgesource.js</a>
- <a href="hydro__derwentbridgesource_8properties_8json[javascript]_source.html">examples/scripting/marvlis/hydro_derwentbridgesource.properties.json[javascript]</a>
- <a href="derwentbridgesource_8py_source.html">examples/scripting/marvlis/derwentbridgesource.py</a>
- <a href="hydro__derwentbridgesource_8properties_8json[python]_source.html">examples/scripting/marvlis/hydro_derwentbridgesource.properties.json[python]</a>
- <a href="meadowbanksource_8py_source.html">examples/scripting/marvlis/meadowbanksource.py</a>
- <a href="hydro__meadowbanksource_8properties_8json_source.html">examples/scripting/marvlis/hydro_meadowbanksource.properties.json</a>

Scripts can be created and executed on the fly wherever needed.
e.g.
@code
//executes dynamic, one pass, inline, non compiled code snippets (use sparingly!).
private Object executeInlineScript(String engineName, String code, HashMap<String, Object> parameters) {
	ScriptManager scriptManager = new ScriptManager(new ScriptConfiguration(engineName, code));
	scriptManager.setParameters(parameters);
	return scriptManager.execute();
}
@endcode



Javascript Specific notes:
For testing, scripts can be run directly on the command line with jrunscript or node.js.

The built-in functions importPackage and importClass can be used to import Java packages and classes.
@code
// Import Java packages and classes 
// like import package.*; in Java
importPackage(java.awt);
// like import java.awt.Frame in Java
importClass(java.awt.Frame);
// Create Java Objects by "new ClassName"
var frame = new java.awt.Frame("hello");
// Call Java public methods from script
frame.setVisible(true);
// Access "JavaBean" properties like "fields"
print(frame.title);
@endcode

e.g.
@code
importClass(java.lang.Thread);
importPackage(Packages.au.csiro.ict.tasman.datatype);
...
var m = new Message("a.b.c", mySample);
...
try {
	Thread.sleep(10000);
}
...
@endcode


java.lang is not imported by default (unlike Java) because that would result in conflicts 
with JavaScripts built-in Object, Boolean, Math and so on.

importPackage and importClass functions "pollute" the global variable scope of JavaScript. 
To avoid that, you can use JavaImporter.

e.g.
@code
// create JavaImporter with specific packages and classes to import
var SwingGui = new JavaImporter(
	javax.swing,
	javax.swing.event,
	javax.swing.border,
	java.awt.event
);
with (SwingGui) {
	// within this 'with' statement, we can access Swing and AWT
	// classes by unqualified (simple) names.

	var mybutton = new JButton("test");
	var myframe = new JFrame("test");
}
@endcode
e.g.
@code
var JodaTime = new JavaImporter(
	org.joda.time.DateTime,
	org.joda.time.DateTimeZone
);
...
with(JodaTime){
	var d = new DateTime(DateTimeZone.UTC);
	...
}
@endcode
  
Arrays: While creating a Java object is the same as in Java, to create Java arrays in JavaScript we need to 
use Java reflection explicitly. But once created the element access or length access is the same as 
in Java. Also, a script array can be used when a Java method expects a Java array (auto conversion). 
So in most cases we don't have to create Java arrays explicitly.
  	
e.g.
@code
// create Java String array of 5 elements
var a = java.lang.reflect.Array.newInstance(java.lang.String, 5);

// Accessing elements and length access is by usual Java syntax
a[0] = "scripting is great!";
print(a.length);
@endcode
  	
Strings: It's important to keep in mind that Java strings and JavaScript strings are not the same. 
Java strings are instances of the type java.lang.String and have all the methods defined by that class. 
JavaScript strings have methods defined by String.prototype. The most common stumbling block is length, 
which is a method of Java strings and a dynamic property of JavaScript strings.

Java scripting API implements a JSR-223 compliant framework for embedding script engines.
The javascript engine is heavily based on the Rhino engine so that may be a good place to look 
for more in-depth stuff.

For more info: 
- http://docs.oracle.com/javase/6/docs/technotes/guides/scripting/programmer_guide/index.html
- https://developer.mozilla.org/en-US/docs/Scripting_Java



@author Ben Howell <ben.howell@csiro.au>
 */























**ScriptManager.java**
{% highlight java linenos %}
package net.benhowell.example;

import java.io.*;
import java.net.MalformedURLException;
import java.net.URL;
import java.net.URLConnection;
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

import org.python.core.Py;
import org.python.core.PySystemState;


/**
 * Created by Ben Howell [ben@benhowell.net] on 22-May-2014.
 */
public class ScriptManager {

  private ScriptEngineManager manager;
  private String engineName;
  private String script;
  private ScriptEngine engine;
  private CompiledScript compiledScript;
  private Invocable invocable;


  /**
   * Constructor. Creates script engine manager, loads script and sets delegate
   * parameter before script is parsed.
   * @param engineName the engine name that runs the script (e.g. "python",
   * "javascript", etc).
   * @param script the plugin code URL (file:// or http://)
   * @param delegate the delegate containing application functions callable
   * from scripts.
   */
  public ScriptManager(String engineName, String script, ScriptDelegate delegate) {
    this.manager = new ScriptEngineManager();
    this.engineName = engineName;
    this.script = script;
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
   * Retrieves a global variable from the script engine.
   * @param name the name of the parameter to retrieve.
   * @return the parameter requested.
   */
  public Object getParameter(String name) {
    return this.engine.get(name);
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
   * Executes an entire script.
   * @return the result.
   */
  public Object execute(){
    if(this.isCompilable())
      return execute(this.compiledScript);
    else
      return execute(this.script);
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
   * Sets the engine name that runs the script (e.g. "python", "javascript", etc).
   * @param engineName the name of the engine that runs the script.
   */
  private void setEngineName(String engineName) {
    this.engineName = engineName;
  }

  /**
   * Sets the engine type for this ScriptManager.
   * @param engineName the name of the engine to set.
   * NOTE: if using python, you need jython installed.
   */
  private void setEngine(String engineName) {
    // Special hack to set gateway lib path for jython
    if(engineName.equalsIgnoreCase("python")) {
      PySystemState engineSys = new PySystemState();
      engineSys.path.append(Py.newString("/usr/lib/python2.7/"));
      Py.setSystemState(engineSys);
    }
    this.engine = manager.getEngineByName(engineName);
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
   * @param code the script itself or the location of the script.
   */
  private void setScript(String code) {
    if(code != null) {
      try {
        BufferedInputStream bis;
        if(code.startsWith("http")) {
          URL url;
          url = new URL(code);
          URLConnection con = url.openConnection();
          bis = new BufferedInputStream(con.getInputStream());
        }
        else if(code.startsWith("file")) {
          String filePath = new File("").getAbsolutePath();
          bis = new BufferedInputStream(new FileInputStream(filePath+code.substring(7)));
        }
        else {
          //assume inline code snippet
          this.script = code;
          return;
        }
        InputStreamReader in = new InputStreamReader(bis);
        this.script = convertStreamToString(in);
      }
      catch (MalformedURLException e) {
        System.out.println("error whilst retrieving script from file: " + e.toString());
      }
      catch (FileNotFoundException e) {
        System.out.println("error whilst retrieving script from file: " + e.toString());
      }
      catch (IOException e) {
        System.out.println("error whilst retrieving script from file: " + e.toString());
      }
    }
    else {
      System.out.println("code is null");
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
    //if available, compile script.
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




**ScriptDelegate.java**
{% highlight java linenos %}
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




















[1]:https://github.com/benhowell/examples/tree/master/JavaPluginScripting

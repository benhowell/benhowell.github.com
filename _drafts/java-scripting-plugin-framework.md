---
layout: post
title: "java scripting plugin framework"
description: ""
tagline: "guide"
category: guide
tags: [java, concurrent, asynchronous, plugin, architecture, beginner, example, tutorial, guide]
article_img: bootstrap/img/qn.png
article_img_title: Question Mark by Anonymous
---
{% include JB/setup %}
<div class="intro">
  <div class="intro-txt">
    ???????????????????????
  </div>
<div class="intro-img-border">
<div class="intro-img-bevel">
<div class="intro-img">
<img class="article-image" src="{{ASSET_PATH}}/{{page.article_img}}"/>
</div>
</div>
</div>
</div>


#### TL;DR
Just give me the code: [GitHub][1]
<br/>
<br/>




**ScriptManager.java**
{% highlight java linenos=table %}
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

  
}
{% endhighlight %}








[1]:https://github.com/benhowell/examples/tree/master/JavaPluginScripting
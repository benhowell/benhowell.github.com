---
layout: post
title: "Scripted user interfaces with Nashorn and JavaFX"
description: "A comprehensive walkthough guide teaching how to develop scripted user interfaces with Nashorn and JavaFX"
tagline: "guide"
category: guide
tags: [nashorn, javafx, javascript, UI, jsr-223, java, beginner, example, tutorial, guide]
article_img: bootstrap/img/nashorn_javafx_250.jpg
article_img_title: Panzer by Anonymous
---
{% include JB/setup %}
<div class="intro">
<div class="intro-txt">
<p>
Java 8 has introduced a bunch of great features and one of the gems amongst those new features is <span markdown="span">[Nashorn][7]</span>. Nashorn is the replacement for the Rhino Javascript engine and could turn out to be a serious competitor for Google's V8. A lot of you are aware of JSR-233 and the ability to embed Javascript (and many other languages) in the back end using Java and the JVM. Well this has been the case since Java 6, so although Nashorn makes many, many improvements here, that is not the subject of this guide.
</p>
<p>
In this article, I'll introduce JavaScript in Java, known as <span markdown="span">`jjs`</span>. <span markdown="span">`jjs`</span> is a small wrapper around javax.script.ScriptEngine and provides a REPL and library for scripting in Javascript. For an in-depth example of building a plugin framework using the ScriptEngine, please see my former article <span markdown="span">[Java Plugin Scripting Architecture]({% post_url 2014-05-24-java-scripting-plugin-framework %})</span>.
</p>

</div>
<div class="intro-img-border">
<div class="intro-img-bevel">
<div class="intro-img">
<img class="article-image" alt="{{page.article_img_title}}" title="{{page.article_img_title}}" src="{{ASSET_PATH}}/{{page.article_img}}"/>
</div>
</div>
</div>
</div>
<br>
<p>
Adding the <span markdown="span">`-fx`</span> switch to the <span markdown="span">`jjs`</span> command allows you to execute fully blown <span markdown="span">[JavaFX][6]</span> UI applications written in Javascipt providing you with a pretty powerful UI scripting toolbox. Maturity aside, you could probably draw a pretty close analogy with <span markdown="span">[wxPython][5]</span>, but in this case, everything you need is already bundled in the Java 8 distribution.
</p>
<p>
Alrighty, let's build a little toy app to introduce a few concepts. This should be enough to get you away and hacking with Nashorn and JavaFX.
</p>
<br/>

#### TL;DR
Just give me the code: [GitHub][1]
<br/>
<br/>


#### Nuts and Bolts example
Let's jump straight in with a complete little UI application. For those familiar with JavaFX from Java, this example will be easy to follow, and for those that aren't, [here is a great place to get started with JavaFX in Java][2][^1]


**slideshow.js**
{% highlight javascript linenos=table %}
load("fx:base.js");
load("fx:controls.js");
load("fx:graphics.js");

var File = Java.type("java.io.File");

function start(primaryStage) {
  primaryStage.title = "Slideshow";

  var images = new java.util.ArrayList();
  var captions = new java.util.ArrayList();

  var root = new StackPane();
  var gridpane = new GridPane();
  var heading = new Label("My Aussie Slideshow");
  heading.setStyle("-fx-font-size: 30pt;");

  gridpane.add(heading, 0, 0); // c, r

  var imageView = new ImageView();
  imageView.setImage(new Image(fileToURL("Footy.png")));
  images.add(imageView);
  captions.add("Footy");
  imageView = new ImageView();
  imageView.setImage(new Image(fileToURL("Warnie.png")));
  images.add(imageView);
  captions.add("Warnie");
  imageView = new ImageView();
  imageView.setImage(new Image(fileToURL("Servo.png")));
  images.add(imageView);
  captions.add("Servo");

  var cur = 0;

  var borderpane = new BorderPane(images[cur]);
  BorderPane.setAlignment(images[cur], Pos.CENTER);
  gridpane.add(borderpane, 0, 1); // c, r
  GridPane.setHalignment(borderpane, HPos.CENTER)

  var col = new ColumnConstraints();
  col.setPercentWidth(100);
  col.setHalignment(HPos.CENTER);
  gridpane.getColumnConstraints().addAll(col);

  var caption = new Label(captions[cur]);
  caption.setStyle("-fx-font-size: 30pt;");
  gridpane.add(caption, 0, 2); // c, r

  var hbox = new HBox();
  var separator = new Separator();
  separator.maxWidth(12);
  separator.setStyle("visibility: hidden;");

  var prevButton = new Button();
  prevButton.text = "<- Back";
  prevButton.onAction = function(){
    if(cur > 0)
      cur--;
    else
      cur=images.size() -1;
    borderpane.setCenter(images[cur]);
    caption.text = captions[cur];
  }

  var nextButton = new Button();
  nextButton.text = "Next ->";
  nextButton.onAction = function(){
    if(cur < images.size() -1)
      cur++;
    else
      cur=0;
    borderpane.setCenter(images[cur]);
    caption.text = captions[cur];
  }

  hbox.getChildren().addAll(prevButton, separator, nextButton);

  var buttongrid = new GridPane();
  buttongrid.setAlignment(Pos.CENTER);
  buttongrid.add(hbox, 0, 0);
  gridpane.add(buttongrid, 0, 3); // c, r
  GridPane.setHalignment(hbox, HPos.CENTER)

  root.children.add(gridpane);
  primaryStage.scene = new Scene(root, 800, 600);
  primaryStage.show();
}

function fileToURL(file) {
    return new File(file).toURI().toURL().toExternalForm();
}
{% endhighlight %}
(oh, don't forget to add your own images and captions, or else get this entire project from [GitHub][1])

Our little app is done. To execute, just do this in the shell:
{% highlight bash linenos=table %}
jjs -fx slideshow.js
{% endhighlight %}
<br/>

Noteworthy:

 * line 1, 2 and 3 are predefined includes for JavaFX saving us from importing and/or defining a lot of individual JavaFX classes. For a listing of all files included by these imports, please refer to the [Oracle documentation][3].
 
 * line 5 shows the regular way to import and alias a class or package.
 
 * JavaFX CSS styling works almost as you would expect. There are some inconsistencies with some attributes preprended with `fx-` whilst some others are not which can lead to a little guess work. [The full CSS reference documentation can be found here][4]. 
<br/>
<br/>

Scoped imports still work as they did under Rhino and are still quite handy.
{% highlight javascript linenos=table %}
 // Scoped imports
  var JavaFileIO = new JavaImporter(
    java.io.BufferedReader,
    java.io.FileReader,
    java.io.File
  );

  //do some coding and stuff...
  with(JavaFileIO){
    var reader = new BufferedReader(new FileReader(file));
    //...
  }
{% endhighlight %}
<br/>

Old school class imports (e.g. `importClass(...)` ) under Rhino no longer work natively with Nashorn. If you do have legacy code writen for Rhino, you can however load a compatibility library like so...

{% highlight javascript linenos=table %}
// not required for Java versions < Java 8
load("nashorn:mozilla_compat.js");

// imports
importClass(java.lang.Thread);
importClass(java.lang.Runnable);
importPackage(java.util.concurrent.locks);
{% endhighlight %}

Which brings to an end this modest little introduction to UI scripted interfaces with Nashorn and JavaFX. If you have any questions please leave them in the comments below and I'll be happy to reply.
<br/>
<br/>


#### Notes
[^1]:All the available documentation from Oracle for getting started with JavaFX are specifically aimed at Java, howver the concepts map pretty much one-to-one with Nashorn.



[1]:https://github.com/benhowell/NashornJavafxExample
[2]:http://docs.oracle.com/javafx/2/get_started/jfxpub-get_started.htm
[3]:https://blogs.oracle.com/nashorn/entry/jjs_fx
[4]:http://docs.oracle.com/javafx/2/api/javafx/scene/doc-files/cssref.html
[5]:http://www.wxpython.org/
[6]:http://docs.oracle.com/javase/8/javafx/get-started-tutorial/jfx-overview.htm#JFXST784
[7]:http://www.oracle.com/technetwork/articles/java/jf14-nashorn-2126515.html

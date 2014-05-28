---
layout: post
title: "Introducing user interfaces with Nashorn and JavaFX"
description: "guide"
tagline: "guide"
category: guide
tags: [nashorn, javafx, UI]
article_img: bootstrap/img/nashorn_javafx_250.jpg
article_img_title: Panzer by Anonymous
---
{% include JB/setup %}
<div class="intro">
<div class="intro-txt">
<p>
Java 8 has introduced a bunch of great features, no doubt, and one of the gems amongst those new features is Nashorn. Nashorn is the replacement for the Rhino Javascript engine and could turn out to be a serious competitor for Google's V8. A lot of you are aware of JSR-233 and the ability to embed Javascript (and many other languages) in the backend using Java and the JVM. Well this has been the case since Java 6, so although Nashorn makes many, many improvements here, that is not the subject of this guide.
</p>
<p>
In this article, I'll introduce JavaScript in Java, known as `jjs`. `jjs` is a small wrapper around the javax ScriptEngine and provides a REPL and library for scripting in Javascript. For an in-depth discussion of the ScriptEngine, please see my former article <span markdown="span">[Java Plugin Scripting Architecture]({% post_url 2014-05-24-java-scripting-plugin-framework %})</span>.
</p>
<p>
Adding the `-fx` switch to the `jjs` command allows you to execute fully blown JavaFX UI applications written in (Nashorn) Javascipt providing you with a pretty powerful UI scripting toolbox. You could probably draw a pretty close analogy with wxPython, but in this case, everything you need is already bundled in the Java 8 distribution.
</p>
<p>
Alrighty, let's build a little toy app to introduce a few concepts. This should be enough to get you away and hacking with Nashorn and JavaFX.
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


#### Nuts and Bolts example
Let's jump straight in with a complete little UI application. For those familiar with JavaFX, this example will be easy to follow, and for those that aren't, [here is a great place to get started with JavaFX][2][^1]


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

Our little app is done. To execute, just do this in the shell:
{% highlight bash linenos=table %}
jjs -fx slideshow.js
{% endhighlight %}

<br/>
<br/>

Noteworthy:

 * line 1, 2 and 3 are predefined includes for JavaFX saving us from importing and/or defining a lot of individual JavaFX classes. For a listing of all files included by these imports, please refer to the [Oracle documentation][3].
 
 * line 5 shows an alternative way to import whilst at the same time aliasing the imported class.
 
 * JavaFX CSS styling is very much CSS-like and works almost as you would expect. There are some inconsistancies with some attributes preprended with 'fx-' whilst some others are not which can lead to a little guess work. [The full CSS reference documentation can be found here][4]. 


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
<br/>
<br/>





#### Notes
[^1]:All the available documentation from Oracle for getting started with JavaFX are specifically aimed at Java, howver the concepts map pretty much one-to-one with Nashorn.



[1]:https://github.com/benhowell/NashornJavafxExample
[2]:http://docs.oracle.com/javafx/2/get_started/jfxpub-get_started.htm
[3]:https://blogs.oracle.com/nashorn/entry/jjs_fx
[4]:http://docs.oracle.com/javafx/2/api/javafx/scene/doc-files/cssref.html

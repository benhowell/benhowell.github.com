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
Java 8 has introduced a bunch of great features, no doubt, but something getting a little less attention is Nashorn. Nashorn is the replacement for the Rhino Javascript engine and could turn out to be a serious competitor for Google's V8. Almost everyone is aware of JSR-233 and the ability to embed Javascript in the backend using Java and the JVM. Well this has been the case since Java 6, so although Nashorn makes many, many improvements here, that is not the subject of this guide.
  </p>
  <p>
In this article, I'll introduce `jjs`.`jjs` is the command line executor for Nashorn and could well add another handy tool to your toolkit.`-fx` switch allows you to execute full-blown JavaFX UI applications written in (Nashorn) Javascipt. That's right. You could probably draw a pretty close analogy with wxPython, but in this case, everything you need is already bundled in the distribution (Java 8).
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



[1]:https://github.com/benhowell/NashornJavafxExample



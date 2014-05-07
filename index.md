---
layout: default
title: Ben Howell
tagline: Supporting tagline
---
{% include JB/setup %}
<br/>
<br/>

<!--<div class="blog-index">
  {% for post in site.posts %}
  <h3><a href="{{ post.url }}">{{ post.title }}</a></h3><p><strong>{{ post.date | date: "%B %e, %Y" }}</strong><br>
  {{ post.content | strip_html | truncatewords:40 }}<br>
  <a href="{{ post.url }}">Read more</a><br/><hr>
  {% endfor %}
</div>-->






<div class="intro">
<div class="intro-txt">
<div class="blog-index">
  {% for post in site.posts %}
  <h3><a href="{{ post.url }}">{{ post.title }}</a></h3><p><strong>{{ post.date | date: "%B %e, %Y" }}</strong><br>
  {{ post.content | strip_html | truncatewords:40 }}<br>
  <a href="{{ post.url }}">Read more</a><br/><hr>
  {% endfor %}
</div>
</div>
<div class="intro-img-border">
<div class="intro-img-bevel">
<div class="intro-img">
<img class="article-image" src="{{ASSET_PATH}}/bootstrap/img/eventbus_250.jpg"/>
</div>
</div>
</div>
</div>
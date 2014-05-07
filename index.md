---
layout: default
title: Ben Howell
tagline: Supporting tagline
---
{% include JB/setup %}
<br/>
<br/>

<div class="blog-index">
  {% for post in site.posts %}
  <div class="intro">
  <div class="intro-txt-index">
  <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
  <p>
  <strong>{{ post.date | date: "%B %e, %Y" }}</strong><br>
  {{ post.content | strip_html | truncatewords:40 }}<br>
  <a href="{{ post.url }}">Read more</a><br/><hr>
  </p>
  </div>
  
  <div class="intro-img-border">
  <div class="intro-img-bevel">
  <div class="intro-img-index-box">
  <img class="intro-img-index" src="{{ASSET_PATH}}/{{post.article_img}}"/>
  </div>
  </div>
  </div>
  
  </div>
  {% endfor %}
</div>
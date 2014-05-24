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
  <small>{{ post.tagline }}</small></h3>
  {{ post.content | strip_html | truncatewords:40 }}<br>
  <a href="{{ post.url }}">Read more</a><br/><hr>
  
  </div>
  
  <div class="intro-img-border">
  <div class="intro-img-bevel">
  <div class="intro-img-index-box">
  <img class="intro-img-index" src="{{ASSET_PATH}}/{{post.article_img}}" title="{{post.article_img_title}}"/>
  </div>
  </div>
  </div>
  
  </div>
  {% endfor %}
</div>
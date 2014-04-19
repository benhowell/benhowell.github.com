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
  <h3><a href="{{ post.url }}">{{ post.title }}</a></h3><p><small><strong>{{ post.date | date: "%B %e, %Y" }}</strong><br>
  {{ post.content | strip_html | truncatewords:75 }}<br>
  <a href="{{ post.url }}">Read more...</a><br><br><hr>
  {% endfor %}
</div>

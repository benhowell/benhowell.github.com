---
layout: default
title: Ben Howell
tagline: Supporting tagline
---
{% include JB/setup %}

<div class="blog-index">
  {% for post in site.posts %}
  <h3><a href="{{ post.url }}">{{ post.title }}</a></h3><p><small><strong>{{ post.date | date: "%B %e, %Y" }}</strong> . {{ post.category }} .
  {{ post.content | strip_html | truncatewords:75 }}<br>
  <a href="{{ post.url }}">Read more...</a><br><br>
  {% endfor %}
</div>

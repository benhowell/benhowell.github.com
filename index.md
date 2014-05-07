---
layout: default
title: Ben Howell
tagline: Supporting tagline
---
{% include JB/setup %}
<br/>
<br/>

{% for post in site.posts %}

<div class="intro">

<div class="intro-txt">
<h3><a href="{{ post.url }}">{{ post.title }}</a></h3><br/>
<strong>{{ post.date | date: "%B %e, %Y" }}</strong><br/>
{{ post.content | strip_html | truncatewords:40 }}<br/>
<a href="{{ post.url }}">Read more</a>
</div>

<div class="intro-img-border">
<div class="intro-img-bevel">
<div class="intro-img-sml">
<img class="intro-img-small" src="{{ASSET_PATH}}/bootstrap/img/eventbus_250.jpg"/>
</div>
</div>
</div>

</div>
<div><hr class="mini-social-hr"></div>
{% endfor %}
---
layout: post
title: "The Hero Programmer and His Dark Secret"
description: ""
tagline: ""
category: 
tags: []
article_img: bootstrap/img/regex.png
article_img_title: Regular Expressions and the hero programmer. From the webcomic xkcd.
article_img_alt: "Our hero programmer saves the day by typing a PERL regular expression on a keyboard whilst swinging past on a vine in a tarzan like fashion. From the webcomic xkcd: http://xkcd.com/208/"
reddit_url:
hn_url:
---
{% include JB/setup %}
<div class="intro">
<div class="intro-txt">
<p>
Most of us have worked somewhere or know of a business that has a resident "hero programmer". The image of the hero programmer who regularly saves the day with his incredible intelligence and dedication when seemingly random unforseen problems arise is often celebrated and idealized. Unfortunately, the truth is much uglier. 
</p>
<p>
If your business relies on a hero programmer or you're currently looking for one, you need to take a step back, analyse the situation and think about a world where hero's aren't needed.
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
<br/>
<br/>

#### Safeguards
single point of failure, failover,

A single point of failure (SPOF) is a part of a system that, if it fails, will stop the entire system from working.[1] They are undesirable in any system with a goal of high availability or reliability, be it a business practice, software application, or other industrial system.

redundancy 
One would normally deploy a load balancer to ensure high availability for a server cluster at the system level.


#### Documentation
documented processes,

Documentation is an important part of software engineering. Types of documentation include:

Requirements - Statements that identify attributes, capabilities, characteristics, or qualities of a system. This is the foundation for what shall be or has been implemented.
Architecture/Design - Overview of software. Includes relations to an environment and construction principles to be used in design of software components.
Technical - Documentation of code, algorithms, interfaces, and APIs.
End user - Manuals for the end-user, system administrators and support staff.
Marketing - How to market the product and analysis of the market demand.

Module and system documentation: Not code documentation but process, component and system level documentation (i.e. what each is supposed to do, who with and how each interact, detailed documentation describing inputs and outputs of all).



#### Process
documented software process (how modules/components are supposed to be written, what contracts and/or interfaces they must comply with, what conventions should be used (naming, implemented functionality, API specifications, etc). create templates, create generators that produce those templates.

A good set of development processes will minimise code obfuscation and reduce the dreaded [big ball of mud][1]

#### Testing

#### Deployment

#### Result

There are far easier ways to improve results than go looking for that one superhero developer that will save your company: how about taking the leash of the developers you already have? If they’re half decent, chances are your results will sky-rocket.

Transition your software development ecosystem to a safe, documented, process driven and tested environment. Task new software engineers with studying and understanding this material when they arrive and task employed engineers with creating and maintaining this ecosystem.



testing and deployment pipeline, continuous integration, etc.



[1]:http://en.wikipedia.org/wiki/Big_ball_of_mud
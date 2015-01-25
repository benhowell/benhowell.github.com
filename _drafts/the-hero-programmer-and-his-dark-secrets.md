---
layout: post
title: "The Hero Programmer and His Dark Secrets"
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
Most of us have worked somewhere or know of a business that has a resident "hero programmer". The image of the hero programmer who regularly saves the day with his incredible intelligence and dedication when seemingly random unforseen problems arise is often celebrated and idealized. Day or night, the hero programmer is ready to sort things out when the not so unusual disaster strikes.  Unfortunately, the truth is much uglier. 
</p>
<p>
The truth is, there will always be a time when a hero programmer needs to save the day but if this is a familiar scene within your workplace then something needs to change. If your business relies on a hero programmer or you're currently looking for one, you need to take a step back, analyse the situation and think about a world where the hero programmer isn't needed.
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

#### Software Process
Whether you're business relies on a single developer or an entire team of engineers, a consistent, structured software process is a must. In house standards and conventions can greatly ease the understanding of software between multiple programmers by creating a shared understanding of the underlying rules on which the software is constructed. Transistion of, responsibility for, and extension of software systems from one programmer to another becomes much more straight forward when a well developed process is in place.

Develop a software process that defines the rules for how modules/components are supposed to be written, what contracts and/or interfaces they must comply with, what naming conventions should be used for all levels of development (e.g. namespace, module, class, function, variable, etc.). Specify what fucntionality needs to be provided by certain types of module, what API hooks, metadata and logging output should be supplied. Try to automate as much of this process as possible by creating templates that include the minimum requirements. Create generators that produce those templates and/or automagically generate class files and you're well on your way to a much less obfuscated code base or set of systems.

When well engineered processes for system development are in place, the risk of catastrophic failure is reduced because your system has a greater internal consistency, the way things interract within the system is better understood and analysable and there is a template for other developers to understand how the system hangs together because they know how it was made and why certain engineering decisions were made. A good set of development processes will minimise code obfuscation and reduce the dreaded [big ball of mud][1]



#### Safeguards

use version control

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

Document code changes as detailed notes on what and why for every checkin (of course you use version control, don't you?)




#### Testing
http://en.wikipedia.org/wiki/Software_testing

#### Deployment
simple, easy, repeatable, REVERSIBLE deployment process.

#### Result

There are far easier ways to improve results than go looking for that one superhero developer that will save your company: how about taking the leash of the developers you already have? If they’re half decent, chances are your results will sky-rocket.

Transition your software ecosystem to a safe, documented, process driven and tested environment. Task new software engineers with studying and understanding this material when they arrive as a part of your induction process and task employed engineers with creating and maintaining this ecosystem.



testing and deployment pipeline, continuous integration, etc.



[1]:http://c2.com/cgi/wiki?BigBallOfMud
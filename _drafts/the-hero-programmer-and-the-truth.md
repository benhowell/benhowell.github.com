---
layout: post
title: "The Hero Programmer and the Truth"
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
The truth is, there will always be a time when a hero programmer needs to save the day but if this is a familiar scene within your workplace then something needs to change. If your business relies on a hero programmer or you're currently looking for one, you need to take a step back, analyse the situation and think about a world where the heroics aren't needed.
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
<br/>
<br/>

#### Safeguards
You must always be able to both return to a former good, working state _and_ quickly move to a complete replica of the current state in a seamless way with minimal effort at all levels of software development, deployment and systems operation.

In order to return to a former good working state during development, system deployment or to rollback your production systems to a former release, there is simply no excuse to have not moved to a version control system (VCS) at some point this century. Version control is a must and the choice of free, stable and secure self or 3rd party hosted solutions is so large you'd be insane to not use one. You absolutely _need_ to be able to roll back your systems to a former, stable version and/or state whether they be web based, server or desktop applications and whether or not it's a development or production environment. If you're using a single code base hosted on a server, desktop or laptop, if you're coding on the live system, if your developers are exchanging code by email then you're in for a world of hurt if you aren't already in a complete mess. **STOP**. Move your code to a version control system now. Even if you're a single dev sitting in a basement writing your minimum viable product, stop and put your hard work into a VCS right now.

If your business runs centrally hosted systems such as servers (web, application, database, etc.) you need failover, especially if you're servicing external systems or clients. All businesses should strive for reliable, highly available and serviceable (RAS) systems (this is also true for non-software systems such as business processes, utility services and the like).

 * Reliability: The probability of a system producing correct results up to a given time _t_ (also called "Fault Tolerance").
 * Availability: The amount of uptime within a given time _t_ (often stated as a percentage of uptime per year).
 * Serviceability: How easy and quickly a system can be repaired.
 
Try to eliminate all common low-level single points of failure by introducing RAS in the following areas:

 * Software: Output data can be checked for corruption (e.g. checksums and tests, testing against output from redundant servers) (R). Data corruption, software and hardware faults, and errors reported on and/or recovery attempted (R). If a fault recovery fails or is not possible, the system may isolate the offending subsystems (R,A) or those faults may be reported to a monitoring system which may conduct a failover to redundant hardware with a replica of the current software system (R,A).
 * Hardware: Servers with [RAID][2] hard drive controllers can be configured in a highly redundant way so when one or more drives fails or becomes corrupted, mirrored, redundant drives can take over whilst at the same time attempting to rebuild the corrupt drives (A). Some servers can automatically log service jobs with varying degrees of urgency when faults and failures occur (R,S).
 * Server: Redundant servers allow you to implement failover systems where non-recoverable, system critical failures in software or hardware occur (R,A). Rack mounted servers with hot-swappable parts can be serviced and repaired without interrupting the operation of the server (A,S). A Load balancer can be used between two or more servers (serving the same systems/data/content) in order to distribute traffic/computation evenly across those servers as well as providing redundancy (R,A,S).
 * Power supply: [Uniterruptable power supplies (UPS)][3] protect against brown outs and full power failures (A) and for critical systems such as security and emergency control/radio rooms (I've worked in many) a completely redundant power supply in the form of a diesel generator may be used (A).




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
[2]:http://en.wikipedia.org/wiki/RAID
[3]:http://en.wikipedia.org/wiki/Uninterruptible_power_supply
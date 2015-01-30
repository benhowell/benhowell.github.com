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
The truth is, there will always be a time when a hero programmer needs to save the day but if this is a familiar scene within your workplace then something needs to change. If your business relies on a hero programmer or you're currently looking for one, you need to take a step back and think about a world where the heroics aren't needed.
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

When well engineered processes for system development are in place, the risk of catastrophic failure is reduced because your system has a greater internal consistency, the way things interract within the system is better understood and analysable and there is a template for other developers to understand how the system hangs together because they know how it was made and why certain engineering decisions were made. A good set of development processes will minimise code obfuscation and reduce the dreaded [big ball of mud][1] which most often produce unstable systems.
<br/>
<br/>

#### Safeguards
You must always be able to both return to a former good, working state _and_ quickly move to a complete replica of the current state in a seamless way with minimal effort at all levels of software development, deployment and systems operation.

In order to return to a former good working state during development, system deployment or to rollback your production systems to a former release, there is simply no excuse to have not moved to a [version control system (VCS)][4] at some point this century. Version control is a must and the choice of free, stable and secure self or 3rd party hosted solutions is so large you'd be insane to not use one. You absolutely _need_ to be able to roll back your systems to a former, stable version and/or state whether they be web based, server or desktop applications and whether or not it's a development or production environment. If you're using a single code base hosted on a server, desktop or laptop, if you're coding on the live system, if your developers are exchanging code by email then you're in for a world of hurt if you aren't already in a complete mess. **STOP**. Move your code to a version control system now. Even if you're a single dev sitting in a basement writing your minimum viable product, stop and put your hard work into a VCS right now.

If your business runs centrally hosted systems such as servers (web, application, database, etc.) you need failover, especially if you're servicing external systems or clients. All businesses should strive for reliable, highly available and serviceable (RAS) systems (this is also true for non-software systems such as business processes, utility services and the like).

 * Reliability: The probability of a system producing correct results up to a given time _t_ (also called "Fault Tolerance").
 * Availability: The amount of uptime within a given time _t_ (often stated as a percentage of uptime per year).
 * Serviceability: How easy and quickly a system can be repaired.
 
Try to eliminate all common low-level single points of failure by introducing RAS in the following areas:

 * Software: Output data can be checked for corruption (e.g. checksums and tests, testing against output from redundant servers) (R). Data corruption, software and hardware faults, and errors reported on and/or recovery attempted (R). If a fault recovery fails or is not possible, the system may isolate the offending subsystems (R,A) or those faults may be reported to a monitoring system which may conduct a failover to redundant hardware with a replica of the current software system (R,A).
 * Hardware: Servers with [RAID][2] hard drive controllers can be configured in a highly redundant way so when one or more drives fail or become corrupted, mirrored, redundant drives can take over whilst at the same time attempting to rebuild the corrupt drives (A). These days servers can have numerous redundant components besides disk arrays. Some servers can automatically log service jobs with varying degrees of urgency when faults and failures occur (R,S).
 * Server: Redundant servers allow you to implement failover systems where non-recoverable, system critical failures in software or hardware occur (R,A). Rack mounted servers with hot-swappable parts can be serviced and repaired without interrupting the operation of the server (A,S). A load balancer can be used between two or more servers (serving the same systems/data/content) in order to distribute traffic/computation evenly across those servers as well as providing redundancy (R,A,S).
 * Power supply: [Uniterruptable power supplies (UPS)][3] protect against brown outs and full power failures (A) and for critical infrastructure such as datacentres, security and emergency control/radio rooms (and I've worked in many), a completely redundant power supply in the form of a diesel generator may be used (A).
<br/>
<br/>

#### Testing
Testing is pretty important in all areas of your system, including code, system integration, process and procedure testing. Even emergency drills if you're dealing with critical systems. For system and integration testing, automation is paramount. Unit testing should be encouraged but you may also wish to look at a more rigourous Test Driven Development (TDD) or Behaviour Driven Development (BDD) philosophy, but whatever the case, make sure testing with good code coverage is used in the development of your systems. 

Deployment and integration testing

failover testing

redundancy testing

load testing

emergency procedure testing

process testing

<br/>
<br/>

#### Deployment
Automate as much of your deployment pipeline as possible. Test and debug your deployment pipeline whenever anything changes and keep it automated (don't be tempted to bypass anything in the pipline and just do it by hand). Automated deployments should run tests at each stage of rollout and halt deployment altogeher if something goes wrong, alert someone and provide a comprehensive log of what happened. Having successfully deployed an update or newer version of software does not mean there won't be problems. so you also need the ability to easily rollback the deployment process and revert to a previous version or state. 

A simple, easy, repeatable and reversible deployment process is key. Fully automated, fail-safe production deployments should be the goal. If you're interested in this topic and would like to see what's possible, read up on the topics of "continuous integration" [here][5] and [here][6] and "continuous delivery" [here][7] and [here][8].
<br/>
<br/>

#### Documentation
Documentation is _the_ most important part of any system and goes well beyond system design, code comments, technical and user manuals. 

Any system before it is built should have good design documentation. This includes things such as system requirements, capabilities and functionality of the system, input and output data requirements, number of concurrent users/processes the system is required to service, the architectural, computational and memory requirements, etc. Once development is underway, technical documentation in the form of code comments (class, structure, function, etc.) should be an integral part of day to day development. I know this is a [recurring rant]({% post_url 2014-09-09-the-forgotten-user %}), but honestly, I see code on a weekly basis that is completely void of comments, and unfortunately there seems to be a direct correlation between poor quality code and lack of comments. Module and system documentation is important too, and I don't mean code documentation but process, component and system level documentation (i.e. what each is supposed to do, input sources, output expectations, how modules interact, detailed documentation describing inputs and outputs of all interacting components within the system). Also try to include the necessary markup in your code comments to generate system docuentation for LaTeX, Doxygen, Javadoc, or whatever. For much more detail on what good commenting should entail, please see [my previous post on the matter]({% post_url 2014-09-09-the-forgotten-user %}). Lastly, don't forget about user documentation. It doesn't matter whether or not your systems users are people or other systems, you still need to document _how_ to use the system, such as interface contracts, API documentation and the like.

Nothing beats thorough process documentation, so the same level of attention to detail that you _should_ be putting into code and system documentation should also be put towards everything else. Document all safety systems, redundancy, failover and contingency plans. Hardware documentation such as technical and user manuals. These types of documentation are usually supplied by the hardware vendor but they should be filed away in a place that is documented and known by all for quick access). Failover process documentation is a must, and even if the whole system is automated, you should document the step-by-step process of how to perform a failover (move operations from one set of hardware to another) as a form of redundancy if the automatic failover itself fails. Testing and deployment should also get full attention.

Taking all this to the extreme, you could produce an "emergency operating procedure" process. Just like airline pilots use, you could produce an operating handbook for procedures to use during emergencies (whether that be software, hardware, power, data, whatever). If your systems are super critical then it may be worth the investment.
<br/>
<br/>


#### Conclusion
Of course, I've taken some things to the extreme in this article, but every business is different and therefore the need (and cost) to avoid technical debt, system breakdown and critical outages vary significantly. However, there are far easier ways to safeguard your business systems against failure than to employ a superhero programmer. Transition your software ecosystem to a safe, documented, process driven and tested environment. Task new software engineers with studying and understanding this material when they arrive as a part of their induction process and task employed engineers with creating and maintaining this ecosystem. The sad truth is, competent and professional systems engineers may never be recognised for their intellectual talent because they never have to save the day against disaster due to their thorough planning and risk averse practices. Your true superhero programmers are the ones who set your business on the path to Reliability, Availability and Serviceability.





[1]:http://c2.com/cgi/wiki?BigBallOfMud
[2]:http://en.wikipedia.org/wiki/RAID
[3]:http://en.wikipedia.org/wiki/Uninterruptible_power_supply
[4]:http://en.wikipedia.org/wiki/Revision_control
[5]:http://en.wikipedia.org/wiki/Continuous_integration
[6]:http://www.thoughtworks.com/continuous-integration
[7]:http://en.wikipedia.org/wiki/Continuous_delivery
[8]:http://www.thoughtworks.com/continuous-delivery
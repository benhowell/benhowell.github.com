---
layout: post
title: "Asleep at the Wheel"
description: "Asleep at the Wheel"
tagline: "craft"
category: craft
tags: [craft, design, craftsmanship, compsci, programming, coding]
article_img: bootstrap/img/Sleeping-Hamster.jpg
article_img_title: Asleep at the Wheel by Unknown
---
{% include JB/setup %}
<div class="intro">
<div class="intro-txt">
<span markdown="span">
**Hamster Wheel**
</span>

/ˈhæm.stər wiːl/ 

<p>
<span markdown="span">_A monotonous, repetitive, unfulfilling activity, especially one in which no progress is achieved.[^1]_</span>
</p>

<p>
<span markdown="span">[Like other rodents, hamsters are highly motivated to run in wheels][1].</span> Likewise, programmers in the early stages of their careers are highly motivated to run in wheels too. Digging into as many languages, libraries and frameworks they can get their hands on, building stuff using whatever happens to be popular this month and working insane hours either on the job, or at home after work and on weekends until stupid o'clock. 
</p>

<p>
As time goes on, it appears more developers are concerned about their future in the industry if they don't run that hamster wheel hard and play, build or learn each new technology that comes along for fear of being left behind. This is not only impossible to begin with, but over time, not sustainable, not healthy and certainly not the way to hone ones craft.
</p>

</div>
<div class="intro-img-border">
<div class="intro-img-bevel">
<div class="intro-img">
<img class="article-image" title="{{page.article_img_title}}" src="{{ASSET_PATH}}/{{page.article_img}}"/>
</div>
</div>
</div>
</div>
<br/>
<br/>

#### Experience 
If there is one thing experience teaches a developer, it is that you _will_ burn out. Possibly many times. The older one gets, the less motivation and capacity one has to stay up late every evening burning the midnight oil. Devoting ones life to the pursuit of learning yet another short lived framework or niche language to the exclusion of other physically, emotionally or mentally stimulating activities that aren't programming is the fast track to unhappiness and burn out. Spend your weekends doing other fulfilling activities that, no matter what they are, tangentially broaden your mind and expand your knowledge and experience as a human being. Doing this is bound to improve your skills as a developer in ways that cannot be predicted. Software is for humans, not computers. With more experience, one can reliably determine whether something is worth incorporating into their work or not. Unless and until _new tech X_ is set to dramatically improve ones work, there is no reason to invest. When the time does come to invest in a new language, framework or library however, you will be able to learn it in a week or two when the need arises. Experience forces developers to work smarter, not harder.
<br/>
<br/>

#### Knowledge
So, how do I gain the experience to know what is worth

Learn paradigms and principles. If you know what to do, you will be able to find the tools to do it.

Its a bit like cooking - the cookbooks for the peasants give recipes. The ones for the chefs give mechanics and techniques.
So learn pointers, functional programming, asynchronous operations and few more important paradigms, learn how to keep a code base tidy and organized and just ignore the foam on the water that is the hot new tech. You will be able to learn it in a week when need arises.

focus on theory, and bolster that by building things. Constantly. If there's one lesson I could hope to impart to any budding developer, it's to never stop building. Learn a bit about genetic algorithms - then implement a simple one. Learn a little Ruby, then build something with it. This will teach you a lot of what you need to know - fill in the gaps with Google. Focus on building good abstractions, and you'll be fine wherever you go.



Read the AOSA book, read Design Patterns, and read something like Domain-Driven Design. Realize that no-architecture is frequently chosen due to ignorance ('pragmatism'). Know SOLID back and forth. Understand why people strive to isolate things when doing TDD. Recognize and know how to decrease coupling, and what the costs of that are. You can practice these concepts every day, and you should, if only to develop the necessary element of taste.
You'll go through a phase where you adhere to these ideas religiously, then eventually assimilate the knowledge so that it becomes almost unconscious.

I've certainly noticed a shift in that these hard learnt lessons are being ignored in order to add features to frameworks etc; which increase their applicable scope, with the increase in complexity that arises. The primary driver of which appears to be the younger generations desire to use the same tools for different purposes rather than learn a new set of applicable tools for a different problem domain. A good example is the shift from v1 Zend Framework to Zend Framework 2 which just appears to be so heavily "Java/Enterprise" influenced I simply refuse to use it; its far far too complex a framework for what is essentially a framework for building web sites. Building web sites is simple. These modern frameworks make it massively more complex than it needs to be.

Which is why simplicity remains the ultimate sophistication in software architectures. At some point, you cannot continue writing code without structure. Then you slowly and deliberately introduce meaningful abstractions along the necessary dimensions.
And no more.
Abstractions are also useful to separate the arrangement of work from the actual work itself. These boundaries are useful for dividing up work among colleagues.

Knuth argues that programming is a form of writing. I agree. Therefore, you should organize code like a book or article, since those are forms of writing that have benefitted from centuries of use and refinement.
Even books and articles on complex subjects are fairly light on structure.

I had pretty much the same experience when learning functional programming. However, I don't think it was functional programming per se that caused a jump in my ability to design  software. It was the exposure to a dual perspective.
The difference is subtle but important. You need to master imperative programming for the exposure to functional to produce a  evolution. You can probably get the same effect in the reverse path (functional first, imperative next) but it's quite uncommon as a learning path.
The same reasoning leads me to advise exposure to logic languages (prolog and the like), to compiler design (a complex state machine) and to a complete algebraic abstraction (relational algebra is probably the best candidate). Each will bring into your toolbox an important top level tool, no matter which paradigm you use at any moment in your life.


Note that a lot (most?) comp sci programs don't necessarily address design principles. If you're in a software engineering track they should, but my curriculum was mostly algorithms, data structures, operating systems, and programming languages. My senior year I had one class where design and project management was really a topic.



So read books, build things and read code.






Rather than seeing each new technology as exciting, the experienced programmer may well ignore it altogether[^1] as he may have seen it before in many different guises.



Relax. A lot of those technologies will be dead. And you should be intimidated by angular and ember. They have the smell of Enterprise Java for me.




I relate to this quite strongly. Whether you like it or not, a software career forces you into being a generalist eventually. Technology changes dramatically every few years - unable to improve what we have, we do entire revolutions to make incremental progress, and in the process, we make everybody's skills stale. The only things that survive are the principles that are common to everything. There's nothing you can do about that. And once you end up a generalist it is very hard to be appreciated for your wide breadth of general skills. People want the best person for exactly the job they are about to do, very few take a long view, very few even have the luxury to do that.
From another perspective, we are in unknown territory here. Us 40+ developers are at the vanguard of a generation of software people who have no trail blazed before them to tell us where our careers should go. There simply isn't a widespread professional software industry of 50+ and 60+ years old for us to look at and say "that will be me in 10 years". We were largely the first generation for whom software was a major industry. I take comfort from this because in all likelihood it will work out better than our worst fears would have us imagine. It is as much the lack of example and uncertainty as the reality that makes us feel insecure.


as you gain more experience, (which can correlate to the older you get, especially when you start early), you get better at knowing when something will really improve your work (making it faster, better looking, more enjoyable, better performing, more efficient, etc.) and when it won't. Perhaps the article's author is actually on to something. He intuitively knows New Tool X won't improve his work much, and he's wiser than his younger self, who would waste time chasing non-essential niceties.
<br/>
<br/>







Mind-bending logical gymnastics 
 
 
 
Dependency Injection frameworks, Javascript application frameworks, DOM manipulation libraries, SQL providers and frameworks, NoSQL solutions, OO languages, FP languages, concurrent and parallel languages and frameworks, to name but a few tricial examples from the top of ones head. Not sustainable. 
 
 



As I’ve gotten older, I don’t stay up so late anymore.
I think about how I used to fill my time with coding. So much coding. I was willing to dive so deep into a library or framework or technology to learn it.

My tolerance for learning curves grows smaller every day. New technologies, once exciting for the sake of newness, now seem like hassles. I’m less and less tolerant of hokey marketing filled with superlatives. I value stability and clarity.


new JS hotness (ES6, Angular, Ember, Shadow DOM, Module systems, etc) feels woefully lightweight. 

What I’m most scared of, though, is being left behind.


Now I feel terribly specialized, and there doesn’t seem like much point to exploring those things if I’m not going to actually use it in my day job — especially when there are few other opportunities to do anything with the tech.

I’m scared that either the job “web developer” is outpacing me, or my skills are atrophying.

Where will I be in 10 years? I don’t know. I hope I still will have some in-demand skills to pay the bills. But it feels like all I see are DevOps and JavaScript, and I know less and less every day about those things.

I hope I still have something to offer. I don’t know what it will be, though.
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 





Unless for need, interest or fun, do not learn like-for-like frameworks and languages in a vain effort to keep up. Instead learn to abstract and/or generalise the relevent benefits and technologies behind the latest hipster framework and only learn what languages and frameworks _you_ want to learn. Another new MVC framework? Learn MVC instead.


#### Asleep at the Wheel
Learn theory and technique, gain a deeper and fundamental knowledge. Get off the hamster wheel.



[1]:http://en.wikipedia.org/wiki/Hamster_wheel
[2]:http://en.wiktionary.org/wiki/hamster_wheel


#### Notes
[^1]:[http://en.wiktionary.org/wiki/hamster_wheel][2]
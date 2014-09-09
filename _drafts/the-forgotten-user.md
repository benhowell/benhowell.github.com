---
layout: post
title: "The Forgotten User"
description: "The Forgotten User"
tagline: "craft"
category: craft
tags: [craft, design, craftsmanship, compsci, programming, coding]
article_img: bootstrap/img/forgotten-grave-abandoned-japan.jpg
article_img_title: Forgotten Grave – Abandoned Japan by Kurt Bell
reddit_url:
hn_url:
---
{% include JB/setup %}
<div class="intro">
<div class="intro-txt">
<p>
We've all heard stories of projects gone wrong because somehow everyone forgot about the users, haven't we? I want to draw your attention to another user who is almost always forgotten in every other bit of coding we all do everyday. That user is the future programmer. That programmer may be you. 
</p>
<p>
I'm you all know how much it sucks to work on other peoples code when the next programmer has been forgotten or ignored<span markdown="span">[^1]</span>. Picking up a previously worked on project, legacy code base, library, custom DSL or what have you is normally a pain in the arse at best, and at worst, a rage inducing, blood boiling exercise. It would be bad enough if you were to inflict this upon yourself, but the ultimate insult is having to support and hand-hold some other poor sod lumped with one of your prior projects where you had ignored the future programmer. 
</p>
<p>
Let's modify our routine a bit and see if we can alleviate some of that pain, shall we?
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

#### Set up
Please make your code easy to deploy and run/install. No future programmer wants to have to first find and then compile umpteen dependencies each requiring their own specific compiler version, each with special compiler flags. Make your stuff easily portable and runnable so your future programmers don't have to spend a week building it. Host the dependencies and do the compilation for them, and if need be, give them your special precompiled binary dependencies for the architectures you support. If there needs to be some configuration and placement of files, dependencies or packages for the software to run, then write up a quick installer or a little script to do it for them. 

If possible, allow your software run wherever it is placed (i.e. in a compressed file that can be unpacked wherever the future programmer chooses) as an alternative to installation. For Unix and Linux based operating systems you can use their various package management systems to manage the installation of your software. Finally, if you must make your users compile _anything_ then try to at least keep the tooling to a minimum (e.g. don't make them use numerous derivatives of make or a number of different compilers or versions thereof). 

Do as much of the heavy lifting as you can for your user to get them up and running because unless they absolutely have to use your stuff, any burden here will drive them to an easier solution, and if they are stuck with your overly complex and painful build methods then they will resent you.
<br/>
<br/>

#### Documentation
Yes this is an oft-repeated rant but please just comment your code. What about self documenting code? Well sure, we all try to do that right? But bang for buck? Plain old written factual discourse. And I'm not talking about blindingly obvious comments ("The Car class is a class that represents a car"). Write some prose on the purpose of the file or class both in and of itself, and in the greater scheme of things with respect to your software. Note any special cases, things that may not be obvious and even give an example or two if it helps right there in the comments. If there are references that may be of use to the future programmer then add them here as well (i.e. links to standards or protocols that may apply, specifications that may be relevant, examples or other code on which the underlying code may be based or even other classes within your software that make heavy use of the code). Please take the same consideration with your functions and/or methods and tell your users some stuff about what each of the arguments are and what's returned from the function. 

The purpose of the code may not be clear. There could be domain specific assumptions or language that your users are unaware of, you could be writing non-intuitive code for performance reasons or to get around a bug in 3rd party code. You might be implementing an algorithm from a scientific paper or a mathematical formula in which case you should link to the paper and preferably include the formula right there in your comments.

Please also comment private and/or protected methods, internal functions and the like as your users still need to know this stuff. For bonus points, mix in a little markup and hey presto! You've got LaTeX/Doxygen/Javadoc/whatever.
<br/>
<br/>

#### Conventions and variable and function naming
Use nice descriptive variable and function names. Don't use one, two, whatever number of characters in place of a real, actual descriptive name unless it is a well known convention (iterators, indicies, exceptions, etc.). Please don't use some initialism or acronym that's only apparent to you. If coming up with a descriptive variable or function name is hard then perhaps you should rethink your code. Don't replicate variable names where the context is not the same and avoid globals and unneccessary state like the plague.
<br/>
<br/>

#### Abstraction and generalisation
Is there really a need for a factory, an abstract class, an interface class, and a WhateverImpl class for a single object type when there are no other generalisations of it's superclass and/or implementations of it's implemented interface? Please don't create multiple levels of abstraction and or generalisations and inherited or extended implementations when it's not necessary. Don't make your future programmers trapse backwards and forwards all over your code in order to figure out the behaviour of what should have been a simple class or file.
<br/>
<br/>

#### Extensibility and reuse
I've witnessed the development of a module that took almost 12 months for a job that should've taken maybe 2 or 3 simply because of a broken and completely misguided adherence to the "reuse mantra"[^2]. The reuse thing was taken so far in this particular case that every class had more than one constructor, and in the worst case, 11 constructors! Why? Not only was there way more code than there needed to be but it also made the module harder to follow, read and reason about, and on top of that, exposes the system to much greater risk as far as introduction of faults is concerned.

Don't write your code for reuse if you don't know if, who or how your code will be reused. Oh, if there is one useless tenet of modern programming perpetuated over the last 20 years I could eliminate, it would be this. Don't write reusable code if you don't know if your code will be reused. If you're making premature design decisions based on hypotheticals of situations of reuse of which you have no idea about, then you're making your code more complex than it has to be. Rather than considering your future programmers, you're really just rolling an extra layer of obfuscation around your stuff for them to decipher later on. Don't prematurely design for reuse, instead, refactor later.
<br/>
<br/>

#### Frameworks, bloat and the antithesis of simplicity
I've mentioned this before in [Code Incomplete]({% post_url 2014-06-25-code-incomplete %}) and again in [Asleep at the Wheel]({% post_url 2014-07-11-asleep-at-the-wheel %}) but it's worth reiterating again in the context of this article. Many software systems have an overwhelming amount of complexity added by developers using numerous frameworks and libraries for only small pieces of functionality inherent in each. Do you really need an ORM for a simple application with a handful of database queries? Do you really need to use a MVC framework for a simple website or application? Doing so is the antithesis of simplicity. What initially seems like a time saving choice sometimes turns out to be a straight-jacket of conformity that costs you more time in the long run. Think carefully and make informed decisions about when and when not to use frameworks and libraries. Build the simplest software that satisfies your needs.
<br/>
<br/>

#### Conclusion
Investment in making things a bit easier for your future programmer is a worthwhile and not too onerous task. All it takes is a small shift in routine that soon becomes second nature in the flow of things. Make the job of picking up your code and getting productive with it as easy as you can for your future programmer as that programmer may be you (or $deity forbid, supported by you). Of course, what I've written here is not a set of non-negotiable rules cast in stone. There are many circumstances where the advice here is not appropriate or warranted so know when to ignore advice too.

**The code you write is for humans**. If we only cared about coding for computers we'd be writing assembler or binary (apologies in advance to those who program in assembler). Modern day programming languages, and therefore your code, is solely written for humans to read so please don't forget the user!
<br/>
<br/>

#### Notes
[^1]: Ignored for whatever reason, whether that be accidental neglect or a deliberate trade-off.
[^2]: I may have to write a future article about bureaucracy...

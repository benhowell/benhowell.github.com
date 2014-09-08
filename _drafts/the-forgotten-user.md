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
We've all heard stories of projects gone wrong because somehow everyone forgot about the users, haven't we? In this article I want to draw your attention to another user who is almost always forgotten in every project, every product and every other bit of coding we all do everyday. That user is the future programmer. That programmer may be you. 
</p>
<p>
I'm sure everyone reading along knows how much it sucks to work on other peoples code when the next programmer has been forgotten or ignored<span markdown="span">[^1]</span>. Picking up a previously worked on project, a legacy code base, another library, a custom DSL or what have you is normally a pain in the arse at best, and at worst, a rage inducing, blood boiling exercise that takes the patience of a saint lest damage be inflicted on objects within your vacinity.
</p>
<p>
It would be bad enough if you were to inflict this upon yourself, but for the ultimate worst case scenario, imagine having to support and hand-hold some other poor forgotten user lumped with one of your prior projects where you had forgotten or ignored the future programmer.
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
Please make your code easy to deploy and run/install. No future programmer wants to have to first find and then compile umpteen dependencies each requiring their own specific compiler version, each with special compiler flags. Make your stuff easily portable and runnable so your future programmers don't have to spend a week building it. Host the dependencies and do the compilation for them, and if need be, give them your special precompiled binary dependencies for the architectures you support. If there needs to be some configuration and placement of files, dependencies or packages for the software to run, then write up a quick installer or a little script with an install.txt to do it for them. 

If possible, make your software run wherever it is placed (i.e. in a compressed file that can be unpacked wherever the future programmer chooses). Finally, if you must make your users compile _anything_ then try to at least keep the tooling to a minimum (e.g. don't make them use numerous derivitives of make or a number of different compilers or versions thereof). Do as much of the heavy lifting as you can for your user to get them up and running because unless they absolutely have to use your stuff, any burden here will drive them to an easier solution, and if they are stuck with your manual build methods then they will resent you.
<br/>
<br/>

#### Documentation
Please comment your code. What about self documenting code? Well, we all try to do that, sure, but bang for buck? Plain old written factual discourse. And I'm not talking about blindingly obvious comments ("The Car class is a class that represents a car"). Write some prose on the purpose of the file or class both in and of itself, and in the greater scheme of things with respect to your software. Note any special cases, things that may not be obvious and even give an example or two if it helps right there in the comments. If there are references that may be of use to the future programmer then add them here as well (i.e. links to standards or protocols that may apply, specifications that may be relevent, examples or other code on which the underlying code may be based or even other classes within your software that make heavy use of the code).

Please take the same consideration with your functions and/or methods and tell us some stuff about what each of the arguments are and what's returned from the function. Please also comment private and/or protected methods, internal functions and the like as your _future programming user still needs to know this stuff_. Mix in a little markup and hey presto! you've got Latex/Doxygen/Javadoc/whatever as an added bonus.


http://www.reddit.com/r/programming/comments/2fgqp8/whats_wrong_with_comments_that_explain_complex/


<br/>
<br/>

#### Conventions, variable and function naming.
Please don't reuse and/or use duplicates variable names throughout your code. Use nice descriptive names, don't use one, two, whatever number characters in place of a real, actual descriptive name. Please don't use some initialism or acronym that's only apparent to you. Hint: if coming up with a descriptive variable name is hard then perhaps you should rethink your code.
<br/>
<br/>






#### Abstraction and generalisation
Please don't create multiple levels of abstraction and or generalisations and inherited or extended implementations when it's not necessary. Is there really a need for a factory, an abstract class, an interface class, and a WhateverImpl class for a single object type when there are no other generalisations of it's superclass and/or implementations of it's implemented interface? Don't make your future users trapse backwards and forwards all over your code in order to figure out the behaviour of what should have been a simple class.
<br/>
<br/>

#### Extesibility and reuse
I had a programmer working on a task for me in the past that took almost 12 months for a job that should've taken 2 simply because of a broken and completely misguided and misinterpreted adherence to the "reuse mantra"[^2]. The reuse thing was taken so far in this particular case that every class had more than one constructor, and in the worst case, 11 constructors! Why? Not only was there way more code here than needed to be which not only made the module harder to follow, read and reason about, it is also playing with fire as far as introduction of faults is concerned. I'm shaking my head as I write this as it still has an effect on me and this happened more than 5 years ago now.

Don't write your code for reuse if you don't know if or who or how your code will be reused. Oh, if there is one useless tennet of modern programming perpetuated over the last 20 years I could eliminate, it would be this. Don't write reusable code if you don't know if your code will be reused. If you're making premature design decisions based on hypotheticals of situations of reuse of which you have no idea about, then you're probably making your code more complex than it has to be. Rather than considering your future programmers, you're really just rolling an extra layer of obfuscation around your stuff for them to decipher later on. Don't prematurely design. Refactor later.
<br/>
<br/>

#### APIs and public interfaces
Don't ram some RESTful schema down the throat of your future users. **look up reference** 
<br/>
<br/>

#### Elegant code
Give me documented code please.
<br/>
<br/>


#### Frameworks, bloat and stuff.





#### Conclusion
If we only cared about coding for computers we'd be writing assembler or binary (advanced apologies to those who program in assembler). Modern day programming languages, and therefore your code, is solely written for humans to read so please don't forget the user! 
<br/>
<br/>

#### Notes
[^1]: Ignored for whatever reason, whether that be accidental neglect or a deliberate trade-off.
[^2]: Yes I checked in on this person regularly, and yes their actual output and time taken was completely out of my hands. I may have to write a future article about beauracracy...
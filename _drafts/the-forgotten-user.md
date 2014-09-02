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
We've all heard stories of projects gone wrong because somehow everyone forgot about the users! But I want to draw your attention to another user who is almost always forgotten in every project, every product and every other bit of coding we all do everyday. That user is the future programmer. That programmer may be you. 
</p>
<p>
I'm sure everyone reading along knows how much it sucks to work on other peoples code<span markdown="span">[^1]</span> when the next programmer has been forgotten. Picking up a previously worked on project, a legacy code base, another library, a custom DSL or what have you is normally a pain in the arse at best, and at worst, a rage inducing, blood boiling exercise that takes the patience of a saint lest damage be inflicted on objects within your vacinity.
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

#### Documentation
Please comment your code. Please. Self documenting code? Yeah, we all try to do that, but bang for buck? Plain old written factual discourse. And I'm not talking about blindingly obvious class comments ("The Car class is a class that represents a car"). Write some prose on the purpose of the file or class both in and of itself, and in the greater scheme of things with respect to your software. Note any secial cases, things that may not be obvious and even give an example or two if it helps right there in the comments. If there are references that may be of use to the future programmer then add them here as well (i.e. links to standards or protocols that may apply, specifications that may be relevent, examples or other code on which the underlying code may be based or even other classes within your software that make heavy use of the code).

Please take the same consideration with your functions and/or methods and tell us some stuff about what each of the arguments are and what's returned from the function.
<br/>
<br/>


#### Running
install, run and deploy. docs and script. not CI or continuous deployment pipeline. don't make me compile umpteen other dependencies from source with special flags and specific compiler versions.
make it easily portable and runnable so i don't have to spend a week building it. make your tooling easy, don't make me use cmake, nmake, gcc and msvs compiler for a single project.
<br/>
<br/>


#### Abstraction and generalisation
Please don't create multiple levels of abstraction and or generalisations and inherited or extended implementations when it is not necessary. Is there really a need for a factory, an abstract class, an interface class, and a whateverImpl class for a single object type when there are no other generalisations of it's superclass and/or implementations of it's implemented interface? Don't make me trapse backwards and forwards all over your code in order to figure out the behaviour of what should have been a simple class.
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

#### Conventions, variable and function naming.
Please don't reuse and/or use duplicates variable names throughout your code. Use nice descriptive names, don't use one, two, whatever number characters in place of a real, actual descriptive name. Please don't use some initialism or acronym that's only apparent to you. Hint: if coming up with a descriptive variable name is hard then perhaps you should rethink your code.
<br/>
<br/>

#### Conclusion
If we only cared about coding for computers we'd be writing assembler or binary (advanced apologies to those who program in assembler). Modern day programming languages, and therefore your code, is solely written for humans to read so please don't forget the user! 
<br/>
<br/>

#### Notes
[^1]: Most of the time. Sometimes it's ok, sometimes it's easy, and, sometimes it's a pleasure. It is the pleasurable outcome I'm calling for here!
[^2]: Yes I checked in on this person regularly, and yes their actual output and time taken was completely out of my hands. I may have to write a future article about beauracracy...
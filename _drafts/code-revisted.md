---
layout: post
title: "Code Revisted"
description: "Code Revisted"
tagline: "craft"
category: craft
tags: [craft, design, improvement, evolution, craftsmanship]
article_img: bootstrap/img/master_craftsman.jpg
article_img_title: Bob Zajicek - Steady Hand at the Lathe by Fr. John Abraham
---
{% include JB/setup %}
<div class="intro">
  <div class="intro-txt">
    ???????????????????????
  </div>
<div class="intro-img-border">
<div class="intro-img-bevel">
<div class="intro-img">
<img class="article-image" title="{{page.article_img_title}}" src="{{ASSET_PATH}}/{{page.article_img}}"/>
</div>
</div>
</div>
</div>


revisit old code. see how bad it is, wonder why you didn't use a more appropriate pattern, not understand why you use such strange data structures, wonder why it is so clunky, non functional, etc.

studying different langs along the way will make you see things in a different light


When I started at Amazon, I could recite for you all the incantations, psalms, voodoo chants and so on that I'd learned, all in lieu of intelligence or experience, the ones that told me Multiple Inheritance is Evil 'cuz Everyone Says So, and Operator Overloading Is Evil, and so on. I even vaguely sort of knew why, but not really. Since then I've come to realize that it's not MI that sucks, it's developers who suck. I sucked, and I still do, although hopefully less every year.


http://blog.codinghorror.com/why-im-the-best-programmer-in-the-world/


Bad developers, who constitute the majority of all developers worldwide, can write bad code in any language you throw at them.

That said, though, MI is no picnic; mixins seem to be a better solution, but nobody has solved the problem perfectly yet. But I'll still take Java over C++, even without MI, because I know that no matter how good my intentions are, I will at some point be surrounded by people who don't know how to code, and they will do far less damage with Java than with C++.

Besides, there's way more to Java than the core language. And even the language is evolving, albeit glacially, so there's hope. It's what we should be using at Amazon.

You just have to be careful, because as with any other language, you can easily find people who know a lot about the language environment, and very little about taste, computing, or anything else that's important.

When in doubt, hire Java programmers who are polyglots, who detest large spongy frameworks like J2EE and EJB, and who use Emacs. All good rules of thumb.


---
layout: post
title: "People Analytics and the Big Data Dystopia"
description: "The dystopian future and the transition from freedom and employment to compliance and deployment thanks to big data and people analytics."
tagline: "future"
category: future
tags: [future, big data, analytics, employment, big brother, privacy, anonymity, compliance]
related: [Step Away From the Kool Aid, Your Financial History. Your Business.]
article_img: bootstrap/img/dystopia.jpg
article_img_title: Nineteen Eighty-Four by George Orwell
article_img_alt: "An image from the movie Nineteen Eighty-Four"
reddit_url:
hn_url:
---
{% include JB/setup %}
<div class="intro">
  <div class="intro-txt">
    <p>
    Mainstream predictive analytics, at least in the "big data" sense, was once the reserve of business intelligence, marketing and speculative trading on the stock market but things are rapidly changing. For a lot of us, big data is about to become personal. Very personal indeed.
    </p>
    <p>
    People analytics, also referred to as workforce analytics, corporate analytics and HR analytics, is the application of predictive analytics on large volumes of data (henceforth referred to as "big data") to create behavioural and social profiles for individual people.
    </p>
    <p>
    Recruitment firms and some large employers have been using people analytics to qualify prospective candidates for quite some time, and some even have dedicated workforce analytics teams to monitor and analyse the activities of their staff. As with all things, the endless march of information technology is set to see this trend trickle down to all levels of employment in the future.
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

#### People Analytics
In this day and age, everyone expects that employers will do some online investigation of prospective employees, however, people analytics proves to be much more sinister. In addition to profiling potential employees using external data (public, paid-for, or otherwise), some employers continually analyse and profile existing staff using both external data and via analysis of internal social media, email, surveys, and the like. So, if you're filling out questionaires at work, using the corporate social networking tools, crowd-sourcing and ideation tools, using email, doing surveys, wikis, what have you, there may be additional reasons beyond engaging with staff, such as analysis and profiling. And, everything you're doing publicly online is bound to be used at some time in the future for profiling and analysis as well.

But really, that's all just tip of the iceburg stuff. According to an article in _"The Atlantic"_  [^1], the Las Vegas casino Harrah’s tracks the smiles of the card dealers and waitstaff (its analytics team has quantified the impact of smiling on customer satisfaction) and Bloomberg logs every keystroke of every employee along with every time they enter and leave the premises according to _"Business Insider Australia"_  [^2]. 

The people analytics software company Gild [^3] use an algorithm that scours the internet for open-source code in order to profile software engineers, and perhaps more alarmingly, the algorithm purportedly evaluates the code for its simplicity, elegance, documentation, and several other factors, including the frequency with which it’s been adopted by other programmers. Stack Overflow is also mined to analyse questions and answers by individuals and the breadth and popularity of that advice. Linkedin and Twitter are mined to analyse the language individuals use, and Gild have decided that certain phrases and words used in association with one another distinguishes expert software engineers from less skilled programmers. According to Gild, it knows _"these phrases and words are associated with good coding because it can correlate them with its evaluation of open-source code, and with the language and online behavior of programmers in good positions at prestigious companies"._  [^1]

There are many other people focussed analytics companies besides Gild such as Entelo and RemarkableHire, and it's unknown what data they collect, how their algorithms analyse that data, and what "score" they end up appointing any individual. Data collected about us and the profiles created from that data are not in our control. If you're a programmer and have ever used the internet, chances are that Gild and others have each already "scoring" you. [^4]
<br/>
<br/>

#### Causation 
In an article in _"The New York Times"_  [^5], Data Scientist, Dr. Ming said of the algorithms she is now building for Gild: _"Let’s put everything in and let the data speak for itself"_.

Big data produces correlates, uncovers trends and probabilities, and finds weak bonds between data. "A correlates with B" however, is not a legitimate form of argument as data can never _"speak for itself"_ because it is strictly quantitative. Correlation is simply not enough. Without causation there is no evidence to support any correlate (in big data or otherwise), and can lead one to believe something that is not true. A patently ridiculous example  [^6] of this has been compiled by Tyler Vigen which shows a 99.2% correlation between suicide by hanging, strangulation and suffocation in the US, and spending on science, space and technology.

People analytics does not provide causality. If there is no critical research to determine the causal mechanism that underlies correlations in data then there is no reason to believe that the result is anything other than mere coincidence. Determining causation from correlations and the causal nexus between them would be an almost impossible problem mathematically, so without empirical evidence showing a connection between cause and effect, a conclusion can not be drawn. Correlates can only direct us as to what questions to ask and which avenues to investigate, correlates cannot, without extensive investigation, be used in isolation to determine any "score" of any significance.
<br/>
<br/>

#### Systematic Bias
Big data and predictive analytics, and especially people analytics suffers from the "N = All" fallacy. "N = All" simply means that the sample size is equivalent to population, and with regards to big data, it is this fallacy that produces the false assumption that sampling bias and uncertainty is reduced to zero. The most comprehensive datasets pertaining to people and their behaviour will always have incomplete information. The interactive, social and behavioural models imposed by any platform (social, e-commerce, crowd sourced, whatever) will dictate who uses the platform, and how they use it. It stands to reason then that this will taint the data collected and introduce systematic bias. Further compounding this, systematic bias introduces a component of modulation and mediation because almost every data capturing interaction (services, recommenders, search result ranking, etc.) modulate results because they steer us in particular directions based both upon the interaction model imposed by the service (architecture, environment, etc.), and upon commercial, social or behavioural interests of those providing the service.

An illustrative example of systematic bias as observed in the "Boston Street Bump app", is detailed by Tim Harford in _"Big data: are we making a big mistake?"_  [^7] 

_"As citizens of Boston download the app and drive around, their phones automatically notify City Hall of the need to repair the road surface. The City of Boston proudly proclaims that the “data provides the City with real-time in­formation it uses to fix problems and plan long term investments."_

_"Yet what Street Bump really produces, left to its own devices, is a map of potholes that systematically favours young, affluent areas where more people own smartphones. Street Bump offers us “N = All” in the sense that every bump from every enabled phone can be recorded. That is not the same thing as recording every pothole."_
<br/>
<br/>

#### Accuracy
The accuracy of the digital footprint left behind by people in their online activities is highly questionable. Personal data, in particular the "self reported" variety cannot be assumed to be objective or accurate. How does big data deal with sarcastic comments made on twitter? Is a message retweeted because the person agrees or disagrees? Is the personal information people use in online surveys and competitions accurate? Are all user details used to sign up for trial software real? How do we determine the accuracy of qualifications and skills from Linkedin? What types of relationships characterise each connection on Linkedin or follower on Twitter? Is the general demeanor of people in online communities always representative of their character in real life? Alter egos? Trolls? If it's possible to answer "no" to any of these questions, then systematic error is inherent in any system churning this data due to inaccuracy.

Data does not speak for itself.
<br/>
<br/>









#### Privacy and Secrecy
Every day there's an insane amount of data being collected about people as they go about their off and online lives, and as time goes on, the types of, and rate of information collected will continue to grow. Do you even know what data is collected, stored, analysed and sold on by third party companies about you? 

Privacy is perhaps the most worrying aspect of big data in general, and particularly, people analytics. The general public are becoming increasingly concerned about government regulation and laws surrounding the topic of ISP data retention of late. The capacity of goverment agencies to perform surveillance and compile dossiers on their citizens is neither ethical or just according to many people around the globe. Is the type of unsolicited surveillance and profiling done by private companies any less worrysome? 

Legislation has not kept up with the growth in collection and analysis of personal data by corporations.

Freedom of personal information and profiles generated and/or kept by corporate entities and government? Should one be allowed access to their own profile free of charge? Can one have their profile "removed" costs to obtain free info (court docs, FOI, scientific papers).

Knowledge is power, if the power only flows one way then there will be control and abuse. The findings of the analytics on any potential employee (hired or not) should required to be share with that person, not only with the employer. 

Surveillance, for example, can inhibit such lawful activities as free speech, free association, and other First Amendment rights essential for democracy.

Daniel Solov, ‘Why Privacy Matters Even if You Have ‘Nothing to Hide’

    “My life’s an open book,” people might say. “I’ve got nothing to hide.” But now the government has large dossiers of everyone’s activities, interests, reading habits, finances, and health. What if the government leaks the information to the public? What if the government mistakenly determines that based on your pattern of activities, you’re likely to engage in a criminal act? What if it denies you the right to fly? What if the government thinks your financial transactions look odd—even if you’ve done nothing wrong—and freezes your accounts? What if the government doesn’t protect your information with adequate security, and an identity thief obtains it and uses it to defraud you? Even if you have nothing to hide, the government can cause you a lot of harm.”
by Daniel J. Solove, Chronicle of Higher Education, “The Chronicle Review” (May 15, 2011) 






http://www.forbes.com/sites/georgeanders/2012/10/03/no-resume-no-problem-show-us-your-social-stream-instead/

How do job candidates feel about having an unfamiliar talent-search company poring over their activities on various professional websites? Bischke says that so far, only two engineers have asked to be delisted from Entelo’s databases. The rest, presumably, haven’t noticed or don’t mind the attention at all. He says Entelo will start offering a more prominent opt-out feature, though, mindful of some job candidates’ desire for privacy — or fewer emails.

Why should I have to, and how could I even "opt-out" of a system that I probably don't even know about in the first place? No, the only ethical thing to do before you repurpose anyones data is to _ask_ them if you can do so before you do it.

If an unknown entity takes data and profiles individuals without their knowledge and then provides an opt-out after the fact, is like copying someones personal files from a filing cabinet without their knowledge and then, if they ever realise, they can "opt-out" and their files will not be analysed or sold from then on (and who knows who's analysed and/or bought the contents of those files in the meantime).




http://www.cio.co.nz/article/521050/what_it_recruiters_know_about_-_whether_re_looking/
RemarkableHire uses what it calls "social evidence" that people are knowledgeable in a particular skill by looking, among other things, for recognition by their peers and indications that they've provided the best answers to questions posted online. "We look for signals within the content that someone has expertise in a particular skill," says Rothrock. The company then provides skills proficiency ratings of one to four stars for each subject. (but this assumes that "social" means "public, on the internet" which is incredibly naieve. 

"It's an algorithmic challenge" to correctly match up data from various sources and assemble those into accurate master profiles with high degree of confidence -- and that's part of the secret sauce, says Ming at Gild Source. (so, paradoxically, the profiling process is secret and kept private)

With the exception of RemarkableHire, most HR data-mining services don't have a clearly articulated mechanism for subjects to opt out, and subjects don't have the opportunity to review the profiles for accuracy or to correct erroneous data. White argues that this should be less of a problem if recruiters use the tools to find potential candidates, rather than as a way to exclude prospects.



<br/>
<br/>



#### Innovation, Diversity and Serendipity

Cohen, Julie E., What Privacy Is For (November 5, 2012). Harvard Law Review, Vol. 126, 2013. Available at SSRN: http://ssrn.com/abstract=2175406

Innovation also requires room to tinker, and therefore thrives most fully in an environment that values and preserves spaces for tinkering. A society that permits the unchecked ascendancy of surveillance infrastructures, which dampen and modulate behavioral variability, cannot hope to maintain a vibrant tradition of cultural and technical innovation. Efforts to repackage pervasive surveillance as innovation — under the moniker “Big Data” — are better understood as efforts to enshrine the methods and values of the modulated society at the heart of our system of knowledge production. The techniques of Big Data have important contributions to make to the scientific enterprise and to social welfare, but as engines of truth production about human subjects they deserve a long, hard second look.


Innovative practice is threatened most directly when circumstances impose intellectual regimentation, prescribing orthodoxies and restricting the freedom to tinker. It thrives most fully when circumstances yield serendipitous encounters with new resources and ideas, and afford the intellectual and material breathing room to experiment with them. 48

When the predicate conditions for innovation are described in this way, the problem with characterizing privacy as anti-innovation becomes clear: it is modulation, not privacy, that poses the greater threat to innovative practice. Regimes of pervasively distributed surveillance and modulation seek to mold individual preferences and behavior in ways that reduce the serendipity and the freedom to tinker on which innovation thrives. The suggestion that innovative activity will persist unchilled under conditions of pervasively distributed surveillance is simply silly; it derives rhetorical force from the cultural construct of the liberal subject, who can separate the act of creation from the fact of surveillance. As we have seen, though, that is an unsustainable fiction. The real, socially constructed subject responds to surveillance quite differently — which is, of course, exactly why government and
commercial entities engage in it. Clearing the way for innovation requires clearing the way for innovative practice by real people. Innovative practice in turn requires breathing room for critical selfdetermination and physical spaces within which the everyday practice of tinkering can thrive.


Which brings into question the future of clandestine innovation, often referred to as "skunkworks" [^8].








"""
predictive rationality
"""
<br/>
<br/>






#### The Age of Analysis, Profiling and Omnicient Surveillance
With people analytics, correlations are drawn and judgements are made about our behavior and social skills, and statistics are used to score our intelligence, quality of work, and probabilities of success or failure for particular tasks. However, in any form of real world analysis, finding correlations in data (big, disparate or otherwise) is only the _starting point_ of research, the process thereafter needs to establish the accuracy and validity of that data and whether correlations present a causal relationship or are merely coincidental. People analytics is being sold as a turn-key solution to recruitment and human resources, and is being touted as a substitute for, rather than a supplement to, observation and analysis. What makes people analytics particularly repugnant is the vast amount of stalking and invasion of privacy it allows corporations to engage in and its apparent acceptance as a normal business practise. 

Even if we can solve the problems of causal mechanism, systematic bias, and accuracy, is this a world we want to live in anyway? What are the negative social effects and psychological impacts of reducing people to numbers calculated by an algorithm? Will people analytics result in a lack of employee diversity? Will constant surveillance stifle creativity and innovation leading to less serendipitous discoveries?  Are there cicumstances where a "score" compiled by a private entity rating the value of a person and onsold to interested parties is considered defamatory? How can fairness and due process be articulated when correlations from a complex algorithm is the one keeping "score"? Am I negatively affecting my "score" by publishing this article?

Is predictive rationality such an elusive possiblibility that people analytics will never be reliable?

The day will come where there will be an omni-present, ever churning array of algorithms watching and calculating the value of all employees, across all businesses, all of the time. What then? Keep your head down, do your work, follow the group, work is life.

In the same way that [Boston's street bump app falsely assumed n = all][ref] so too do profiles produced by people analytics.

will be tracked only for their suitability for their initial jobs, not for their potential.

Government legislation needs to be put in place to restrict corporate data mining, surveillance, profiling and analysis on private citizens without their consent.


Hiring managers are just looking for a reason to discard your resume to narrow down the talent pool. Anything negative gets you tossed.

According to its critics, Big Data is profiling on steroids, unthinkably intrusive and eerily omniscient.

from anonymous marketing to personal intrusion (i.e. marketer doesn't care about the individual per-se, whereas with employers, that's all they care about).
before it was anonymous, now its personal.




Although the content of this article is mostly concerned with those in the programming community with regards to people analytics, it brings into question the ethics of commoditisation and trading of data and subjective analysis on citizens, erosion of privacy and ultimately democracy itself.
<br/>
<br/>

#### Notes

[^1]:[They're Watching You at Work][2]
[^2]:[The Bloomberg Spying Scandal Isn't Surprising — Stalking Is Part Of Its Culture][1]
[^3]:[https://www.gild.com/][3]
[^4]:Gild ranks the quality of each programmer they analyse as a number between 1 and 100.
[^5]:[How Big Data Is Playing Recruiter for Specialized Workers][4]
[^6]:[Spurious Correlations][5]
[^7]:[Big data: are we making a big mistake?][6]
[^8]:[Skunkworks][7]

[1]:http://www.businessinsider.com.au/bloomberg-scandal-culture-secrecy-spying-2013-5
[2]:http://www.theatlantic.com/magazine/archive/2013/12/theyre-watching-you-at-work/354681/
[3]:https://www.gild.com/
[4]:http://www.nytimes.com/2013/04/28/technology/how-big-data-is-playing-recruiter-for-specialized-workers.html?pagewanted=4&ref=business&pagewanted=all&_r=0
[5]:http://www.tylervigen.com/view_correlation?id=1597
[6]:http://www.ft.com/intl/cms/s/2/21a6e7d8-b479-11e3-a09a-00144feabdc0.html
[7]:http://en.wikipedia.org/wiki/Skunkworks_project
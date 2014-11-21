---
layout: post
title: "Vigilantism: Vandalism and Ethics in Engineering"
description: "In this article well debate the ethics of sabotage and damaging of hardware with software. We'll use a recent and well known case involving FTDI to make the case clear"
tagline: "craft"
category: craft
tags: [ethics, programming, craft, design, compsci, coding]
article_img: bootstrap/img/jason_li.jpg
article_img_title: Jason Li - Vandal, Braggart.
reddit_url:
hn_url:
---
{% include JB/setup %}
<div class="intro">
<div class="intro-txt">
<p>
On the 26th of August, FTDI, manufacturer of the world's most widely used USB serial chip, released their latest Windows driver (v 2.12.0.0) and its distribution via a recent Windows automatic update has been, at best a monumental error in judgment, and at worst a case of unethical vandalism. 
</p>
<p>
In simple terms, the driver update executes code to brick any device deemed non-genuine by FTDI such as counterfeit chips, but more alarmingly, legitimate clone chips. This not only stops the device from running with the FTDI drivers, it stops it working entirely as it sets the device PID to 0 which stops Windows and Linux operating systems recognising the device.
</p>
<p>
Had FTDI released drivers that simply didn't work with non-genuine chips then there would be no issue. Manufacturers and end users could have been alerted to the fact that the driver was incompatible on non-genuine hardware, and this would've made the point well enough.
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

#### Vandalism
It's perfectly acceptable to supply drivers that don't work with non-genuine hardware, however, FTDI went far beyond detection of, and not providing supported drivers for counterfeit and clone chips. They engineered and released malicious code that deliberately damages that hardware. <b>That's right, FTDI are using malicious code to damage hardware.</b>

Here is a dump of the reverse engineered code[^1]:

<div class="plain-border">
<div class="plain-bevel">
<img class="article-image" title="FTDI Driver Malicious Code" src="{{ASSET_PATH}}/bootstrap/img/ftdi_evil.png"/>
</div>
</div>
For a full analysis on what the code actually does, please visit the [original thread over at EEVBlog][1]

I have to point out at this time that [FTDI license][3] specifically states: "Use of the Software as a driver for, or installation of the Software onto, a component that is not a Genuine FTDI Component, including without limitation counterfeit components, MAY IRRETRIEVABLY DAMAGE THAT COMPONENT." but this _does not_ give them any ethical right to use malicious code to damage end user hardware. Even if we ignore the potential illegality of this deliberate bricking tactic and "disclaimer" it's still wanton vandalism on the unsuspecting and innocent end user. FTDI are taking a riot mentality and damaging innocent victims' property. Fantastic customer relations FTDI!

As can been gleaned from the code above, the driver in no way discriminates between illegal counterfeit chips and _legal_ clone chips. Further, it is ridiculous to suggest that any end user would be aware of the fact that some USB device they have legitimately purchased from a legitimate supplier knows the difference between real and counterfeit FTDI chips. Hell, if most even knew what FTDI was it'd be bloody amazing! In fact, supply chains in the electronics industry are part of such a complex ecosystem there is no telling where in the chain any alleged counterfeiting occurs (or even if it is in fact deliberate). And again, I must point out that these drivers disable legitimate cloned chips.

Now let's suppose for a moment that a manufacturer of a device has, unbeknown to them, shipped 10000, 100000, whatever number of devices using a counterfeit chip that was substituted many links away up the supply chain. What does a company do when they have any number of devices out there produced in any number of different production runs where any or all of the devices on any or all of those runs could contain either counterfeit or clone chips? What does the future hold for them once all their devices suddenly stop working and their support lines are running hot with complaints and returns? Rolling back the to the previous driver has no effect. What now? Device recall?
<br/>
<br/>

#### What should've been done?
FTDI should be using legal avenues with which to pursue counterfeit chip makers, and if their claims are proven, seek prosecution of those counterfeiters. Any punitive measures that arise as a result of such legal action will then be borne by the actual perpetrators and not the innocent end users. 

Other than pursue the relevant counterfeiters via the legal system, there are many other avenues for getting a point across.
The driver could employ some intelligence to alert the user as to whether or not they are using a genuine FTDI chip. "Sorry. You are attempting to use an official FTDI driver with incompatible hardware." or something would suffice. This course of action would avoid damaging hardware and in the case of clones not illegally disable them. The onus then is on the clone manufacturer to provide updated drivers. Supplying software that doesn't work on competitors hardware is perfectly OK, damaging hardware is definitely not OK.
<br/>
<br/>

#### What should be done now?
Microsoft should revoke the driver signature as the driver not only disables counterfeit chips, it also breaks legitimate FTDI clones, rendering any devices using these chips broken via what can only be defined as malicious code. Microsoft need to treat this issue with the seriousness it deserves. Windows automatic updates of drivers that maliciously break perfectly legal hardware should never be condoned or facilitated by Microsoft and I'm sure they are already thinking very deeply about the matter and are taking steps to ensure something like this never happens again. If the FTDI signature is not revoked, then how can we trust any signed driver from now on?

As to what FTDI can do to rescue themselves, their aggressive and damaging tactics in this matter, to use their own terms: "MAY IRRETRIEVABLY DAMAGE" their company.

**Edit: The drivers have now been pulled from Windows update**

If you are unfortunate enough to have bricked a device with these drivers, there is a [solution posted on /r/arduino][2]
<br/>
<br/>

#### Brand damage
This fiasco could validate competitors and/or create a market for legitimate 3rd party manufacturers. Many electronic engineers say they will no longer use FTDI chips and will just switch to another chip to avoid any possible hassle, particularly given the difficulty of verifying genuine chips. Hardware producers and engineers will certainly think twice about incorporating a device into their product with all these uncertainties which could appear at any time in the lifetime of their hardware.

Some [Arduino products][4] have already switched from a FTDI clone to a different chip (CH340G).

I wouldn't be surprised if a clone manufacturer(s) emerges as visible branded competitor. The market opportunity has been created right here, right now by FTDI themselves.
<br/>
<br/>

#### Safety
FTDI chips could be in any number of medical devices, critical safety equipment or emergency equipment. Deliberately breaking these devices and rendering them nonoperational could result in unforeseen circumstances which is incredibly negligent and unethical. If, as a result of this negligence, a defect occurred in a critical device which led to a loss of life or property, criminal charges could be brought against FTDI.
<br/>
<br/>


#### Ethics
FTDI have vandalised end user equipment, damaged legitimate devices and their reputation. Not only have they pissed off users, they may have put people and property at serious risk. They have bred distrust in the entirety of the FTDI genuine and non-FTDI clone line of chips and in themselves as a company. They may well have cost manufacturers a lot of money (or their entire business) due to broken hardware that either contained clone chips or was not known to contain counterfeit ones. 

As we can see, just a small snippet of malicious code is enough to do all this damage (literally and figuratively). Sabotage of hardware using software is entirely unethical for whatever reason and can have unknown and devastating consequences. It is for this reason that as software engineers we should always be ethical in the code we write.
<br/>
<br/>

#### Notes
[^1]: Reverse engineering, function naming, code comments and image thanks to marcan over on EEVBlog. The original source and discussion can be found [on this thread over at EEVBlog][1]

[1]:http://www.eevblog.com/forum/reviews/ftdi-driver-kills-fake-ftdi-ft232/msg535270/#msg535270
[2]:http://www.reddit.com/r/arduino/comments/2k0i7x/watch_that_windows_update_ftdi_drivers_are/clgviyl
[3]:http://www.ftdichip.com/Drivers/FTDriverLicenceTerms.htm
[4]:http://www.ebay.co.uk/itm/Nano-V3-0-ATmega328-16M-Micro-controller-CH340G-Board-Mini-USB-for-Arduino-/121465239079

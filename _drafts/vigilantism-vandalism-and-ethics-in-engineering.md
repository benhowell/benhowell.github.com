---
layout: post
title: "Vigilantism: Vandalism and Ethics in Engineering"
description: ""
tagline: ""
category: 
tags: []
article_img: bootstrap/img/jason_li.jpg
article_img_title: Jason Li - Vandal
reddit_url:
hn_url:
---
{% include JB/setup %}
<div class="intro">
  <div class="intro-txt">

<p>
On the 26th of August, 2014, FTDI, manufacturer of the worlds most widely used USB serial chip, released their latest Windows driver (v 2.12.0.0) and it's distribution via a recent Windows automatic update has been, at best a monumental error in judgement, and at worst a case of unethical vandalism. In simple terms, the driver update deliberately executes malicious code to brick any device deemed non-genuine by FTDI such as conterfeit chips, and more alarmingly, legitimate clone chips. This not only stops the device from running with the FTDI drivers, it stops it working entirely as it sets the device PID to 0 which stops Windows and Linux machines recognising the device. FTDI are using malicious code to deliberately damage hardware.
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


------------
Evidence

Microsoft auto update of the driver is what runs the drivers and malicious code that breaks hardware.

Someone reverse engineered FTDI driver on the EEVBlog forum and its not specifically detecting fakes. Its doing EXACTLY SAME procedure without discrimination - it is issuing illegal (and ignored on real chip) write to eeprom.
There is no detection, just some fuzzing with illegal instructions.

https://marcan.st/transf/ftdi_evil.png
http://www.eevblog.com/forum/reviews/ftdi-driver-kills-fake-ftdi-ft232/msg535270/#msg535270


**the twitter stream has been deleted but one argument put forward was the licence agreement:

http://www.ftdichip.com/Drivers/FTDriverLicenceTerms.htm

"Use of the Software as a driver for, or installation of the Software onto, a component that is not a Genuine FTDI Component, including without limitation counterfeit components, MAY IRRETRIEVABLY DAMAGE THAT COMPONENT."

Even though their TOS might state that, it's extremely poor PR to go after the end user rather than the distributor. Most people won't even know that they have a counterfeit chip. Even though it may be well within their rights the customers will eventually find out what they have done and bear a grudge.
May cause more problems than it fixes.



list affected devices?
ftdi attitude?


link to news coverage?

--------------








[condensed] what should've been done?

Other than pursue the relevent conterfeiters via the legal system, there are many other avenues for getting a point across.
The driver could employ some intelligence to alert the user as to whether or not they are using a geniune FTDI chip or not. This course of action would avoid damaging hardware and in the case of clones not illegally disable them. The onus then is on the clone manufacturer to provide a driver (fix this, it sounds crap). "Sorry. You are attempting to use an offical FTDI driver with incompatible hardware." or something. Supplying software that doesn't work on competitors hardware is perfectly ok, damaging competitors hardware is not ok.

-----------

---------------------
[condensed] what should be done?

Microsoft should revoke the driver signature as the driver not only disables conterfeit chips, it also breaks legitimate FTDI clones, rendering any devices using these chips illegally broken via what can only be defined as malicious code. Microsoft need to treat this issue with the seriousness it deserves. Auto updates of drivers that deliberately break perfectly legal hardware should never be condoned or facilitated by Microsoft and I'm sure they are already thinking very deeply about the matter and indeed taking steps to ensure something like this never happens again. If the driver signature is not at the very least revoked, then how can we trust any signed driver from now on?

There is a fix!
http://www.reddit.com/r/arduino/comments/2k0i7x/watch_that_windows_update_ftdi_drivers_are/clgviyl

-------------

----------
[condensed] ruining brand and spawning competitors 

This could indeed validate competitors and/or create a market for legitimate 3rd party manufacturers. electronic engineers such as eevblog dude (and try to find others) say they will no longer use ftdi chips. just switch to another chip to avoid any possible hassle, particularly given the difficulty of verifying genuine chips. Hardware producers and engineers will certainly think twice about incorporating a device into their product with all these uncertainties which could come back to bite them at any time in the lifetime of the hardware.

I wouldn't be surpprised if the clone manufacturers emerge as visible branded competitors. The market has been created right here, right now by FTDI themselves.

Some Arduino products [ http://www.ebay.co.uk/itm/Nano-V3-0-ATmega328-16M-Micro-controller-CH340G-Board-Mini-USB-for-Arduino-/121465239079 ] have already switched from a FTDI clone to a different chip (CH340G). 

----------

--------------
[condensed] safety
FTDI chips could be in any number of medical devices, critical safety equipment or emergency equipment. Deliberately breaking these devices and rendering them unoperational could result in any number of unforeseen circumstances which is incredibly negligent and unethical. If, as a result of this negligence, a defect occured in a critical device which led to a loss of life or property, criminal charges could be brought against FTDI. This may indeed be an extreme hypothetical but it is for this reason that as software engineers we should be ethical in the code we write.

------------














------------
what I think


Nobody would fault FTDI for releasing new drivers that don't work with counterfeit parts.
That alone would cause enough inconvenience to manufacturers to make sure they use legitimate parts.
It's the (unethical) bricking of the parts that has everybody up in arms.


It's not that they just won't work with their drivers, it's that it disables the device so that it's not operable without special (and costly, if they're widely deployed) intervention.

  The new Windows driver changes the PID to 0, and then the driver won't
  recognize the device (even if you edit the INF file), and you can't use
  the config tool. The workaround is to use a Windows XP or Linux system to
  change the PID back, and then don't use the new driver.



This is really skirting the ethical line and I'm not sure I can agree with their methods. I'm fine with them writing a driver that fails to work with counterfeit chips, but not actively disabling the chip. What if the device is attached to more expensive equipment that will also fail? We can't ensure that these chips are knowingly purchased after all.



It is ok to have SOFTWARE that refuses to work
It is NOT OK to modify the HARDWARE in ANY WAY, including changing the PID.

Further Most people defending FTDI assume this code will be 100% pure and never incorrectly identify a real chip as fake, I find that claim to be suspect.



the sabotage of hardware

You purchase a device or some piece of hardware for a project which becomes useful to others and you decide to make the project marketable and release your own line. You source your components and one of them happens to have an FTDI chip that you believe legitimately came from the company. Your project is accepted by others and everyone is happy.

Then one day, your supplier decides to switch out a source for the chip in one of the components of your project to one that's cheaper (the supplier may not suspect these are counterfeit). You don't see any difference either as you haven't tested it with the latest driver, which may not be out yet. The project is now shipped.

A new driver is released. An overwhelming number of customers now hit your support system saying the update has stopped your project cold. Rolling back the update has no effect. You don't know which component caused the problem as your project uses several from different suppliers. It may take days or even weeks to track down the problem, but meanwhile, your customers grow angry.

This is now your fault and you're left holding the bag.

This scenario need not be hypothetical, as this is just the same as GM "fixing" a problem with their ignition switch that has already claimed lives, but leaving the part number the same. This makes recalling a nightmare as there's now no way to tell which cars are affected until the switch fails or someone dies again.

Just the same as there's no way to tell which one of your project's components carry counterfeit chips and you need to issue a recall for potentially 100K units since you don't know which ones carry a counterfeit and there's no software fix anymore as the chip is bricked. Your project is also potentially being used in mission critical infrastructure that cannot be shut down to do a test without costing a lot of money. Better hope no one follows "best practices" and updates the driver as it may also contain a security fix.

FTDI is well within their rights to protect their product. But are they doing the ethical thing by costing unsuspecting customers and potentially hundreds of other businesses that rely on their product valuable time and money? All for not knowing that somewhere along the supply chain, someone cut a few corners and got their chips from a counterfeiter? Should they be victims twice; once by the counterfeiter and again by the company?




You completely missed the point. The driver cannot possibly know the difference between a chip falsely sold as being an FTDI chip, and one that is compatible without being mislabeled.




vigilante destruction of the property of end-users who bought a product with a chip in it that, without the user's knowledge, may or may not have passed through the hands of someone who may or may not have labeled or described it deceptively.

The deceptive party may not even exist, and even if they do, they are not the victim of the vigilante justice. A user who may not have even heard of FTDI is the victim.

Basically all vendors outsource production. Very few vendors will knowingly put a fake chip in their product, the trick is pulled by someone closer to manufacturing, either to cut costs or to try and ship earlier in case the original chip is hard to get right now.

I doubt very few vendors will realize if a fake FTDI chip is part off their manufactured product even after rigorous testing since the functionality is correct (until you get this driver).


A consumer buying a product doesn't make an informed decision about the type of serial interface in their devices, much less whether it's genuine or not. There's an expectation that the product will work and that falls on the manufacturer, who might not even be responsible either.


But what they're doing is catching consumers in the firing line, who will wind up with multiple dead USB devices. There's no reasonable way a consumer can know they are buying something with a fake chip and this could kill devices years old, which will be outside of warranty.


** nice analogy
Then they don't have any options, period. But if someone slaps a Toyota badge on a car and takes it to be serviced, Toyota don't suddenly have the right to instead smash the car with sledgehammers.





--------------
ethics

In addition to the other posted replies, it's bad software engineering. You really can't be confident enough in your logic to ever start issuing DESTROY_HARDWARE() commands of any kind, short of the small set of very specialized programs that may be deliberately used for such things (FPGA programmers, etc). Any error whatsoever and you may end up nuking your real customer's hardware. Bad plan. Same reason why programs that think they are pirated shouldn't run around being actively destructive... some real customer will find some way to tickle that code, if only through bad hardware, and now you're in trouble.

To a first approximation, all code eventually runs. Relatedly, never put an error message into your product you wouldn't want customers to see, because they will.


fuck FTDI, fuck the management who ordered this, and fuck the engineer(s) who didn't quit in protest. I'm not easily offended, but as a programmer who has been responsible for code that would result in loss of life if I made it defective by design, I have no sympathy for this sort of thing. One other thing that I could hope for is that this teaches everyone the true cost of closed source, especially drivers. Don't put up with it.



is there a "law" that says we shouldn't do this (programmers law of doing no harm)?
be an ethical programmer.


-----------






























-----------
Legalities


It's interesting to consider from a legal perspective exactly why this isn't something a company is allowed to do (even if it is reversible? software shouldn't be able to disable hardware?). 

- Intentionally sabotaging someone's stuff, legally, is more or less the same as intentionally taking it. Keying a car and driving it away might have different names but are on the same scale.

- There ain't no self help. If you think someone else's stuff should actually be your stuff, your path is through a court.

- We don't fix things with injunctive relief that can be fixed with money. When Apple proves that Samsung violated a patent or vice versa, we don't collect and burn all the infringing phones, we just make someone cut a check. Because we are not idiots.

- The "someone" who cuts the check is Samsung or Apple, not their customers. As far as I know no one's managed to go after end users, even in extreme cases like a $10 designer handbag where the buyer obviously knows it's not real. (And it's at best unclear whether going after the buyers would make any sense, even in those extreme cases -- if someone pays knockoff prices for a knockoff product, it's the seller and not the buyer who has ill-gotten gains. There might be some additional reputation damage and lost profits that the buyer is complicit in, but it makes a lot more sense to me -- and apparently everyone else -- to make the seller pay for those as well.)

- When you do go after the seller of trademarked goods and want to seize those goods, we actually have a procedure for that -- Section 34 of the Lanham Act.[1] Which includes a whole bunch of protections like swearing out an affidavit, getting permission from a judge, informing the attorney general, posting a bond to cover damages, conducting the seizure through government agents, and keeping the seized items in the custody of the court. It's very much unlike showing up at someone's house and breaking their stuff.

(I am a lawyer; I am not a trademark lawyer; I just googled some stuff based on vague memories from law school to write this.)


VIDs are not legally protected marks.

Unless there's a valid patent at issue (and nobody's mentioned a patent at all), there is nothing about the chips themselves that violates any law.

Defending vigilantism is disgusting enough when you have a colorable argument that the target of your act is the only one harmed, and is actually guilty of some sort of legal violation.

After giving this some more thought, it's no longer clear to me that a simple {vendor, product} id pair is even "protected" by trademark. It would be protected by contract terms, especially if at some point in the supply chain someone had signed a contract with USB-IF. However it's quite clear that no entity is entitled to commit vandalism in order to enforce contract terms for a contract to which that entity is not even a party.

These FTDI fakes aren't stolen, though. There is a massive difference between stolen, counterfeit, and clone. If the chips are only sold as "FTDI-compatible", then I would even say they should be completely legit, like Dalvik vs. Java.

The label printed on the outside of the plastic package of the chip is the only thing that's illegal, since it's depicting FTDI's trademark. Scrape that off and you've got a 100% legal compatible clone. Whoever decided to use the clones in any given device may be be in breach of a contract if the use of genuine FTDI chips was specified, but that may easily not be the case. (The clones also violate USB specs by using someone else's VID, but that only prevents them from labeling the product with the trademarked USB logo.)


As I see it, it's not about whether "fake chip users" are obeying the laws; the burden should be on FTDI to justify its willful property damage.

To the extent that there is a violation of law involved in the manufacture, sale, or use of the chips (and such violations are much more likely in the first two cateories), there are legal remedies available for those violations, which generally require proving that a violation occurred and that the person against whom remedy is sought is the appropriate person against whom to seek a remedy.

To the extent the violations are with manufacture and sale, the user is not generally the appropriate target, even if destruction of the device was an appropriate remedy.

If FTDI have an issue with a company ripping off their IP then go sue that company. 

---------------









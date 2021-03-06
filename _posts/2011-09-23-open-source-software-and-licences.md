---
layout: post
title: "Open Source Software and Licences"
date: 2011-09-23 00:42
comments: false
categories:
---

We all have heard about <a  title="Open-source software" href="http://en.wikipedia.org/wiki/Open-source_software" rel="wikipedia">open source software</a>, the philosophy behind that, how it came into being etc etc. The general opinion is that these open software are free, we can see the <a  title="Source code" href="http://en.wikipedia.org/wiki/Source_code" rel="wikipedia">source code</a> and change that to fit our needs - perfect! No more paying yearly licence fees! And then there are problems like getting support for these and who to sue when your million dollar system built on <a  title="Open Source" href="http://www.wikinvest.com/concept/Open_Source" rel="wikinvest">open source</a> technologies fails - but that is a different story.

In this post I will describe a few issues with the open source licencing schemes - I will not describe any specific licence or list all available <a  title="License" href="http://en.wikipedia.org/wiki/License" rel="wikipedia">licences</a> - you can easily get that from the internet. What I will do is highlight a set of key considerations that you must bear in mind when you try to use any open source software in your projects. This topic piqued my interest of late especially after the Oracle vs Google fight over Java copyrights.
<h3>A basic introduction of why we need a licence for open source software in the first place</h3>
Couple of years ago while creating a project proposal based on an <a  title="MIT License" href="http://en.wikipedia.org/wiki/MIT_License" rel="wikipedia">MIT Licensed</a> portal framework, my roommate asked me how does it matter what licence it is and why there should be a licence when the software is free and open source? This is what I told him.

Lets say you build a piece of software which is pretty cool and you want to charge a couple of hundred dollars for that from the people who would use it. But then you don't have the background to be able to create a sales pitch to sell it and make that money. Or maybe you believe that what you built should be available to everyone so that they can use it freely. So, you decide to put it out on the internet and let them download it and see the source code and modify it if they need to. You probably lurk around in <a  title="Internet Relay Chat" href="http://en.wikipedia.org/wiki/Internet_Relay_Chat" rel="wikipedia">IRC</a> or on email or forums and help the users out as well. And then one day someone who is using your software gets into a mess because of the code or they way they used it and is in a loss. And they want to sue you because it was your code! Now you are in a pickle aren't you?

You wrote a piece of software and now you are being held responsible for damages arising out of its use - even though you are not employed by the guy who got damaged. And this happened because you released the software without a set of terms - terms of usage, terms defining and limiting your liability, terms defining how the little liability that you might have will be nullified if they change the code etc etc. Lots of legal talk, but yes, this is what will save you from legal problems.

You could probably just put a disclaimer saying you are not responsible for the code or the issues out of it - but then you are not claiming or getting credit for work that you have done and which might be worth something. Plus there is no ownership so anyone can claim your work as his - plagiarism etc etc.

So, having a licence gives you a twofold advantage - it lets you assert your ownership or more importantly authorship of a particular piece of software which is you intellectual property and then it lets you define the liabilities and limit the scope of things that you are held responsible for when everything hits the fan.
<h3>OK, so why do we have so many licences?</h3>
Opinions and philosophies are pretty common and to quote one of <a  title="Mel Gibson" href="http://www.rottentomatoes.com/celebrity/mel_gibson" rel="rottentomatoes">Mel Gibson</a>'s lines, everyone has one. So it is not surprising that in a sample of a few thousand open source enthusiasts there are differences of opinion on how things should be shared and how the shared things should be used. There are camps for each and they are pretty strong. In general, all the licences that are out there mainly differ on the following items
<ul>
	<li><a  title="Journalism sourcing" href="http://en.wikipedia.org/wiki/Journalism_sourcing" rel="wikipedia">Attribution</a> - When you use the software released under a licence, there are certain guidelines on how you should attribute the work back to the original author and how you should not claim it as yours</li>
	<li>Extending - Whether you are allowed to extend the software? What parts and maybe within what limits. Again, this links back to Attribution in the sense that once you change a part of code, you should clearly distinguish your parts from the parts built by the original author.</li>
	<li>Re-licencing - If you decide to change some of the code and use it just for yourself the fine - but, if you decide to release your version of it, then there are guidelines on how you should do it, whether you should use the same licence or can you use it as a <a  title="Proprietary software" href="http://en.wikipedia.org/wiki/Proprietary_software" rel="wikipedia">closed source</a> proprietary software etc etc</li>
	<li><a  title="License compatibility" href="http://en.wikipedia.org/wiki/License_compatibility" rel="wikipedia">Compatibility</a> - Just like people don't get along with each other, licences also do not get along with each other. Some of them say that its OK to use it as part of a proprietary software while others refuse to let you do so. Also there are things like a software under a particular licence cannot be used along with a software from another licence because a clause in one of those conflicts with the other.</li>
</ul>
<div>As you can see there are lots of if's and but's and there are people out to enforce them and make things right. But all these licences help cover the rights of all the developers involved in these projects and help them get paid to build your favorite cool open source tool!</div>
<div><span class="Apple-style-span" style="font-size:15px;font-weight:bold;">Hmm, seems complicated - should I even use these?</span></div>
Yes - of course you should use them. Just because something is written in legal parlance and is confusing does not mean that it should scare the shit out of you. In general, the following rules should be kept in mind though -
<ul>
	<li>If you download and run an application released under an open source licence and never bother to look at the code or change it - then you are safe.</li>
	<li>Some licences do mention that you can use it free for personal or non-commercial usage and you need their permission to use for other cases - in those scenarios it is better to get in touch with the guys who built it and get their approval</li>
	<li>If you are using an open source library and include a method call or class from that in your code then you are bound by the licence of the library.</li>
	<li>If the application you built using an open source library or a set of such libraries is used only within your organization and not sold to others as your product then you don't have to worry about licencing your software or the compatibility of the individual licences of the libraries</li>
	<li>If you do plan to release it though, then make sure that you use libraries with licences that are compatible with each other and also that you release your software under a licence that covers all these other licences in use.</li>
	<li>Last but not the least, do not remove any banners or licence files that are in the source code or shipped with the software that you are using.</li>
</ul>
<h3>No one is out to get you if you don't intentionally break the terms</h3>
<div>Remember, we are all human and we sometimes mix up things. If we are not intentionally doing things the wrong way and take a moment to clarify before we take a step then we should be good. If you are looking to use any licence and have any questions then please make sure that you contact the developers and that you take necessary permission before you do anything.</div>
<h3>Conclusion</h3>
<h3></h3>
<div>Licences are complicated and there are many out there in the open source world - in the end all they intend to do is to give credit where deserved and limit scope of liability. As long as we are not stealing someones work and we are giving credit these are pretty easy to use.</div>
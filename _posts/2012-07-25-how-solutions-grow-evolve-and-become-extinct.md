---
layout: post
title: "How solutions/platforms grow, evolve and become extinct"
date: 2012-07-25 23:00
comments: false
categories:
---

Every so often, I hear someone saying or writing about how a particular <a  title="Solution" href="http://en.wikipedia.org/wiki/Solution" rel="wikipedia" target="_blank">solution</a> seems wrong and how given a chance they would <a  title="Design" href="http://en.wikipedia.org/wiki/Design" rel="wikipedia" target="_blank">design</a> it properly and show how it should be done. My reaction - bravo! if you do get a chance to do the thing cleanly again, definitely do it. Until of course when your solution has evolved enough and there is a new system or a new guy that claims the same thing yet again.

Don't get me wrong here, I am all for building the correct solution that lasts, but there are so many other considerations that ruin your day, and you have to make a <a  title="Compromise" href="http://en.wikipedia.org/wiki/Compromise" rel="wikipedia" target="_blank">compromise</a>. These compromises eventually lead to your solution being replaced with a better one.This post is an attempt to list and understand some of these limitations that cause us to build a not so right or a compromise design for any application - and which eventually kill your solution.


<h2>The ideal world which isn't</h2>
The hypothetical ideal world which we all would like to believe we live in and which we try to emulate is where we use the right tool that fits the problem and then solve the problem using that with the best design.

If you think about the above statement, then there is nothing about it which anyone will dispute. Except maybe for the fact that what is right is very subjective, and although you can prototype and measure certain aspects of rightness objectively, there are things that are  vague.

Take for example the benefits of building something in <a  title="Java (programming language)" href="http://www.oracle.com/technetwork/java/" rel="homepage" target="_blank">Java</a> vs building the same thing in <a  title="Python (programming language)" href="http://www.python.org/" rel="homepage" target="_blank">Python</a>. Lets say you are building a <a  title="Thread (computer science)" href="http://en.wikipedia.org/wiki/Thread_%28computer_science%29" rel="wikipedia" target="_blank">multi threaded</a> server application which has to serve thousands of requests - you can build it in lesser lines of code in Python than in Java. If you are measuring developer productivity then this is a good point that swings it in favor of Python. Once you have something up and running and you start testing it, you might find that as you approach a few tens of thousands of connections, Java multi threading seems to perform  better then Python. And it works faster. This performance aspect will swing you in favor of Java.

Assuming you do not have any existing <a  title="Application software" href="http://en.wikipedia.org/wiki/Application_software" rel="wikipedia" target="_blank">applications</a> built in Java or Python, you will probably decide based on whether you need productivity or performance. However, if you had applications already built in one of these two languages, and you have developers who are familiar with it, then you will swing towards it. This is an easy decision and you might say that we made an almost ideal choice here based on our requirement.

Lets take another example, where you have an existing platform on top of which you are building something new. The platform provides you means to do a lot of low level work so that you can concentrate on the problem and delegate the low level work to the platform. You build some code to solve the business problem, it works as you would expect, but there is a requirement,lets say about handling transactions, which does not fit the way that your platform handles the. Lets say you want to do one commit for all updates and the platform demands per update commit or vice versa - how would you get around this? You are committed to the platform, it has been around for a while and solves a whole lot of problems very well, and this new requirement is the first one to face a problem like this.

You could do it this way - let the platform commit each update, but you will keep track of each update, and if any of the subsequent ones fail, then you will undo the previous ones - simulating a all or none commit. I can already hear a lot of people screaming "dirty hack!". Yes, it can get ugly if you ran into more issues while trying to undo the transactions manually - but this example was designed to create exactly that sense of ugliness.

You could also say that you need to redesign your solution to think in terms of the platform paradigms and get an buy in from the users on the transactions and find other ways to undo them.

Or you could ditch the whole platform and write your own solution.

Which one of these 3 is the right approach, which one is the not-so-right approach, and which one is the compromise approach? The opinions are as varied as the five fingers on your hand; maybe even more varied. And which one makes your application or platform last a long time - even that is debatable.
<h2>Platforms evolve and dictate the future direction; until they become extinct</h2>
There is a reason old systems are called dinosaurs - they live out their time and purpose and when the environment becomes adverse or changes dramatically they get decommissioned. You don't see anyone running a <a  title="DOS" href="http://en.wikipedia.org/wiki/DOS" rel="wikipedia" target="_blank">DOS</a> program these days do you?

While an application or a platform is in regular use, and there is no other visionary radical redesign that is promising to change the world, not many people realize or even think if the design is right or not so right or a compromise. They build solutions to the platform, extending it and growing it, taking care that no part of it turns rogue and give it all their love. If the people who built the platform or application are around to guide the newcomers, and induct a philosophy into them, then the code actually grows into a very mature and stable system.

However, during the course of time, people move on, get fired and <a  title="New People" href="http://en.wikipedia.org/wiki/New_People" rel="wikipedia" target="_blank">new people</a> join, and each one of them brings their own attitude to it. Business environments change and costs also change, and these generally mean that you do not spend enough time or effort on maintaining the platform correctly and either ignore it or build a whole lot of new components on the application or platform which make it grow beyond manageable levels. When you are building these changes at a fast pace, small design decisions - like for example mandating a commit for every update - can become so tightly built into the whole design that later it is impossible to undo them.

These kinds of problems surface later when you are trying to build even newer features - and that is when you start working around the problems and building solutions that work, but do not seem right. Once you have a certain number of these work around solutions, they start interfering with the whole system and ultimately choke it to eventual decommissioning and a entire new clean solution from scratch.

That is when the person claiming that he can rewrite the whole thing from scratch and do a good jobs wins.
<h2>Why don't we bite the bullet and just build the correct solution the first time?</h2>
If you ever asked a developer or an architect why they built a shitty solution, you will not hear them say that they did that because they could or because they don't care or because they did not know and were simply ignorant. At least 95% people will not give you this answer. They probably will say that they inherited a design, were constrained by time and resources, had to use specific tools because they already had those paid for and buying new licences for new stuff would not fly etc. Sometimes it is the boss - he had a cool idea which he thought of on his vacation and which his boss was very pleased with.

There are many things that go into building a solution, and sometimes, like I mentioned before, it is difficult to objectively determine which one of them you should use. You want requirement A to be met, and that is more important than others then you need to cut some slack and design the system so that A is met at the cost of a few others. Or sometimes, you are forced to do some clever hacking to make the language or the platform do something that it isn't capable of doing in order to meet a requirement - like pre-loading and caching all your <a  title="Graphical user interface" href="http://en.wikipedia.org/wiki/Graphical_user_interface" rel="wikipedia" target="_blank">GUI</a> windows and keeping them hidden because the GUI needs to appear snappy and quick. It will work but when you have a million records? Or when you have a few million records? Maybe it was not a good idea in the first place.
<h2>The right thing becomes obsolete; or gets upgraded and loses backward compatibility</h2>
I remember an instance where a Java API I wrote failed to work in a multi threaded JNI application. The application would load, find all the classes and be fine - but once a new thread was spawned, it would not find a core class and would die with a class not found exception. I banged my head for a week before I found what was wrong - and it really wasn't my fault.

When the JNI wrapper was written, Java was in the 1.3 version and there was just one class loader (I believe) and it was OK to share it between threads in JNI when calling from C/C++/C#/Delphi. But when I upgraded the API, we had to move to Java 1.4, and in Java 1.4, there was a root class loader and a context class loader for every thread. Long story short, the JNI layer my senior had written used custom caching logic to make the API faster for non-Java applications - and his clever coding was now broken. It was not our fault, but the platform had evolved and what was correct was now wrong - subtly wrong, so that it seemed to work but failed!

Now the idealist would say that I undo all that clever coding and give the users the correct solution - right? Wrong, I tweaked the clever code to be a bit more cleverer and still work.

Why would I do such a horrible thing? Because there were 50+ applications that depended on this and they did not give a shit about the fact that this little API wanted to do the right thing and break their applications and make them rewrite things. So much for right solutions.
<h2>Is there a way to avoid this?</h2>
If you can spend time grooming your application every now and then, upgrading aging parts and re-doing broken parts, cleaning up bad code - much like de-weeding your garden or servicing your car - you can actually make sure that your applications age like wine instead of vinegar. This involves time, resources and above all willingness - if you can afford them. Otherwise you just let applications live out their lives and then build something new and shiny which solves the problems of the day using state of the art technology.

&nbsp;
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://blogs.perl.org/users/su-shee/2012/07/is-it-reasonable-to-write-new-code-in-perl-right-now.html" target="_blank">"Is it reasonable to write new code in Perl right now?"</a> (blogs.perl.org)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.drdobbs.com/mobile/232400093" target="_blank">The Rise and Fall of Programming Languages in 2011</a> (drdobbs.com)</li>
</ul>
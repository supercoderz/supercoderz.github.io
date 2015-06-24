---
layout: post
title: "Open development processes - how feasible are they?"
date: 2012-10-20 13:57
comments: false
categories:
---

Open development process - the Utopian dream where you are building on a software platform or use a product and you find a bug; you checkout the source code, figure out what to fix and how; make a fix, add tests, create a patch and submit it. It gets approved, pushed out and you are a happy developer! You fixed a problem, you have a contribution to show and you earn brownie points! This is developer heaven.

And then you wake up from your dream when you receive a email from someone that your attempts at fixing someones code caused more issues and it got escalated.

There are lots of blogs, discussions etc on the internet and even stories like how a developer in Google found an issue with Gmail and he was able to contribute a fix for that even though he had been there just for a few weeks. All these rock star developers make you feel that you are losing out on a lot by working on a closed development methodology in your not so cool company.

In this post, I will try to explain and understand how open development works, and whether it works for everyone or not.

<!--more-->
<h2>Something similar to open development - the global development model</h2>
Before we go to the concept of open development, lets take a look at something that is similar and involves an equally large number of collaborating developers - Global Development Model. I would say essentially this is something that gave rise to the outsourcing and off shoring of work to different countries - someone got the great idea that if we are building software to a predetermined set if requirements and the requirements are sufficiently documented in such a manner that developers across multiple geographies have access to these and everyone has a common understanding of what needs to be done, then the question of who exactly is coding the software is a trivial matter and the code produced by everyone should be more or less the same. So we can get the work done by developers across the world in their own time zones and we should be able to cut down our development time.

A very nice derivation I must say - except that it made one very BIG assumption - that everyone is at the same level of capability with regards to development in a given language, everyone can juggle facts at the same rate and come to the same set of logical steps to solve a problem. They forgot the fact that no two human brains come to the exact same logical solution to a given problem - unless of course they have been put trough the same training over years and their brains have been trained to think in exactly the same way day in and day out. Kind of like a factory where a machine churns out the same design again and again to feed an assembly line.

Talking from personal experience in working for the global delivery model of a large Indian software company, I can tell you that this works very well in many cases and even fails spectacularly in most cases. In cases where this worked, all the developers in the team were
<ul>
	<li>Of roughly similar mentality and ability when it came to breaking down problems</li>
	<li>Had experience dealing with the same kind of problems in the past and knew what would work and what would not</li>
	<li>Did not have and ego or an overly aggressive or overconfident attitude towards the work</li>
	<li>There was a strict hand over procedure between different regions about the code done everyday</li>
	<li>Were willing to spend time and mentor juniors and explain things to them even if it took 100 attempts instead of telling them that they were wasting their time or that they should step up - at the risk of sounding cliched or being offensive, I would say that they did not have the 'you are expected to know how to do your job and don't waste my time' attitude that I see with non Indians - and this helped the juniors to ask lots of even silly questions and get things answered</li>
	<li>There were deadlines, but no moving targets and no overly demanding performance requirements; this helped the developers in different regions have the same view of the bigger picture</li>
</ul>
In cases where this failed, it was mostly due to two things - changing requirements and attitude problems where two persons did not always agree on what to do
<h2>Open development processes</h2>
When we say open development processes, we normally mean the scenarios where there is a core team of people working on a product or feature and they are working as a single team. There are others who are either using this product or feature or are interested in the product and decide to look at the code and contribute like in the case of open source software.

The ideal case would be where the core team provides access to source control so that anyone can checkout the code and built it on their own and decide to contribute. The main things that the code needs to have here is
<ul>
	<li>Ability to easily build the code once it is checked out - all dependencies must be captured</li>
	<li>Instructions on how to setup and distribute the product</li>
	<li>Documentation - either the old school way with large requirement documentation or the cool way where code is self documenting and you can see everything in the comments. Although you would still need something that explains the big picture</li>
	<li>Tests  - you can debate exactly how much testing you want - but basically tests to cover every possible feature of the software. Tests can be used as a means of defining requirements in code.</li>
	<li>Coding standards</li>
	<li>Instructions of what is expected when you want to submit a patch</li>
</ul>
And then of course the most important thing that everyone stresses about - don't be attached to the code because anyone can come and change your code as he pleases and it will be accepted as long as the tests pass. The tests are more important than your beliefs or attachment.

This works - if you look at the large number of community driven open source projects on the internet then you get a good picture that this works. There are so many cases where you go to Github and fork your favorite project and make a fix or two and then contribute back to the community. Surely with the open and merit driven attitude, and with all the tests, all these patches get accepted. And they never break things.

Or do they? Do they really have patches that are not accepted? Is there no heart break or disagreement? Am sure there is, but no one talks about these while writing a case study for open development.

And if you imagine the same in a enterprise setting (no I don't mean the misguided belief that enterprise means a large architecture, I mean like in a BIG company) then you can easily see how much of a pain it will be if a change made by someone causes a production issue, and because of this how protective someone would be about their code.
<h2>Of course you can have code reviews and release control, and you have tests!</h2>
Tests, automated or manual, they only test what you ask it to test. If you write a test case to verify something and it passes, then it means that the test case passes. It does not mean that what you wrote or the behavior that you tested is what it should do. You could be testing a wrong use case. Surely all tests will pass, along with the wrong tests, and then someone will do something slightly different. But it is not really as morbid as I am making it out to be, and tests do help in making the system more stable.

Code reviews and release control also help to a large extent - they help iron out lots of issues.

Then what is the problem? Lets say you have a code base that has a few hundred thousand lines of code. It grew organically over many many years. And you are making a small change to one part of the code. This code is referenced by many other parts of the code directly or indirectly. In the ideal world, there will be tests that will capture all these and they will get sufficiently tested. But there is something about testing - not everything can be unit tested. So we resort to using mock objects to test dependencies. These mocks always return a certain number of predetermined valid and invalid results. The dependency itself is tested separately. And then there are integration tests which test the entire system.

If you have spent enough number of years actually coding something rather than in academic discussions of coding, you know that things slip through the cracks even for the best developer out there. Its simply unavoidable. There will be use cases and code paths that cannot be 100% tested automatically. Plus there will be things like tests running on a database with 100 rows and code in actual production running on database with a million rows - the loads and exact environment conditions will be different.

All these issues result in people controlling how much code is allowed to be changed by people outside a core set of developers who know exactly what they are doing and have 200% concentration on things. And this leads to systems being developed in a closed manner. You can either meet deadlines by shutting out unwanted disturbances or you can spend time debating till you miss the deadline and the paycheck.
<h2>Collectively arriving at wrong decisions</h2>
All collaborative development processes depend on a bunch of people democratically agreeing to what is required, what is correct and  what is wrong. There are a large number of processes that are followed. Processes are a pain and take out the human factor and common sense factor from decisions. Everything must be backed up by numbers and scientific results.

But sometimes, it is possible that someone is forceful enough to convince a lot of people and that someone might convince everyone to do something that is not correct. For example, lets say you want to optimize the way you connect to a database and get a unique ID. And someone has an idea - he has even designed a test, taking a representative sample of the production loads on the application and the test proves that it will be a spectacular improvement. Although the loads were a representative sample, they do not account for a sudden spike or a sustained spike in the load - and this change was approved because of an overwhelming vote.

It will fail one day or the other and that will cause some issues, it will get fixed and more checks will be added.

But this happens few more times.

And this leads pressure which leads to fear factor. Unless the managers and management understand that sometimes issues cannot be totally avoided and best you can do is to fix them quickly (they don't understand this in most cases) and if there is someone on the team who wants to build something that never fails  - this fear factor or pressure can be very very damaging to morale.

And this is another reason why people tend to move towards a closed approach to development of key software components.
<h2>Conclusion</h2>
I have only touched one few aspects of open development and how it can go wrong. All these apply to closed to development as well. And they can cause the same issues. But in case of closed development, it is possible to ensure that the small set of developers agree to things and do not deviate. It is easy to control how much deviation is allowed.

In a larger or open group, it becomes an overhead to maintain all the developers in a given track and sometimes this can lead to other issues.

So the choice of whether to go open or closed depends on the complexity and criticality of the software and how much of the co-ordination overhead you are willing to take.
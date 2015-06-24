---
layout: post
title: "Fear of regression tests and application instability"
date: 2011-12-29 01:02
comments: false
categories:
---

[caption id="" align="alignright" width="300" caption="Image via Wikipedia"]<a href="http://en.wikipedia.org/wiki/File:Guitest.jpg"><img class="zemanta-img-inserted zemanta-img-configured" title="Guitest" src="http://upload.wikimedia.org/wikipedia/en/thumb/4/42/Guitest.jpg/300px-Guitest.jpg" alt="Guitest" width="300" height="144" /></a>[/caption]

We all have heard about or experienced first hand some situations where an application owner comes back saying that a seemingly small change will take an obscenely long time because it would impact the other components and regression testing will take time. In these cases generally you will also see someone from you team or above you start a long winding discussion on why you should have test packs and <a class="zem_slink" title="Unit testing" href="http://en.wikipedia.org/wiki/Unit_testing" rel="wikipedia">unit tests</a> so that you can automatically <a class="zem_slink" title="Regression testing" href="http://en.wikipedia.org/wiki/Regression_testing" rel="wikipedia">regression test</a> the whole thing and making a change takes only a very very unbelievable short time. Am sure that sounded familiar - whichever side you were on or not on.

In a way if we think about it both sides make a valid point - to quote from <a class="zem_slink" title="Fight Club" href="http://www.rottentomatoes.com/m/fight_club" rel="rottentomatoes">Fight Club</a>, on a long enough time scale the survival rate of anything drops down to zero. You new application with all the shiny features and a shiny regression test pack will not be shiny after it has spent a few months or years out in the open with users asking for  ad hoc changes.

You will ultimately end up in a situation where you will dread making changes. Just why do we get into such a scenario?

<!--more-->
<h3>Applications evolve, and age like vinegar</h3>
When you build a new application from scratch, you know exactly what the application has to do, how it has to be done and what it should look like. Hours will be spent in meeting rooms discussing every minor detail, things will be tested documented and proven to work as expected. Since it is a new project there will be zeal and time and patience to get the right  thing done, and using all the latest paradigms it is possible and fairly common place to have a regression test pack in place which will be run by the <a class="zem_slink" title="Quality assurance" href="http://en.wikipedia.org/wiki/Quality_assurance" rel="wikipedia">QA</a> team or whatever they are called.

But all this is pre-release. Once the application is released and users start to use it, they will want more. Some users are nice and want good features that make sense. Some others are not so nice but they will ask for features that to them are nice to have. These could be sometimes like taking a Ferrari and plastering a vulgar paint job on it. Ouch! but the owner wanted it.

In a good time, technology can push back on these bad requests that do not do harm to the application, but since nowadays our main aim is to give what the business wants so that they can make money and in turn that can trickle down to us - we generally end up doing what they ask. Or someone will make sure you do.

Plus, with all the deadline and timeline crunching and cost cutting, we never get to spend enough time on our changes - we need it yesterday and probably done by the lowest bidder.

Also, with time, applications evolve to meet changing needs and it is not always possible to rewrite the whole thing - so you just hack it.

With all these ways in which people, processing, time and our approach to cost cutting etc make an application evolve, it is not always a happy ending where it turns to wine - it more often than not becomes vinegar - a bad application which is too much to maintain and hence is replaced with a new one which everyone claims will have better regression pack.
<h3>Evolving applications and not-so evolving <a class="zem_slink" title="Test case" href="http://en.wikipedia.org/wiki/Test_case" rel="wikipedia">test cases</a></h3>
Like I said above, applications evolve. And with that so do the business cases and the tests. In a perfectly reasonable world where the application was built it do a certain kind of thing and nothing else, as long as it did the same kind of things, it is possible to keep these business cases in sync with the tests.

But, these days, everyone wants re-<a class="zem_slink" title="Usability" href="http://en.wikipedia.org/wiki/Usability" rel="wikipedia">usability</a>. Write once and use a hundred times to save effort and the cost of paying someone to build it. Also the speed with which you can make the change. Good ideals. And they work too. But there is a catch - when you build a re-usable component, it is built to do a common task that is shared by a certain kind of similar things. Like if you built shopping websites for a living, then the shopping cart and payment processing parts would be pretty much the same and reusable. Or to take a more physical example, think of AA or AAA size batteries - any gadget that can take that size of batteries can use them. Now in this day of smart gadgets when we need more <a class="zem_slink" title="Battery (electricity)" href="http://en.wikipedia.org/wiki/Battery_%28electricity%29" rel="wikipedia">battery life</a>, these wont work - so you will need to throw in some more work to do build a better battery.

Now the general business case for a battery that it should power the gadget will still work - that is regression will work, but any new cases specific to this battery will need to be added. You have replaces a re-usable component with another, but you still need to update the test cases to account for this new component and its intricacies. And this needs time and effort to do.

In a typical software upgrade scenario where the <a class="zem_slink" title="Time to market" href="http://en.wikipedia.org/wiki/Time_to_market" rel="wikipedia">time to market</a> or release for this update is super short owing to business pressure and the requirement that we should be agile in our responses to business requirements, this does not happen. You add some new cases and you miss some. Depending on what you missed, you might end up with a spectacularly failing application as you add different components and miss different cases each time.
<h3>Re-usability means everyone uses the same plain old boring stuff - mostly</h3>
I am not 100% sure but I would wager that re-usability of software was an idea born in the factories where they could use a single mold and stamp out thousands of components from that covering the costs and making profits. They would still paint the stuff in different colors but the core remains the same.

When it comes to software, it is possible to reasonably standardize the core and paint the <a class="zem_slink" title="Graphical user interface" href="http://en.wikipedia.org/wiki/Graphical_user_interface" rel="wikipedia">GUI</a> as per <a class="zem_slink" title="User (computing)" href="http://en.wikipedia.org/wiki/User_%28computing%29" rel="wikipedia">user preferences</a>. However, it also means that all the external interfaces that we use will be able to be fit into this core. A standalone application that does not talk to any other application will not be doing much so that is not really considered a candidate for re-usability. If you don't talk to too many things and do your own thing then what will change that often?

In the real world, all interfaces do not talk the same way - so your core code will have as many ways of doing things as your external applications have. Some of them might need to use a file based interface - files are so 19th century! - yes but they are robust and we cannot change it.

So we keep adding new interfaces instead of re-using, or we keep hacking at things until they bend to our will and the ultimate result of this is that the tests that we wrote and were so proud of, they wont always work. Some will fail and fixing those issues will cost time and money which we don't have.
<h3>People leave, and take with them their brains</h3>
Losing someone on your team is a tragedy - no matter how much wiki pages the person wrote and how commented his code is, when a person leaves you cannot step up and fill his shoes without causing or having issues. The thing here is that software is all about thoughts and how we design things based on those thoughts. The way someone wraps his head around a problem and a solution is not the same as someone else does it. So if i were to give you my idea and have you make it your own, it will not be easy. You will take time and you will miss the subtle things.

People moving away is one of the many reasons why reasonably well written components die an slow death. The new guy might not understand the thing or he might be a smug new developer who does not believe in the same principles that led to the original design or share that passion.
<h3>So how exactly does all this lead to a fear of regression ?</h3>
Fear of regression is not some thing that arrives on a CD and is installed. As we saw above there are different reasons why and how an application evolves after it is out in the open. All these contribute some good things but add a small nick here and a small dent there to the overall tests and test coverage and regression packs etc that you have put in place to protect against this exact problem. Also people leave. All these collectively over a period of time lead to the application getting into a state where you don't know what will break what.

Once you get into such a state, you will shy away from making any quick or major changes. You will add buffers, test find issues and have delayed releases. And of course anything you don't catch will cause PROD issues that will have you scratching your head and trying hard to explain to the business.

As you can see it gets kind of ugly - so in the interest of cost, effort , issues and evolving technology you decide not to make any changes and re-write the whole thing as a new application. Or you probably quit your job and do something else.
<h3>Is there no way out?</h3>
Well there are a couple -
<ul>
	<li>Take more time to fix things or make changes</li>
	<li>Understand and analyze all aspects</li>
	<li>Do not hack your code to appease one user or a bunch of them - the application is important too</li>
	<li>Properly cross train people so that when one leaves others can do a better job of taking over</li>
	<li>We need to periodically review the test cases and fix them  - see the image above for a sample flow</li>
	<li>We need to look at the code once in a few release cycles and clean out bad parts and make it conform to standard model.</li>
</ul>
There are many others too, we need to do what suits us and our situation and hope that we don't get into that corner which we all don't like. Even if we followed all the techniques that I mentioned above, it requires commitment that we will continue to do that over the entire life of the application and not give up in the face of other issues.

Any application that has survived long enough and does not have all these issues is because it has been able to hold its position and dictate how exactly it will change and in a way that will benefit everyone. It always helps to look at the bigger picture - just ensure that while looking at the bigger picture you don't lose sight of the things in front of you.
<h3>Conclusion</h3>
In the end its all about balance - you cannot always think of the bigger picture and the stability, you have to sometimes make changes for the short term as well. As long as your heart is in the right place and you make up for any sloppy short term work by cleaning up later it should be good and you will not end up in a situation where you end up fearing any change because you regression tests will fail.
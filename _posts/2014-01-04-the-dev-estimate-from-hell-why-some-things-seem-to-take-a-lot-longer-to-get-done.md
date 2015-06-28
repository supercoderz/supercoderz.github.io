---
layout: post
title: "The DEV estimate from hell - why some things seem to take a lot longer to get done"
date: 2014-01-04 14:22
comments: false
categories:
---

We all have given estimates for development work - and we all have either missed them at some point or have been asked why it takes so much time to get something done. How and what we respond to these questions varies - but in all cases the common theme is that there were either scope changes, or unexpected issues and defects, or integration problems - and we always promise to do better next time.

But we don't - we repeat the process again next time we miss a estimate.

This post is an attempt to understand a few issues with estimates.



What do we need to give an estimate anyways?
<ul>
	<li>We need the requirements</li>
	<li>A basic spec</li>
	<li>We need to know whether there is any existing code that we will extend</li>
	<li>And we need to know how many people can work on this, and if the work can be split up between more than one person</li>
</ul>
So we know all these - can we make an estimate that sounds reasonable?  We can come up with a reasonable estimate based on these, and we can add some QA and other testing time. And we could get the job delivered in that estimate. But that is not the most important problem in most cases - the most important problem is that the users or the person who is going to pay for the work asks why it takes so much time, and sometimes asks if the same work can be done cheaper and faster. This might not apply to companies like Facebook or Google or a product company but it applies to most companies.

So why does this happen?
<h2>The myth of prioritization</h2>
There is always a lot to be done - a lot that can be done to add value or to bring more business and make more money. And given a list of things to do and a set of people you can throw at doing these, there is always a tendency to do the things that give the most value, the most bang for buck. This could be something that is small work and more value or something that is more intensive and takes more time.

So we make a list and we stat working through the list. The list would have been made at a certain point of time based on what everyone wanted - and this is very subjective. What anyone wants can change over period of time, so lets say we put something at #5 on the list, then by the time we worked through #1 and #2 and do a review, there would be someone who feels that #5 is too far away and he would want that ASAP now! Why does it have to wait.

And more troubles if the work done so far took more time than expected.

Clearly, trying to make a priority list is a good idea but as time goes on, the priorities change and it seems to us that something is taking longer than it needs to.
<h2>Piling up BAU</h2>
Business as usual (BAU) is the work that we need to do to support what we have already done. If software was something that could be written once and then left to run in the wild with no need to have anyone looking at it or tuning it then it would be great! But that is not the truth.

For every piece of code that is running out there doing some piece of work, there are a bunch of people looking at the it making sure it is working fine,  someone getting new ideas when using it and most important of all odd defects and issues showing up.  The issues need fixing and that takes time - this is not something that can be planned for so when it happens it will take some time away from the other things that you want to do.

Can we minimize BAU? We cannot minimize the defects and issues, but we could try to take all the requests and improvements and put them on the priority list, maybe combine them with a related item that we already have on the list. This will mean that apart from the items that we need to get done, we have BAU items competing for attention and time.

The other approach to handling this is to pad the estimates to ensure that we are never lose time because of BAU. This leads to estimates looking bigger, and things taking longer. Which doesn't really solve the problem.
<h2>We cannot cut testing to speed up delivery</h2>
Priorities, BAU items and the actual effort required to do the work cannot be reduced, there is tendency to think that we can reduce the amount of time testing - we can do testing in parallel, or start testing incrementally from the beginning etc etc. Sometimes they even say that if you write unit tests then you don't need a lot of formal QA - writing unit tests will pad the development time.

In many cases though it is not possible to start testing early or use unit tests because of the way the application is built. Very often I find myself in situations where my part of the code which is supposed to create some data is ready and I can see the data being generated. Hell, even even the unit tests covering all corner cases work fine. But I am waiting for some other team to finish their work so that we can test the whole thing end to end and see my data flowing all the way to the end system which the users look at.
<h2>The waiting an co-ordination</h2>
Anyone who has worked in a place that is not a start up or where a single team does not do the complete end to end development of some piece knows that you always have to work with other teams. Teams who sometimes have their own priorities and resource availability.

The coordination not only involves agreeing to and meeting deadlines, but you need a constant dialogue with the other teams to ensure that they deliver according to the requirements and also are open about any potential issues. Its one thing to define a standard interface between components and a totally different thing to expect that work 100% of the time without throwing up new use cases or corner cases that need special attention. This takes a lot of time.
<h2>Developer productivity</h2>
All the points that we have considered so far do no talk about the actual developer - we were talking about things that effect the work schedule. But developers also matter. It is safe to assume that in most places the team that is going to work on some deliverable is competent enough and knows what to do.

However, being competent and knowing what to do is not all - you also need to be productive. If a person working on something gets distracted by any of the above items like BAU or defects or meetings then it takes time for them to get back into the work and pick up from where they left off.

Letting developers work in isolation is not a good idea either - making decisions on priority and timelines without involving the people actually doing the work will result in skewed estimates.

This is not something that can be solved quickly and each place needs a different approach to handle this based on the developers, the task being done and other factors.
<h2>Conclusion</h2>
We can see that there are things other than lazy developers and padded up estimates that lead to things taking more time to get done. Depending on the industry and company there might be lot of other factors like cost etc which determine exactly what resources you throw at a problem.

So next time you see a estimate that you think is outrageous, or someone calls your estimate outrageous - think about these things too.
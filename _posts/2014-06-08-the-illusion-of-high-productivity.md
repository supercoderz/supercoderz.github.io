---
layout: post
title: "The illusion of high productivity"
date: 2014-06-08 16:05
comments: false
categories:
---

Nowadays the emphasis seems to be on developer productivity - you have the tools and libraries that have been built by people who know how to do it right, these are open source, and there is a community to help you out. Or there is a large corpus of previous projects in the company that you can simply reuse and save lot of work.All you need to do is get the libraries, follow the examples and you should be good to go! You should be able to build castles out of thin air!

But that never really happens. Why?
<h2></h2>
<h2>Just a decade ago</h2>
I said that productivity doesn't happen at the level that we expect it to happen, but that doesn't mean that it doesn't happen at all.

Almost 10 years ago, I joined a project where we had to install a piece of software on Solaris. We had never done that before, no one we knew had, and the documentation was sparse. We took what we had and we did what we thought was correct - but it did not work. Why? We did not figure out the why for another 6 months. There were tons of tar files with libraries and environment settings that were supposed to make the whole thing work, and it would not work. We had Google, we searched, we put questions on forums and all that but nothing helped - it was because the software was part proprietary, was used in niche telecom use cases and there was not enough experience around that people could answer it.

We hated that work. But a couple of years later when we handed off the work to another company, they had it easy - by then the software was cleaned up to use more standard libraries and also a few communities sprung up on the internet with just enough information to get going.

I cannot imagine facing the same issue today - even though I work with a large platform built over 8 years, and most developers gone, I can make sense of any issue by looking at the libraries and asking the right questions.
<h2>How did it change?</h2>
In the last ten years, not much has changed about how we code - we still take a problem and we attack it the same way we always did - breaking into pieces. But in these ten years with the explosion of open source, and the tools that help us collaborate - mostly on the internet - we are able to talk to more people more often. And this we have figured out how to ask the right questions so that we get the answer that helps us solve our problem.

We have evolved with the tools.

This applies to any company or individual who works in an open environment. However we all still work on a lot of proprietary stuff and in silos where we cannot openly ask for and share information. I once had an issue with Hibernate which took me a month to solve because I could not put the exact code on the forum, and with my half baked info, the solutions that I got did not move me forward.

So it still works and fails and needs some more improvement.
<h2>But we have cool tools and programming languages</h2>
We also improved on the IDE's and programming languages - we now have lots of dynamic languages and support in languages for things we did not have few years back. These indeed make us get our work done faster. I can now build a website in Django faster than what I could with Java ten years ago.

All this speed comes into picture only after you have taken a look at the problem and have broken it down enough to be able to start coding. The while process of taking the problem and sufficiently breaking it down is still not that much faster. Agreed there are new methodologies like scrum and agile that let you start with small parts and iterate over that to get the full solution, but it still isn't much different.
<h2>All this creates an illusion of high productivity</h2>
We all build tools for users - and users generally are not usually able to appreciate how development work progresses. So when they are given an estimate or told that there are tools out there that can speed up development time, they expect us developers to be more productive than last time and hence deliver the project in a lesser time.

However, in this urge to make the use of best tools and deliver quickly, we forget that the core part of delivery - the problem to solve - hasn't gotten any smaller or easy to manage. It still is the same problem and it is hard to analyse and build a correct solution even if we throw the most efficient processes and tools at it.
<h2>The human factor</h2>
Development is done by people, even in cases where there is automatic code generation, we need people to do some work before the code can be generated. And this human factor decides how fast things can be done or not. A person who gets to work on something for the entire day without any disturbance and with high concentration can get things done a lot more better than someone who has to deal with multiple issues in a day. Its hard for the mind to context switch between two completely different issues.

And no matter how good the tools are, if the person using them isn't allowed to use them effectively then there is no way you can get the expected results.
<h2>There are only so many common use cases</h2>
The other factor that impedes high productivity is the commonality of use cases. When we build a library we assume that it will be used in a certain way, and that we have done all that is needed to ensure that we have covered the most common cases where it will be used. Obviously this is wrong and we will always end up with cases where people come and expect us to add more features that sometimes might conflict with what we want to do or with the best practices etc etc.

The solution to this is to build a library that satisfies the most common cases. And the let people build on top. We expect that the developer community will get excited about what we have to offer and they will share their exploits and eventually as a community everyone will figure out how to get their job done.

And this does happen, eventually, after a certain time has passed. There is no limit on how long this will take and there is no guarantee that this has covered all the cases.

When we build tools and platforms in large enterprises we do the same and we assume that something we built in the previous project can be reused - and we factor this into the estimate to come up with the magic developer productivity gain which helps us cut down the delivery time. But we soon find out that there are things that were not taken care of earlier or are new and now we need developer time to fix these. And these take time.

And this is not good for productivity.
<h2>Conclusion</h2>
Although there are libraries, tools and techniques and what not to make coding faster and easier, there are still factors that come in the way of achieving the ultimate high developer productivity.
---
layout: post
title: "Building configurable enterprise services"
date: 2011-05-31 20:32
comments: false
categories:
---

Sometime back when I was in <a  title="India" href="http://maps.google.com/maps?ll=28.6133333333,77.2083333333&amp;spn=10.0,10.0&amp;q=28.6133333333,77.2083333333 (India)&amp;t=h" rel="geolocation">India</a> and talking to my brother, he mentioned that he could not get himself to work on building something abstract like <a  title="Computer software" href="http://en.wikipedia.org/wiki/Computer_software" rel="wikipedia">software</a> which mostly takes shape inside the developers head. He works in accounts and finance and is more used to dealing with numbers and facts that exist on the books. To an extent he is correct - software starts as an idea in someones head and then manifests itself as a set of applications or services that process or provide data that performs the tasks and in these days of big enterprises spread across the globe and using IT to drive <a  title="Business" href="http://en.wikipedia.org/wiki/Business" rel="wikipedia">business</a>, it helps make money by providing efficiencies.

<a href="http://commons.wikipedia.org/wiki/File:Software_spanner.png"><img title="Programming in the large and programming in th..." src="http://upload.wikimedia.org/wikipedia/commons/8/82/Software_spanner.png" alt="Programming in the large and programming in th..." width="239" height="184" /></a>


<h3>Why build software?</h3>
Why do we build software? In my day job I build software for a bank that provides financial services to clients and makes money for them. It makes this money by leveraging the different opportunities that exist across geographies so it becomes important that we build software that can provide all this information in a consistent manner and enable our business to take decisions that will make the best of the opportunities.

Given the nature of business and the competition out there, there is a near constant demand from the business in any enterprise to provide the best and most correct information at all times. Not just provide information but to distill it and create dashboards and reports that drive business decisions and then archive all these reports and analysis for years to come so that in future we can use this to predict trends and use past experience to make a decision about an uncertain future.

Its all jugglery of facts and figures that lead to money.
<h3>Is it hard to build the software?</h3>
Of course it is, not because you don't know what to build, even when you know exactly what to build, the problem is to build it in the correct way so that it can serve the purpose of not one but perhaps hundreds of users and above all provide correct results consistently without failing. My worry with all the code that I write is not that it will not work, but predicting when it will fail and accounting for that.

But then these can be solved. What cannot be solved is the universal constant of change. Businesses no longer follow a set pattern that lasts for years. Management does not make decisions that are set in stone for years and so the software need not change. Software needs to change more dynamically than the business decisions themselves if it wants to keep up and stay relevant.

And this is where we fail, many times.
<h3>Building software to solve all problems, of a given type.</h3>
Given a <a  title="Problem statement" href="http://en.wikipedia.org/wiki/Problem_statement" rel="wikipedia">problem statement</a>, any half assed coder out of school will build code that implements <a  title="Solution" href="http://en.wikipedia.org/wiki/Solution" rel="wikipedia">solution</a>. This is provided he knows the domain and clearly understands at least one solution to that problem. Throw in some years of experience and he will build something that is more polished and can be used by a larger audience. Maybe even scalability or even speed.

But how often does someone build something that can be used to solve the same problem for a different situation without making any or too many changes? Few times.

Enterprises these days do not run as a single monolithic piece of software that does everything. They run as a set of co-operating <a  title="Application software" href="http://en.wikipedia.org/wiki/Application_software" rel="wikipedia">software applications</a> talking to each other by some means. So each part does a small defined task. And sometimes this task is repeated for different contexts. The essence of the task is the same, just that it gets similar kind of information from a different location and then processes it to produce a slightly different output.

If there is a critical mass of situations where this is required then it makes sense to build a single piece of code that can be configured to work in all these cases. You just tell the application what to do, and then configure where to get the data and where to put the results. You could even tell it to look up the what to do part from a set of rules which in turn can be configured. The code plus the configuration is tour application.
<h3>Problems with building configurable applications</h3>
The problem is more human than technical. Given a set of numbers, if you ask someone to write code to add them up, the person will know how many numbers are there and can easily add them up. If you put in the <a  title="Complexity" href="http://en.wikipedia.org/wiki/Complexity" rel="wikipedia">complexity</a> that there can be an arbitrary number of these numbers and you do not know that upfront, then there is an additional complexity to be introduced that the code should know when the numbers end so that it can report the sum. This it not too bad. What if you said that not only is the list of numbers arbitrary, but the operation to be performed on them is also not known upfront and you need to decide that at run time based on input?

What we are trying to do here is to increase the complexity of the solution by introducing more and more abstractions. And as we keep on increasing this, it becomes difficult for the human mind to handle this. If we were trying to solve an <a  title="Enterprise software" href="http://en.wikipedia.org/wiki/Enterprise_software" rel="wikipedia">enterprise level</a> problem, and by adding more and more complexity or abstraction it takes more time to solve the problem, then we will not be able to meet deadlines.

Also the fact that not all developers and manage such complexity or build effective solutions to these means that sometimes such solutions will fail. This leads to the wrong assumption that all generic and configurable solutions are wrong and the Keep It Sweet and Simple (<a  title="KISS principle" href="http://en.wikipedia.org/wiki/KISS_principle" rel="wikipedia">KISS</a>) principle looks a lot more tempting. So we do the simplest thing, often just <a  title="Hard coding" href="http://en.wikipedia.org/wiki/Hard_coding" rel="wikipedia">hard code</a> and tightly couple, a solution to a problem. And later if we see a similar problem we just copy the old solution and edit where required. The fact that we could have created a reusable component out of the original solution to solve the two problems escapes our mind. And at some point even suggesting such an approach seems very radical.
<h3>How do we solve this?</h3>
There is no ready solution to this - just like every other problem in the world. What is needed is a decision, and a balance between what we want to do and what are the long term goals. Also the developers need to know what are the best tools and techniques out there and whether they make sense. It is not always possible to use the latest shiny framework developed by your favorite hacker because as an enterprise there is the responsibility to use a common and stable technology stack that can be supported. Withing that technology stack it will be possible to build configurable and easily reusable software applications.
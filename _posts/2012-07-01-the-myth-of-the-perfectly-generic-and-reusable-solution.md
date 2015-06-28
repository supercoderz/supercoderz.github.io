---
layout: post
title: "The myth of the perfectly generic and reusable solution"
date: 2012-07-01 16:22
comments: false
categories:
---

[caption id="" align="alignright" width="300"]<a href="http://commons.wikipedia.org/wiki/File:SOA_Elements.png" target="_blank"><img  title="English: Elements of a Service Oriented Archit..." src="http://upload.wikimedia.org/wikipedia/commons/thumb/d/d4/SOA_Elements.png/300px-SOA_Elements.png" alt="English: Elements of a Service Oriented Archit..." width="300" height="197" /></a> English: Elements of a Service Oriented Architecture Polski: Elementy architektury zorientowanej usługowo Українська: Елементи Сервісно-орієнтованої архітектури (Photo credit: Wikipedia)[/caption]

Often times when I set out to do something new on the job, I hear a simple requirement over and above the actual business function - keep it generic enough that it can be reused. And I set about it to achieve the mythical generic solution that can be reused again and again for a variety of similar <a  title="Use case" href="http://en.wikipedia.org/wiki/Use_case" rel="wikipedia" target="_blank">use cases</a>. Do I succeed? Depends on how you define success. Given that my hands are tied because of an existing platform and the solution has to fit in this rather than be a rouge process, I make compromises and we end up with something that sort of looks much better than hard coding but also cannot be directly reused without making small changes. The closest I got is making a component that can take Javascript functions in configuration and I can use these scripts to tweak behavior as I see fit.

This makes me think - is it even possible to achieve a perfectly generic solution that fits every need that has a similar use case?

<!--more-->
<h2>It obviously depends on the use case</h2>
It is kind of obvious that if the use case is not generic enough and cannot be applied to a different set of inputs without any change, then the solution cannot be generic. If you are building something that solves a <a  title="Business process" href="http://en.wikipedia.org/wiki/Business_process" rel="wikipedia" target="_blank">business process</a> which is very unique to your company for whatever legacy reasons and is not an industry standard, then there is no reason to believe that you can find an off the shelf generic solution for that. Similarly if the process was specific to your line of business then there is no reason to believe that it will be used by someone else in the same company. These are cases where trying to build something generic will only kill time.

In other cases, the generic features might have already been distilled and built into the language you are using or there might be libraries for the language which provide the same functionality. All you need to do is use that code and write something on top that is specific to your use case. In most of these cases, unless there is a commonality in all the ways that you use that library, you cannot take out a generic reusable solution out of it. It again depends on the percentage of commonality etc.

This does not mean that you cannot build generic solutions. Design paradigms like <a  title="Service-oriented architecture" href="http://en.wikipedia.org/wiki/Service-oriented_architecture" rel="wikipedia" target="_blank">Service Oriented Architecture(SOA)</a> are built around that fact that when you are building a large enough system there will be common functionality that will be required by all the components. This functionality is such that it can be extracted into its own separate component and then that can be exposed as a service which can be discovered by those components that need it and used appropriately. SOA can be implemented in many ways and depends on the IT landscape and the technologies used, but in all cases it lets you build a fairly easy to reuse set of components.
<h2>Making something easy to reuse</h2>
When you build a solution, using SOA or any other paradigm, there are parts of your logic that are dumb pipes just processing data on the inputs and sending them to the output - transforming the data so to say - without really worrying about what the data means. And there are parts of the logic that inspects the data to determine what it means and then creates the outputs which can be processed by these dumb pipes.

In a world where there are a large number of generic components being used by all others, the dumb pipes are the parts that become the reusable solution. They can be built once, highly optimized and to be scalable and do the job. They would run and never fail inspite of all the load that you throw at them. Very good!

Then there will be components that inspect the data and make decisions. These would need to know what the data is and what are the rules that need to be applied to the data. The simplest solution to this is to <a  title="Hard coding" href="http://en.wikipedia.org/wiki/Hard_coding" rel="wikipedia" target="_blank">hard code</a> everything - its the fastest in terms of execution, does not have dependency and cannot fail unless you change the code again. And you can always guard against coding issues using unit tests, <a  title="Test-driven development" href="http://en.wikipedia.org/wiki/Test-driven_development" rel="wikipedia" target="_blank">TDD</a> and all those cool methods can't you?

The other way to build these will be to identify the commonality in how the rules are applied, the rules in a common representation, for example a mini language, and then build something to take the name of a rule and some data and apply it. Then this can do that for any kind of data as long as it can identify what rule to use. This is such a common approach that there are libraries called rule engines which do this. They work pretty well.

So now you have some user data coming in, you have a rule engine which can be configured with the type of the data and the rule to apply for each new data that you need to process, and you have dump pipe services to take care of downstream. Hurray you have a reusable solution! Do you?

In a way you have, in a way you don't. There is the question of how you are getting the data - you could have a <a  title="Graphical user interface" href="http://en.wikipedia.org/wiki/Graphical_user_interface" rel="wikipedia" target="_blank">GUI</a> that lets the users enter all the data, you could have templates in the GUI that pre-fill some data or you could actually build a very very specific non reusable GUI - not many people seem to complain about GUI components that are not reusable.

Can anything go wrong?
<h2>State, <a  title="Scalability" href="http://en.wikipedia.org/wiki/Scalability" rel="wikipedia" target="_blank">Scalability</a> and <a  title="Business transaction management" href="http://en.wikipedia.org/wiki/Business_transaction_management" rel="wikipedia" target="_blank">Application performance</a></h2>
When there is an application that processes something, there will be the <a  title="State (computer science)" href="http://en.wikipedia.org/wiki/State_%28computer_science%29" rel="wikipedia" target="_blank">application state</a> - how does the application or the data in the application look like at any instant of time. Any component in any application will always be interacting with this state and mutating the state. Other components will be depending on this state and might not be able to work if they are not able to access or mutate this state fast enough. How fast the application as a whole moves depends on
<ul>
	<li>How easily each component can manipulate the state independent of other components</li>
	<li>How quickly the state becomes available to other components.</li>
</ul>
If you had a service that was perpetually waiting for something to become available then it isn't really going to go anywhere. You need to ensure that all your fancy reusable components can work independently on this state. If there are two components which need to wait for some change in the state and cannot be uncoupled, then it is better to merge them into one.

The problems with state lead to problems with scalabillity. Scalability means handling greater loads easily. One of the easier ways to do this in a SOA world or when there are dumb pipe services is to spin up more instances of the services. If these services are tightly coupled to one another because of state, then they will be stepping on each other and will bring down the whole system instead of showing any improvement.

Lets think about the state and scalability for a moment here. In most applications that I have seen, there will be some dependency which cannot be uncoupled. And this dependency will mean that a certain part of the solution is not as generic as you would want it to be. There will be some thing that will be written very specific to a problem that was being solved. If you want to reuse this component for some problem where the exact same logic does not apply, then <a  title="Reusability" href="http://en.wikipedia.org/wiki/Reusability" rel="wikipedia" target="_blank">reusability</a> is killed. It is possible however to built it using a standard design pattern so that you can easily plug in the specific logic - then all you need to do for the other use cases is to rewrite the logic in the plug in. This works in most cases, but still it is not a 100% generic solution.

This leads us to the next issue - performance. Business likes solutions that react in microseconds -  who doesn't? I once worked with a guy who felt that if the inbox had a 1000+ items, it should still load in 3 seconds. I kind of agreed with him because I knew it could be done using lazy loading - trouble was our design wasn't built for that. The whole thing was built on a few rule engines, some pluggable logic modules and some XML over JMS web services (for reasons I cannot elaborate) and all that. While each part of the system was what we would expect and worked very well at small loads, it failed when you put in 500+ users at once. It was initially built for 300 users at peak and we did not see it growing so soon. But it grew anyways.

All our generic reusable stuff was adding minor delays on the way and the end to end latency increased from under a second where most people don't notice into few seconds where it is easily noticeable. Performance is something that can get killed when you have lots of complex nested calls to execute your logic as opposed to simple inline code. Modularisation and inline code have their own uses and we cannot claim for one to be good or bad over the other.
<h2>External connectivity</h2>
There are some systems that give you one connection and then they let you send all kinds of data and do all kinds of interactions over that connection. They take care of handling it. Then there are applications that will give you one connection for each data type or type of interaction and then expect you to send slightly different messages or data on each connection. What happens here is that the end points of your solution either work as one single component which is easy to build once and reuse or they get fragmented trying to meet each unique requirement and each time you add something you end up adding one more service doing the same thing just a little bit more differently this killing any sense of generic solution.

This is usually the last nail in the coffin that kills your hopes of building a perfectly generic solution.
<h2>Conclusion</h2>
While it is possible to build  generic solutions at one level, it is not always possible to build a perfectly generic solution that meets all your needs. It is not because by doing so you will lose your job and so won't do it but because by building everything so generic and loosely coupled you are introducing a few layers of latency and other issues which might or might not be acceptable to your users. In the end you will have to build something that the users will sign off and this means compromising on how much generic you can get.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://www.codeproject.com/Lounge.aspx?msg=4295177" target="_blank">Code Reuse</a> (codeproject.com)</li>
</ul>
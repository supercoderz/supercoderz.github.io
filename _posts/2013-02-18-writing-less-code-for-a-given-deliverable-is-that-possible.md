---
layout: post
title: "Writing less code for a given deliverable - is that possible?"
date: 2013-02-18 22:44
comments: false
categories:
---

I have been writing code on the job for over 8 years now, and have written many thousands of <a  title="Source lines of code" href="http://en.wikipedia.org/wiki/Source_lines_of_code" target="_blank" rel="wikipedia">lines of code</a> in Java - partly because Java is verbose, and partly because of the size of the problem being solved and the size of my deliverables. I have also tried my hand at <a  title="Python (programming language)" href="http://www.python.org/" target="_blank" rel="homepage">Python</a> where I can get more done in fewer lines of code, and Python frameworks like <a  title="Django (Web framework)" href="http://www.djangoproject.com" target="_blank" rel="homepage">Django</a> where I can get a basic website up in fewer lines of code than anywhere else.

I have also built reusable code components that I have used to piece together a complete solution with just a few tweaks to the configuration or code. While this is in a way writing less code for new deliverables, I would still categorize it as reusability than less code.

What  I am talking about is different - can you have a framework, that can be used to create a very minimal set of instructions of the key functionality that is needed and then the framework goes and builds the solution? Meta programming so to say, and similar to what Django does to build websites very quickly.

This is very much possible, and is done in many cases, however the issue will be if we try to apply this to every solution and every business scenario. This post tries to understand where we can and where we cannot expect to see this kind of thing and why.


<h2>How do we decide how much code we write for any given problem solution?</h2>
The most obvious answer is that it depends on the requirements - we write code for all the business requirements and then we write code for the tests. Simple. But if we dig a bit here and think more about how we decide what the requirements are then we will find that the requirements are determined by -
<ol>
	<li><span style="line-height:13px;">The problem that we are trying to solve</span></li>
	<li>The scope of the various aspects of the problem that we are trying to solve</li>
	<li>The time on hand</li>
	<li>Whether it is a long term solution or a short term one until something else is ready</li>
	<li>How badly the users want this</li>
	<li>How many people we can throw at the work</li>
	<li>Nice to have features vs absolutely necessary ones</li>
	<li>Budget</li>
</ol>
These are just a few cases and not in any particular order of importance. From all these, the main ones that impact how much of a good job we do with the requirements are #3 and #6 - if we have more time on hand and if we have more people then we can explore more requirements.

And the more requirements we have, the more the bigger picture becomes clear and we can find out cases where we can optimize, cases where we can reuse code, and cases where we can find out patterns that are similar to this problem and which might be useful to solve a similar problem in future.
<h2>An example - Spring framework</h2>
The <a  title="Spring Framework" href="http://www.springsource.org" target="_blank" rel="homepage">Spring Framework</a> is a J2EE framework what provides a lots of <a  title="Application programming interface" href="http://en.wikipedia.org/wiki/Application_programming_interface" target="_blank" rel="wikipedia">API's</a> and templates that can be used to quickly build J2EE applications without having to build boiler plate code. It can be used to build command line applications, server side processes, GUI applications, web apps and a lot more. The beauty of all this is that you are not forced to use the entire framework and can pick and choose which parts you actually want to use. This makes it very light weight. The most heavily used part of this framework is  probably the <a  title="Dependency injection" href="http://en.wikipedia.org/wiki/Dependency_injection" target="_blank" rel="wikipedia">dependency injection</a> part of it - where you define your application in terms of <a  title="JavaBean" href="http://en.wikipedia.org/wiki/JavaBean" target="_blank" rel="wikipedia">Java beans</a> (<a  title="Plain Old Java Object" href="http://en.wikipedia.org/wiki/Plain_Old_Java_Object" target="_blank" rel="wikipedia">POJO</a>'s) and wire them together in the <a  title="XML" href="http://en.wikipedia.org/wiki/XML" target="_blank" rel="wikipedia">XML</a> configuration.

The beauty of dependency injection is that it frees any given class from having to instantiate all the dependent class instances that it needs. For example if a class needed a list of connections, in the past you would probably write a loop to create all the connections - however in this case you will just define a list variable, and then through Spring XML assign it a set of connections. These connections themselves could have been created using one of the various Spring components and XML. No code at all.

If we take a step back and think about it - we all had at some point or other written code that was wiring together our application by creating and setting various class instances, by making connections, by setting properties etc. And we all wrote this same code again and again, maybe some of us wrote some reusable code, but no one ever came up with something like Spring which solved the problem for everyone in a very simple and idiomatic fashion. And I believe not many people these days ever think about wiring Java applications by hand - much like how we don't think about writing code in assembly or using punch cards.
<h2>So how did they create such magic?</h2>
The team that build Spring framework was talented, no doubt, but they did not build such an easy to use and successful dependency injection framework because they were talented - not for that reason alone at least. They did that because they spend time to sit back and think about what was the key issue that will help them to easily plug in the various components of the Spring framework without having to force the users to use the whole framework. And this would have boiled down to two things -
<ol>
	<li><span style="line-height:13px;"><a  title="Software build" href="http://en.wikipedia.org/wiki/Software_build" target="_blank" rel="wikipedia">Build</a> each component of the framework as a independent module with a clearly defined interface and a simple set of attributes.</span></li>
	<li>Find a way to easily put these all together along with the code that will be written by the users.</li>
</ol>
While #1 is what we all do for out day jobs, cracking #2 was what nailed it. And they did it because they spent time looking for it - they were not in a hurry to finish the framework, but they were looking to make it reusable and easy to use.
<h2>So how do we build our business deliverables again?</h2>
I would bet that most of us are more focussed on getting the problem solved efficiently rather than on how we can make our solution easy to use in future. I am always faced with code that has been written and then updated by at least 10 developers over a period of time. The coding style is similar but different. The solution would have been built for one use case and with the goal of making it do one simple thing again and again fast enough. And I will be tasked with building the same for a different use case. The eternal questions are -
<ol>
	<li><span style="line-height:13px;">Do I refactor this code to make all the main code agnostic to what problem we are solving and pull out use case specific code - so that I can then write my specific logic in a few lines.</span></li>
	<li>Or, do I just copy the code, make my changes and then make it run.</li>
</ol>
The decision to this is usually made while keeping in mind two things -
<ol>
	<li><span style="line-height:13px;">How quickly I need to deliver, and whether I have time to regression test all the old code if and when I refactor this - automated tests can make this easy but not all applications, especially server side process that depend on a complex SOA pattern can be proven this way</span></li>
	<li>And more importantly. whether I see a case where the existing code base will need a fix very specific to that use case and which will not be easily done if I move to the refactored and repeatable approach.</li>
</ol>
This is the most common thing that we all do - and if we can spend some time to identify common patterns in our code, then we can build things that can save us from writing more code next time.
<h2>Patterns - they are the key</h2>
Whenever we do something there is a pattern in there - if we look at the solution and can extract out the pattern then we can build that into something that can save us effort next time. This is different from reusable code - reusable code might or might not be a pattern - it can be a method call that I use in many places.

We all know design patterns, Spring is an implementation of the dependency injection pattern ( am not sure if that is pattern, but am cure Composition is a pattern and dependency injection helps achieve Composition). If we can identify a pattern in the application - Application Pattterns so to say - then we can easily build something that implements the pattern - takes an input, applies the pattern and then generates a full blown solution.
<h2>Another example - Django</h2>
If you ever tried to build a web application in Java, using MVC for example, you know that you need to write the data model, define a view using JSP servlets controllers etc, and then build the controller. Maybe you can use Spring as the MVC provider and save some work.

But whatever you do, you will have to convert the data model that you created into Java classes and do all the management. Sure you can use Hibernate or other ORM, but it involves work like writing XML or annotated beans etc.

In Django, you just write a class with a set of properties, a simple list, nothing more, and it handles all the save and delete operations etc, it handles how the data is mapped to tables, and even syncs the table definitions for you. It even provides you a very rich Admin interface where you can see, create, update and delete your entities without even writing a line of code for the view or any HTML.

So what is the magic here? The magic is simple - the designers of Django figured out that fro the point that you define a data model on a piece of paper, to the point where you create database tables and provide a simple CRUD view for that, it is the same for all applications, everyone does it the same way more or less and there can be a simple effective solution built for this so that no one else has to ever do this again.

They used Python, so there was support for runtime code generation using meta classes and life was easier than Java, however there was still work to do and they did a pretty good job of it. And now everyone enjoys the benefits.
<h2>So what can you do if you want to write less code?</h2>
Next time you design an application or get a chance to redesign existing one, look at the application as a whole and try to find a pattern - like data flows, is data generally taken processed and split out, are there rules for data handling that every component is doing and some are doing better than others etc. Once you have this, you need to see if you can separate out the application logic - that is the common pattern code - from the code specific to your use case and then weave it together into a coherent solution.

If you can, go ahead and do it. When you pick up the next part of the application, don't look at how it was done in the past, but see how you can do it in the new approach by weaving together the use case code with the common code, maybe enhancing the common code on the way.

If you do this for a few components then the whole application will slowly fall into place and you will find that for most of the application you wrote very less code - most of the code was written for the first component.
<h2>Conclusion</h2>
So as you can see, if you are able to identify the common pattern in the various components of your application, you can build a framework within your application so that each time you need to add a new component or a new feature, you just need to add the code that is required to create inputs for the framework or handle the output of the framework  - rest of it is handled by the code that you already wrote.

You are writing less and less code for each of your deliverables!
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://chrismdp.com/2013/01/dependency-injection-not-ioc/" target="_blank">Dependency injection != Inversion of Control</a> (chrismdp.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://premaseem.wordpress.com/2013/02/09/spring-design-patterns-used-in-java-spring-framework/" target="_blank">Spring : Design Patterns Used in Java Spring Framework</a> (premaseem.wordpress.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://zef.me/5390/decoupling-events-vs-dependency-injection" target="_blank">Decoupling: Events vs. Dependency Injection</a> (zef.me)</li>
	<li class="zemanta-article-ul-li"><a href="http://agile.dzone.com/articles/you-can%E2%80%99t-refactor-your-way" target="_blank">You Can't Refactor Your Way Out of Every Problem</a> (agile.dzone.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://architects.dzone.com/articles/apache-hadoop-decreasing" target="_blank">Apache Hadoop: Decreasing Technical Debt through Refactoring</a> (architects.dzone.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.brandonsavage.net/when-to-write-bad-code/" target="_blank">When To Write Bad Code</a> (brandonsavage.net)</li>
</ul>
---
layout: post
title: "Do frameworks really speed up development and make us more productive?"
date: 2012-01-09 19:08
comments: false
categories:
---

Any developer will know that it is not possible to code from scratch and re-invent the wheel each time we want to solve a problem.Since most problems fall into generic categories with some custom logic, standard libraries were developed which shipped with the programming languages and helped ease things. The Java standard library, MFC for C++ etc are some examples. While some languages have required us to install the library separately, languages like Python have followed the batteries included approach where we get everything in the standard install.

But having a library does not solve all problems - there is another level of abstraction that can be built on top of this - this is called framework. A framework is a set of components built using the standard library or other libraries that solves a particular class of problem. <a  title="Web application framework" href="http://en.wikipedia.org/wiki/Web_application_framework" rel="wikipedia">Web frameworks</a> make it easier to build a website - for example <a  title="Ruby on Rails" href="http://rubyonrails.org/" rel="homepage">Ruby on Rails</a> which says you can build an entire website with very few lines of code, or Django which does something similar and provides a very good admin application. If we move away from websites into more back end work then we have frameworks like Spring and Hibernate that solve issues like ORM and dependency injection.

All these are supposed to make our jobs easier, speed up development, make us more productive and in general cause less bugs because they are proven libraries. But do they really speed us up and cause less bugs? If not then why? What are the things that we should consider to get best results?


<h2>What problems do frameworks solve?</h2>
Frameworks usually solve the high level problems like
<ul>
	<li>Storing objects in database , for example Hibernate</li>
	<li>Rendering forms views and <a  title="Create, read, update and delete" href="http://en.wikipedia.org/wiki/Create%2C_read%2C_update_and_delete" rel="wikipedia">CRUD</a> pages on websites, for example Django, Rails</li>
	<li>Providing an abstraction to a common messaging layer, for example Spring JMS template</li>
	<li>Wiring together an application by bringing together all dependencies, for example Spring, <a  title="Google Guice" href="http://code.google.com/p/google-guice/" rel="homepage">Google Guice</a> etc</li>
	<li>Make it easier to create a web UI, for example GWT, <a  title="Apache Wicket" href="http://wicket.apache.org" rel="homepage">Apache Wicket</a> etc</li>
</ul>
All these solve a high level generic problems - we can use these for the common parts and then build our own business logic on top to complete the application. There is no single framework that can solve all problems and give us a solution that suits our business requirements. That is impossible. Even enterprise packaged solutions like <a  title="Oracle Applications" href="http://en.wikipedia.org/wiki/Oracle_Applications" rel="wikipedia">Oracle Apps</a> or <a  title="NYSE: SAP" href="http://www.google.com/finance?q=NYSE:SAP" rel="googlefinance">SAP</a> do not provide everything out of the box - you have to install them and customize them before you can get anything done.

And this very reason that the frameworks do not solve every problem leads to issues and lack of expected speed in our development process.
<h2>Knowing the problem to solve and how</h2>
Although frameworks provide us with the tools to solve a problem, we should know what to solve and how. If we are trying to build a website, then we should know what are the things that we should have on the website - things like user management, <a  title="RSS" href="http://en.wikipedia.org/wiki/RSS" rel="wikipedia">RSS feeds</a> etc that we should have. We might know these from our requirements, experience or by looking at websites around us.

Knowing the problem to solve and the approach to take will help us identify the framework to be used and the features that it should have. This will help in the longer run by making it easy to implement the requirements.
<h2>Choosing the right framework</h2>
There are often multiple frameworks that solve the same problem. Which one do you choose? This is not always an easy question. Sometimes you decide on a framework which is available for the programming language or the technology that you prefer to use. Or you choose a programming language which has a healthy set of frameworks that suit your requirements.

When choosing a framework some of the things that you need to consider are
<ul>
	<li>How mature it is, how stable it is</li>
	<li>The number of developers using it, the community of users and how much help we can get from them</li>
	<li>Familiarity of your developers with the language, technology or <a  title="Problem domain" href="http://en.wikipedia.org/wiki/Problem_domain" rel="wikipedia">problem domain</a> that is solved by the framework</li>
	<li>Does it have the features that you require to implement your requirements?</li>
</ul>
<div>You can try to do a proof of concept with some of the frameworks and the decide on which one to use based on the success with that. Also this exercise will give you good exposure to learn and ramp up on the framework.</div>
<h2>The tool is only as good as the person using it</h2>
We all have seen those home shopping ads on <a  title="Television" href="http://en.wikipedia.org/wiki/Television" rel="wikipedia">TV</a> where the host shows the best food processor in the world or the latest and sharpest knife that can cut through rubber, wood and still not lose the sharpness of the blade required to thinly slice tomatoes. These do work and they look really great on TV. But if we ever tried to buy one and use it on our own, we will notice that the results aren't quite the same. Sure its the same knife and in the same packaging but it is a different person using it.

The chef on TV was able to get the best results because he was used to the tool - he was comfortable with the tool, knew how to use it and the most effective way to use it - and since he knew all these he was able to get the results. You on the other hand would not have the same kind of comfort level and so the results are not good. With enough practice you can get the same results.

Its the same with all the frameworks - there is a certain way to use it to get the best results and once you know that you can get the desired results just like they show it. Every framework comes with a set of examples and documentation that shows how to use it. You need to ensure that you understand exactly how to use it and do it the right way.
<h2>Get under the skin of the framework and understand how it works</h2>
The examples and documentation of every framework only show certain ways of using it - there will be things that can be done with it that are not yet explored or ways to customize the usage to your scenario. Since every business case and every technical requirement is unique, it is possible that you can tweak the usage of a certain framework for your application.

Also, the more you understand a framework, the easier it is for you to fix it. On a bad day when you have a serious issue that needs to be fixed, often it is this knowledge of inner workings that will help you to solve the issue quickly.
<h2>Working around limitations or using a different approach</h2>
Not everything is perfect and not all problems are solved by the framework. For every problem that we try to solve using a framework we will run into a roadblock that prevents us from getting the job done. This could be because of a limitation in the framework, or because your requirements or architecture was not built with the framework in mind.

If  is an issue with your design then it is easy to fix that - you can go back to the drawing board and rethink the problem. You might be able to come up with a solution that works. If it is problem or limitation in the framework itself then it is a different issue - you might be able to get a fix for that or might have to look for alternatives.
<h2>Conclusion</h2>
Whether we are successful with a framework or not depends on how much we understand it and how we use it. If we know the framework correctly and know the problems that we want to solve, then we can definitely achieve the results that we want.
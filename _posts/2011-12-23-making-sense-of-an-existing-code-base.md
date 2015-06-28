---
layout: post
title: "Making sense of an existing code base"
date: 2011-12-23 13:22
comments: false
categories:
---

[caption id="" align="alignright" width="300" caption="Image via Wikipedia"]<a href="http://commons.wikipedia.org/wiki/File:Calling_super_in_ruby.jpg"><img  title="English: A graphical description of the limita..." src="http://upload.wikimedia.org/wikipedia/commons/thumb/2/2e/Calling_super_in_ruby.jpg/300px-Calling_super_in_ruby.jpg" alt="English: A graphical description of the limita..." width="300" height="378" /></a>[/caption]

At least once in our developer lives we would have come across a situation where we need to look at an existing <a  title="Codebase" href="http://en.wikipedia.org/wiki/Codebase" rel="wikipedia">code base</a>, make sense of if, understand exactly what it is doing and fix something. If you are one of the many active contributors to the various open source projects there then you might have done this again and again. You look at a project, find an issue, make a fix, send a patch get it reviewed and get brownie points when they accept it. If you are a <a  title="Software developer" href="http://en.wikipedia.org/wiki/Software_developer" rel="wikipedia">software developer</a> in a large company then you probably got put in that place when a developer quit or someone had to revive the code of a legacy application.

The situation is unavoidable, and presents us all with different things to react to - oh my god they hard coded this! oh my god i cannot believe this is software engineering! this thing needs a complete rewrite and is beyond saving! Oh my god I cannot believe people don't know this <a  title="Programming language" href="http://en.wikipedia.org/wiki/Programming_language" rel="wikipedia">Programming</a> 101!.

We have the benefit of hindsight and the edge that we are taking a fresh perspective, but we have no right to belittle those efforts. Yes, sometimes it is impossible to even make sense of the code but its our job.

In this article we will go though a few points about how we can approach these situations.

<!--more-->
<h3>If it runs then it was built for a purpose and not by idiots</h3>
Whenever we are faced with the task of understanding a code base - Java, C++ or Excel Macro - the first and the most important question is - does the damn thing compile and run? And this question can answer a lot of questions.

If a code base compiles and provides a binary that we can run then it will do one or more of the following
<ul>
	<li>It will print some console or log messages</li>
	<li>It will perform some task on certain inputs and fail if any of them are missing</li>
	<li>If it is a <a  title="Graphical user interface" href="http://en.wikipedia.org/wiki/Graphical_user_interface" rel="wikipedia">GUI</a> then it will show something on the screen that we can look at and make sense</li>
	<li>It will let you interact with it in some manner and give feedback</li>
</ul>
Based on these, you can get a vague idea of what the purpose of the code was.If you are lucky and have an instance of that application running somewhere then you will get a clearer idea. This also tells you that whatever it is good or bad it serves a purpose. Based on this understanding, you can make a rough appropriation of what you would do to solve the problem.

Lets say it was an application to place orders like an e-commerce application, then you can dig into your knowledge of that domain to understand what it should be doing.
<h3>Knowing the domain</h3>
Before we even look at the code, we need to know the domain from which the purpose of this application is from. You know that the application runs and prints logs or shows you a GUI. Perfect! But what exactly is the problem and the domain?

You can go to the users of the application or to the application itself and try to understand more of the domain like
<ul>
	<li>What is the core flow of logic?</li>
	<li>What are the steps being performed?</li>
	<li>What are the kinds of inputs or outputs that you see?</li>
	<li>Do you see a file or database involved?</li>
	<li>Do you see a magic cloud or ether that brings in some data or functionality?</li>
	<li>Any interfaces to other systems?</li>
</ul>
Once you have this, then the first thing you need to do is get some more information on that problem so that you can solve the problem in your head and see how you would design it. This is important because you should know what to expect. For example if I told you that you have to build a e-commerce application then you would assume that there has to be some way of storing the orders etc created by this application and also that you need a large amount of static data that can be used to enable the users to place orders. If you have this idea in your head and I showed you some database interface code then it will make sense in your head why I need  database code. But if you did not have this fundamental understanding and I showed you database code then there is no stopping you from asking me why we need a database - which means you are not making much progress.
<h3>Why it is important to solve a problem in your head before looking at its code</h3>
If you were given a problem to solve, you would break it down to constituent parts and then attack each piece. This is what we all do in our daily lives without computers. And this knowledge of the break down is what we use to understand what a person is doing - imagine sitting in an observation room and watching a person do some tasks and trying to identify what he is doing. Unless you have some pre-knowledge of what that guy is doing you will not be able to identify it even if your life depended on it.

Similarly, if you know the problem that the code base solves, and have a break down of it in your head, then when you look at the code the inherent structures, objects and flow in the code will become apparent to you.

You can piece together the pieces in you head to make the full picture.
<h3>Know the technology or programming language</h3>
At the end of it all its just a few <a  title="Source lines of code" href="http://en.wikipedia.org/wiki/Source_lines_of_code" rel="wikipedia">lines of code</a>. If you are looking at some <a  title="Ruby (programming language)" href="http://www.ruby-lang.org/" rel="homepage">Ruby</a> code, then it will help to know that Ruby treats everything as objects, Ruby can have both procedural and <a  title="Object-oriented programming" href="http://en.wikipedia.org/wiki/Object-oriented_programming" rel="wikipedia">OOP</a> style of programming and some idea of how Ruby modules are organized. The constructs like loops and assignments are much easier to understand.

Once you know how a simple program looks in that technology and you know the start point of execution or how that code is executed - main() method or something equivalent - then you start looking at the code.

Another code thing to know is what is the development environment for that. Do we use Eclipse or such <a  title="Integrated development environment" href="http://en.wikipedia.org/wiki/Integrated_development_environment" rel="wikipedia">IDE</a>, or VI or any other IDE. How quickly we can make sense of a large code base depends a lot on what kind of tools we have at hand. If we had to carve a knife out of stone before we can hunt then we can be sure hunting is a long time away!
<h3>Finally, the code!</h3>
Code is just flow - so we start by looking at the cod in main() and see what it calls and what sequence it calls it. Nowadays with all the IDE's and tools that are available to developers, it is trivial to see the call hierarchy of any method or check the references to that class or method.

Once we know the sequence of calls being made, we can look at the entities that are in play, and applying our knowledge of how we would solve the problem, we can then map these entities to our understanding. Obviously there will be differences between the two and some cases we might not be aware of the particular problem being solved. All this cannot be done in one pass and needs at least 3 attempts to get our head around it - we might need to document the various relations between the entities.

We could use <a  title="Unified Modeling Language" href="http://en.wikipedia.org/wiki/Unified_Modeling_Language" rel="wikipedia">UML</a> or the different IDE plugins to generate a class diagram or a sequence diagram for the code. This will give use a picture to visualize and make it easier.
<h3>Context is the key we don't have</h3>
How we solve a problem depends on how we think we should solve it, and how we think we should solve it depends a lot on the context - people, constraints, resources - and these are something we just cannot replicate. Not unless we have a time machine.

Trying to understand a solution without knowing why it was decided to solve it that way is difficult. It is what I would call '<a  title="Software archaeology" href="http://en.wikipedia.org/wiki/Software_archaeology" rel="wikipedia">Software Archeology</a>' - you look at the ruins of a city and find a seat with a hole in it and think a lot about what it was - then you finally link it to your toilet seat and figure out that that room must have been a toilet and then probably you can dig underneath that and discover that the people who lived there had an extensive sewage system! You will not know about the sewage system if you cannot link it to the toilet seat!

They key thing in the absence of a context is to be able to make that link - you see something that looks odd or does not make sense, you sit down and make a list of all possible reasons why that could have been that way and match it with other design decisions that you see around in the code and then pick the most probable one. You could even see that component in action while the application is running and then things might just click!
<h3>Tweak it, break it, shake it</h3>
The best way to make sense of some things is to see them in action. You can run the application in debug mode, or add some code that will intentionally break it. By doing this you will know how the application behaves. And once you know this and understand the relationships between the entities, you can try to fix small issues, refactor the code and try to implement whatever change you want.
<h3>Looking at tests and comments</h3>
If you are lucky and the code was built according to the modern day practices like test driven development or sufficiently commented then your life will be easy. But not everyone writes tests, and there are companies that are proud to say we are anti comments.

But if you did have tests though, then that will help you a lot by clearly showing how each piece of code should work and how it will react to valid and invalid inputs. You can make any change and ensure that all tests pass.
<h3>Holding back your emotions when you see bad designs</h3>
We all are developers who are proud of our ability and consider ourselves the best thing after any great programmer and we live the fact that we build the best designs and best applications. So when you see an old code base and see a hard coded reference your first reaction would be to berate those guys for lack of foresight. Sure you have the benefit of hindsight but did you know what exactly the problem was back then?

In my career of 7 and a half years twice I had to look at old code which had Hibernate ORM and used custom SQL instead of using beans. In a recent review I was asked why I did not do anything to change that design and contribute to the long term greater good blah blah. My answer was simple - the application was initially built with proper ORM principles, but somewhere down the line few years after go-live there were changes made that needed to be put in quickly with less disruption. These would either require re-design of the model to suit the Hibernate best practices or a quick solution using custom SQL. In the end the decision was made on the cost benefit analysis of the two approaches. If we rewrite the application then we do it the correct way.

This is the kind of context that is needed when we see some bad code - and it will not help if we get flustered or judgmental.
<h3>Best way to make changes without breaking the damn design</h3>
The thing I said above about cost benefit, context and bad design - it is easy to get into that situation ourselves while making fixes. If you are making a change then it is for a particular business case. Each business case provides a measurable benefit. The cost of implementing that change in terms of time and resources should not be more than the benefit. Otherwise there is no value.

Although we might have 100 ideas on the best way to solve it, the solution that wins is not just the best one but also the one that provides the most value. If we choose that then we can make the best of both worlds.
<h3>Making it better</h3>
We might not be able to outright change everything but we can surely make incremental changes that help in the long run. Things like adding missing tests, comments and refactoring the code into re-usable components are all things that we can do to make it better. And yes this depends on what is the lifespan of the code and if we are anyways planning to throw that away and rebuild. Even then we can use these practices when we rebuild it.
<h3>Conclusion</h3>
Understanding and working on legacy code bases is not difficult if we use the right approach. We can learn a lot from the code that was built long ago and runs to this day and we might even be able to broaden our horizons about the domain and problems that we can solve.
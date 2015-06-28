---
layout: post
title: "Breaking down a problem as a team"
date: 2012-04-14 15:34
comments: false
categories:
---

[caption id="" align="alignright" width="147" caption="Flow of data in a typical parser (Photo credit: Wikipedia)"]<a href="http://en.wikipedia.org/wiki/File:Parser_Flow.gif" target="_blank"><img  title="Flow of data in a typical parser" src="http://upload.wikimedia.org/wikipedia/en/a/a9/Parser_Flow.gif" alt="Flow of data in a typical parser" width="147" height="409" /></a>[/caption]

If you walked past most programmers these days and asked what is it that they were coding for then the likely answers would be - business requirement, use case, user story, bug fix - maybe a few more, but very few of them will say that they are coding to solve a problem. You may ask if it really matters, i think it does impact how we think about the solution.

Another aspect that is pretty obvious is that not all think and understand in the same manner - so the reasoning that one programmer has got in his head for a problem might not be the same as what another one has got. How we reason out a problem and break it down depends on how we have been trained as kids - cool calm approach, panic, methodical, trial and error etc.

When we throw together a team, the success or failure of that team depends on how well the team can gel together - you can give them the best tools that money can buy, but unless they gel together they cannot get the work done. This is what is also referred to as the culture of the team or the company. This has to click for the delivery to succeed. The team has to work together to solve the problem the right way.

This post aims to understand the different aspects of how a problem can be broken down and how the team culture helps with getting it right.

<!--more-->
<h3>From problem to a program</h3>
When I first started programming, we were told that a program is a set of steps, an algorithm, which ultimately solves the problem. And this can be repeated again and again. Notice that this definition that we were given does not have <a  title="Object-oriented programming" href="http://en.wikipedia.org/wiki/Object-oriented_programming" rel="wikipedia" target="_blank">OOP</a> or any high level abstraction in it - it is just a few steps that solve a problem. We were also told that as the steps become longer, or repetitive, you could create a <a  title="Subroutine" href="http://en.wikipedia.org/wiki/Subroutine" rel="wikipedia" target="_blank">sub routine</a> and call that in the main program. This was the old procedural world and I used <a  title="BASIC" href="http://en.wikipedia.org/wiki/BASIC" rel="wikipedia" target="_blank">BASIC</a>, <a  title="Comparison of Pascal and C" href="http://en.wikipedia.org/wiki/Comparison_of_Pascal_and_C" rel="wikipedia" target="_blank">PASCAL and C</a> in those days.

What this did was to embed deeply in our heads to think of everything as a set of steps - you always have an input that goes through a set of steps, each of which could be one line or a sub routine and then finally ends up in a result. We did basic programs in these languages and then moved to hierarchical databases like DBASE and FOXPRO, and even there we applied pretty much the same principles. Additionally we learnt to accept the fact that data can be stored in tables instead of variables.

All this was in high school - so by the time it was turn to learn OOP - Java or C++ - in our heads, objects were just another way to represent data. It was structures with methods. Whether you call a object method as its behavior or a way to pass messages - it is just a sub routine which encapsulates some code.

What I am getting to here is that at a low level, it is all a set of steps. If you get the steps right, then the problem is solved.A large application is a collection of smaller programs each of which solve one part of the problem using simple steps or more smaller programs.  The funky business requirement that you are trying to solve is composed of a set of smaller problems that you need to solve. The cool GUI feature is a bunch of small problems. There are very few things in software that are not composed of problems.
<h3>Breaking down the problem - an example</h3>
If we agree that every requirement in computer science is a problem, then in order to solve it, it has to be broken down into the correct number of steps with sufficient granularity.

For example, lets us consider a string message that represents data related to a <a  title="Business" href="http://en.wikipedia.org/wiki/Business" rel="wikipedia" target="_blank">business entity</a> in your application. This is just a small part of the whole application. This message can be received by any means - socket, <a  title="Message queue" href="http://en.wikipedia.org/wiki/Message_queue" rel="wikipedia" target="_blank">message queue</a>, file etc. You need to convert this string message into a hash map that can be later used in the application. Lets look at the following breakdown of this problem
<ol>
	<li>Get the message from the input</li>
	<li><a  title="Parsing" href="http://en.wikipedia.org/wiki/Parsing" rel="wikipedia" target="_blank">Parse</a> the message into its constituent parts</li>
	<li>Identify each part and translate that to a field your application can later use</li>
	<li>Pass the set of fields and the values to the rest of the application</li>
</ol>
This is very high level. Lets tackle each of these in turn. First, getting the input
<ol>
	<li>Connect to the socket or message queue or check existence of the file</li>
	<li>When ready, start reading the data</li>
	<li>Check data is received, and acknowledge the data by sending back a message or deleting the file</li>
</ol>
Next, parsing the message
<ol>
	<li>If you are receiving data from someone, there will be a spec for the data representation - check that and identify the <a  title="Data" href="http://en.wikipedia.org/wiki/Data" rel="wikipedia" target="_blank">data format</a> and the delimiter</li>
	<li>Break the string into a set of smaller strings based on the delimiter</li>
	<li>If these smaller strings are smaller messages then parse them as well - repeat this until you have a set of unit strings that do not need any further processing. Each of these should represent one piece of information</li>
	<li>Check if there are any header or footer parts and mark them as such</li>
</ol>
Identifying the parts of the message
<ol>
	<li>Loop through each of the message parts that were parsed
<ul>
	<li>For each part identify the format of the data - is it key value pair? Is it a field ID and value? Is it a encoded string?</li>
	<li>For each part extract the data</li>
	<li>If the data needs massaging with additional information from a data dictionary or something like that then apply that</li>
</ul>
</li>
	<li>Once all parts are processed, send them for further processing</li>
</ol>
Sending the data to the application
<ol>
	<li>If the application has a callback, then invoke that using this data</li>
	<li>If the application checks a database, then insert the data there</li>
	<li>If there is any other approach for data distribution, then insert the data using that</li>
</ol>
That was quite a few right? This definitely is more clean and clear than for example business statement - take the message from the source and make it available to the application. Also each step is very clear in terms of what needs to be done. There is less ambiguity and so less chance to make a mistake.
<h3><a  title="Unit testing" href="http://en.wikipedia.org/wiki/Unit_testing" rel="wikipedia" target="_blank">Unit Testing</a> is easy when you break down correctly</h3>
These days when test driven development and continuous builds are he recommended approaches, having the problem broken down so explicitly is very convenient to testing - you know exactly what to test for each part and once all the parts are tested, you can rest assured that the sum total of this will work correctly.

Also, when changes are made to each part - for example parsing the message - as long as it is properly isolated from the other parts, you have to worry very little about the impact of these changes on rest of the parts. This will help you concentrate more of your testing on the core change you are making and then you can quickly validate the full application.
<h3>Representing the problem at this level of granularity</h3>
Ever since computers and programming came into being there have been ways to represent problems at this level of granularity - they are called flow charts.

These days everyone wants to deal in high level abstractions like cloud and use tools like wiki, bug tracker etc to solve problems and not many bother to draw a flow chart. But if you do that, even for the 4 lines that you need to write, you will see that you get a great amount of clarity in your thought about the solution. You need not draw on paper, you can do that in your head but paper is a good reference to look at.

Again, coming to the reason why wiki and bug trackers are used - so that every one can collaborate - flow charts can be easily shared as VSD or JPG files or using any such diagrammatic representation on a shared location. Collaboration does not mean that everyone talks in high level terminology - properly breaking down a problem and communicating is also part of collaboration.
<h3>So, where does the team come in all this?</h3>
Like I said at the beginning of the post, not everyone can think in the same manner and each of us has our unique way of breaking down a problem. When you give instructions to a team to solve a problem, each team member has his own way to going about it. Even the team lead who explains the problem as his own approach.

In some cultures, it is common to expect a person to be able to do his job on his own without depending on others. In some it is acceptable that you hand hold someone to help to get the work done. This applies to work as well.

When a team comes together, not everyone can come up with the same clear cut breakdown. So it is important that everyone works together to come up with the breakdown. How clear you want to be and how much hand holding you want to do for breakdown depends on the skill levels and ability of each team member.

Sometimes might be better off with just doing a high level breakdown, sometimes you need to do a low level breakdown.
<h3>Proper breakdown helps in defect prevention and quick turnaround</h3>
When you know the proper steps, the chances that you will make mistakes in developing that is less. Also, since you know the solution clearly, it also helps your productivity. Once you have broken down a problem and documented that, any new team member who has to pick up and solve the issue can do it without much hassle. The problem that he has to ramp up remains, but the thing is made easy.

This also simplifies cross training and bringing everyone on the same page with minimum effort. Instead of some team members talking hi fi terms which no one else understands, if everyone talks on the same broken down level, then it makes it easier to communicate and get everyone on the same page.
<h3>It helps avoid FUD</h3>
FUD - Fear, Uncertainity, Doubt - this is the most common thing that is encountered when a team gets together to make a change in an existing system. Will it work? Will it break? Is this the good design? In most cases these doubts arise because we are not clear about the solution. If the problem was given and solved by a person without a proper break down, then there is very little understanding of the problem in the team. When a future change needs to be made to that, it is obvious that there will be doubts because the context is not clear to everyone.

However, if everyone spends time on breaking down the problem, and everyone is clear on the exact steps needed to solve the problem, then you can check whether the proposed change follows the same breakdown and confirm that things work correctly.

It also helps to quickly explains the changes to someone outside the team like the business.
<h3>Conclusion</h3>
Breaking down a problem correctly is important to getting the correct solution. Having the whole team participate in breakdown and be on the same page helps a lot in getting things done.
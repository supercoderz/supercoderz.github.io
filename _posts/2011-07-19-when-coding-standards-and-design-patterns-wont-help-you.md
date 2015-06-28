---
layout: post
title: "When coding standards and design patterns wont help you"
date: 2011-07-19 23:42
comments: false
categories:
---

We all have been through those phases in our lives as developers where we religiously followed coding standards and used the most appropriate <a  title="Design pattern (computer science)" href="http://en.wikipedia.org/wiki/Design_pattern_%28computer_science%29" rel="wikipedia">design patterns</a>. Before we got to that stage we would have been in a stage where we did not care what these meant or we did not even know these, and sometimes we will be in a situation where we cannot use them for some reason no matter how much we want to. In all these cases the job got done and we got paid. So, do we really need these? Do they really matter? Or are they just instruments of torture which more experienced and opinionated developers use on juniors? Is it possible at all to write a piece of code that is not coded as per standards, has no design patterns and absolutely no documentation and still works? Works without failing?

The answer is YES - read on to find out more about situations where ditching the best practices might be the only way to get there.
<h2><!--more-->Keeping it short and simple (KISS) or Keep it generic?</h2>
In one of my earlier posts I wrote that when the developer cannot get his head around the abstraction of a problem in terms of a highly generic and configurable design, he builds the simplest possible solution with lots of hard coding which means that the solution cannot be scaled in the future. I made it sound that it was  a bad practice.I still maintain that it is a bad practice. However, I will say that there are situations, and it needs special judgement to identify these situations, where not being generic is the way to go.

Think about it this way - when you write a piece of code, what is the probability that you will need to run the same piece of code for a different set of inputs? Or for example what is the probability that the code that you copy pasted and slightly altered in three places will have to be used in 10 other places and hence merits being put into its own method? In most cases you will find more scenarios where that code will be used or scenarios where you will run that code for different inputs. And in some cases you will write a few <a  title="Source lines of code" href="http://en.wikipedia.org/wiki/Source_lines_of_code" rel="wikipedia">lines of code</a> that will not get changed in the whole lifetime of the application. In the former case, it makes sense to write generic code and in latter case it makes more sense to just write the damn code and move on to more important things.

And it takes an intelligent decision to identify these scenarios.
<h2>The overheads of generic code</h2>
When we write generic code to satisfy more than one scenario, we almost always end up with
<ul>
	<li>Lots of methods and a long stack of methods calls sometimes very deep</li>
	<li>Parameters, hence local variables and fields and lost references to variables or unwanted references to variables beyond their scope</li>
	<li>Variables mean state information and state information can get corrupted by multiple threads accessing the same state or when we do not update state properly</li>
	<li>Configuration - XML, <a  title="YAML" href="http://en.wikipedia.org/wiki/YAML" rel="wikipedia">YAML</a>, Property files and all the hell</li>
	<li>Not being able to change something without breaking a lot of other things or requiring lots of regression</li>
</ul>
<div>These are just some problems and I don't mean that they should always occur. They can be avoided, but to avoid them requires effort and experience and the knowledge to be able to identify these as issues.</div>
<div>Lets take the example of deep chains of method calls - each method call is a <a  title="Context switch" href="http://en.wikipedia.org/wiki/Context_switch" rel="wikipedia">context switch</a>, and that takes up memory and <a  title="CPU time" href="http://en.wikipedia.org/wiki/CPU_time" rel="wikipedia">CPU time</a>. These can be optimized to speed up things, no doubt, but only up to a few milliseconds. What if your application needs to be faster? There are many applications in banking and other fields that require high speed processing whee microseconds count, even nano seconds. In such cases, a method call and a context switch are time consuming. They should be avoided - it will be much better to inline the code so that it executes faster.  The same logic applies to trying to manage state as class variables and then trying to lock to avoid synchronization issues.</div>
<div>Having to manage XML and any kind of configuration is another thing that we can avoid in applications that wont change much once they are deployed. You can always add 2+2 much faster if you don't have to read the 2 value from an XML file first.</div>
<div><span class="Apple-style-span" style="font-size:20px;font-weight:bold;">Then what about the cost involved in rebuilding the same thing again just because it was hard coded?</span></div>
<div>This is a valid concern - no one wants to put in 6 months development because that last time you coded the same thing somewhere else you hard coded a few key parameters. You would rather want to redeploy the same thing again quickly.</div>
<div>This is where the ability to make a judgement call on what to hard code and what to configure comes into play. You cannot just flip a coin and decide. The developer or the architect needs to make a knowledgeble and informed decision, quickly, about which route to take.</div>
<div>Its not possible to always imagine all scenarios and design a system - you will do your best to cover everything. If two years down the line you find out that you will deploy your application to new regions and will need to be generic - fine, go back to the drawing board and build it again properly configurable and all. This is a new business case that you did not see two years ago.</div>
<div><span class="Apple-style-span" style="font-size:20px;font-weight:bold;">Conclusion</span></div>
<div>I am not suggesting that you should or should not build hard coded applications - I am just saying that there are situations where you are better off hard coding to get speed rather than adding complexity in the name of generic solution. And it is with experience and better judgement that you take a call.</div>
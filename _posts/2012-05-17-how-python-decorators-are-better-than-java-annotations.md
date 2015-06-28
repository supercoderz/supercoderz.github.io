---
layout: post
title: "How Python decorators are better than Java annotations"
date: 2012-05-17 00:01
comments: false
categories:
---

This post is not about how to use <a  title="Python (programming language)" href="http://www.python.org/" target="_blank" rel="homepage">Python</a> decorators - there are many posts on the internet about this and this one in particular is one of my favorites which faithfully shows up in page one of my <a  title="Google Search" href="http://Google.com" target="_blank" rel="homepage">Google search</a> on this topic -

<a href="http://www.thumbtack.com/engineering/a-primer-on-python-decorators/">http://www.thumbtack.com/engineering/a-primer-on-python-decorators/</a>

Instead this post is about how much I , a primarily <a  title="Java (programming language)" href="http://www.oracle.com/technetwork/java/" target="_blank" rel="homepage">Java</a> guy, enjoy the decorators in Python.


<h2>Java does have decorators, they are called annotations</h2>
Java has annotations, but the problem is that they don't do much. You <a  title="Annotation" href="http://en.wikipedia.org/wiki/Annotation" target="_blank" rel="wikipedia">annotate</a> your methods or classes with a annotation that you defined, and pass it a few parameters if you want to. But that's about it - that by itself cannot do anything. You need to define the retention type of the annotation - whether it makes to the compiled <a  title="Bytecode" href="http://en.wikipedia.org/wiki/Bytecode" target="_blank" rel="wikipedia">byte code</a> or not - and then you need to write some code that can inspect your objects, check if there was an annotation and then do something with the annotated class or <a  title="Method (computer programming)" href="http://en.wikipedia.org/wiki/Method_%28computer_programming%29" target="_blank" rel="wikipedia">method</a>.

Is it good? Is it functional enough? I would say it is a so so solution. For example if I am writing a <a  title="JUnit" href="http://junit.sourceforge.net" target="_blank" rel="homepage">JUnit</a> test, then putting @Before on any method would make the test runner run that method before every test. This is better than always writing a setup method because in some cases you might want to quickly change the setup method - and this allows for you to do that without commenting some code.

Similarly, the @<a  title="Test cricket" href="http://en.wikipedia.org/wiki/Test_cricket" target="_blank" rel="wikipedia">Test</a> annotation easily lets you specify the timeout for the test or if you are expecting an exception.
<h2>But doing anything complex is tough</h2>
Lets say that in Java,  I want to measure the time taken by a method to complete. I can,
<ul>
	<li>Call that method in a wrapper method and time it there</li>
	<li>Write an annotation and have an processor that checks for the annotation and times the method</li>
	<li>Use <a  title="AspectJ" href="http://www.eclipse.org/aspectj/" target="_blank" rel="homepage">AspectJ</a> or something like that which can intercept the code execution based on annotations - but this requires additional <a  title="Compile time" href="http://en.wikipedia.org/wiki/Compile_time" target="_blank" rel="wikipedia">compile time</a> or runtime weaving to instrument the code.</li>
</ul>
If for example, I wanted to annotate all methods that I want to be wrapped in a timing method, I cannot do that without an external processor - and this sucks.
<h2>It is much easier in Python</h2>
See for yourself, the following code is all that I need
<pre style="padding-left:60px;">from datetime import datetime</pre>
<pre style="padding-left:60px;">def time_this(f):
 print datetime.now()
 def call():
     f()
     print datetime.now()
 return call</pre>
<pre style="padding-left:60px;">@time_this
def add():
 print 1+1</pre>
<pre style="padding-left:60px;">add()</pre>
Just by defining a method, I was able to set that as a decorator on another method! No external processor required! This is the flexibility that I would love to have in Java.
<h2>Flexibility makes abstraction easier</h2>
I took a very trivial case here, but you can do interesting things if it were this easy to decorate and do additional processing. You could execute the decorated method, and put the results in a DB before returning it, send it to a different application on a messaging platform and what not.

The application that will use this decorator does not need to know anything except the decorator syntax.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://pythonconquerstheuniverse.wordpress.com/2012/04/29/python-decorators/" target="_blank">Python Decorators</a> (pythonconquerstheuniverse.wordpress.com)</li>
</ul>
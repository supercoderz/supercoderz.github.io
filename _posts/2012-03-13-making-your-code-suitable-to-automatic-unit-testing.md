---
layout: post
title: "Making your code suitable to automatic unit testing"
date: 2012-03-13 00:25
comments: false
categories:
---

If you tell me about unit tests and the importance of having those to build a good application, my emotions swing between two extremes - one where I fantasize about having the perfectly unit tested software, and two where I want to vent my frustration on those tests being broken - everything in between rarely crosses my mind.

<a href="http://www.flickr.com/photos/51035609331@N01/43393028" target="_blank"><img  title="Unit Test" src="http://farm1.static.flickr.com/32/43393028_960dd71a0b_m.jpg" alt="Unit Test" width="240" height="159" /></a>

The fantasy is best described by this article about <a  title="Google" href="http://google.com" rel="homepage" target="_blank">Google</a> fixing a Chrome security flaw in less than 24 hours - <a href="http://arstechnica.com/business/news/2012/03/after-the-pwnage-critical-google-chrome-hole-plugged-in-24-hours.ars">http://arstechnica.com/business/news/2012/03/after-the-pwnage-critical-google-chrome-hole-plugged-in-24-hours.ars</a>

The other extreme is best described by this Hitler meme video -

[https://www.youtube.com/watch?v=T-Qn_-F2x1c](https://www.youtube.com/watch?v=T-Qn_-F2x1c)

So, why do I get such extreme emotions? And is it so difficult to get unit tests? You can get there and have the best automatic unit testing experience - but first you must prepare your code for unit testing.


<h3>A few words about the Google fix in 24 hours</h3>
I am amazed, but I know it is because of discipline and is not magic.

This is all I will say. <a  title="Google Chrome" href="http://www.google.com/chrome" rel="homepage" target="_blank">Google Chrome</a> is definitely not  a trivial piece of software that does a simple task, and given that the issue fixed was a <a  title="Zero-day attack" href="http://en.wikipedia.org/wiki/Zero-day_attack" rel="wikipedia" target="_blank">zero day exploit</a> in the core of the application, the fix isn't trivial either. But they did it - folks at Google are pretty awesome and definitely way above my level.

But if you think about how they might have done this, and applying some basic knowledge that most of us have about software development and testing, we can say the following things about this feat -
<ul>
	<li>The code as modular and functionality was isolated correctly in the design. Whatever part of the code needed to be fixed was not tightly coupled to the whole application in a way that it is impossible to fix something and not break other parts.</li>
	<li>They knew exactly what parts were to be tested and what uses cases needed to be satisfied. They probably also knew what interface contracts needed to be preserved so as not to affect the rest of the thing.</li>
	<li>They had the resources - human or machine - to execute these tests, and anything else that was required to ensure that the fix was ready for shipping.</li>
	<li>They have, and this is pretty much confirmed and known to all, the best delivery mechanism to push the patch without a fuss and install it to your browser without you even noticing it.</li>
</ul>
The rest of this blog post will be devoted to diving deep into points 1 and 2 raised above.
<h3>Defining unit testing</h3>
<a  title="Unit testing" href="http://en.wikipedia.org/wiki/Unit_testing" rel="wikipedia" target="_blank">Unit testing</a> is the process of testing a single unit of code - this can be a line, a method, a class or an application depending on what your are building and what your building blocks are and what you are dealing with. I build messaging applications and <a  title="Service-oriented architecture" href="http://en.wikipedia.org/wiki/Service-oriented_architecture" rel="wikipedia" target="_blank">SOA</a> applications for  a living, so unit testing for me is testing my individual adaptor or service until I am satisfied with the functionality.  If ever i end up in case where more than one of us is building different parts of the same application then unit testing will become testing the single method or function or class that one of us is building.

Whichever level of abstraction I choose to look at, I will always be testing a single unit of code - one that takes some input and gives an output, and this output is same no matter how many times I give that input. Or if the state is manipulated by this input, then for every call with this input, I should get an output which is a logical next step from the previous iteration.

Very easy to do.
<h3>What is a unit of code?</h3>
There are different answers depending on who you ask - someone will say it is a file with code in that, someone will say it is a class, a method or a variable. Some even call an interface a unit or sometimes even a running process.

But since we are talking about automated unit tests, our unit of code is a method - a method that takes an input and returns a value as an output. It could return nothing as an output or even throw an error.
<h3>OK, so we know that we need to <a  title="Test method" href="http://en.wikipedia.org/wiki/Test_method" rel="wikipedia" target="_blank">test methods</a> - is it enough to just send a few inputs and check results?</h3>
Not really.

If you think from a procedural paradigm like C, each method performs some logic on the inputs to return a value. It might alter some global variables.

If you think from an object oriented paradigm, then methods are behaviors of the objects - they operate on the attributes of the object and alter its state. This method defines the behavior of the object.

So depending on whether you are testing a stand alone method or a method on an object - you are either testing the logic or the behavior of the object as well.

Since the standalone methods work on data structures or global variables, and since object methods work on the attributes of the object which could in turn be other objects, there will be a final state that needs to be achieved if the method executed successfully. Once this state is reached, the method will complete. If the method has different execution paths, then the final state is different. There will be as many outputs as there are combinations of inputs.

In case of error or exceptions, there can be one or many errors thrown depending on which input caused it or where in the logic the error occurred. And of course, there can be the validation errors when the input given to the method is invalid.

So as you can see there are a number of valid input output cases, errors and exceptions, invalid inputs and also cases where you need to test that the method handled invalid or incorrect inputs gracefully and reported back the issues.

Quite a handful of cases and all these need to be verified by confirming that the proper value was returned or the proper <a  title="Global variable" href="http://en.wikipedia.org/wiki/Global_variable" rel="wikipedia" target="_blank">global variable</a> or state was updated.
<h3>Writing code to effectively test and verify all paths of execution</h3>
I once met someone who had a rule that no method should be more than 15 lines - that was because he configured his <a  title="Integrated development environment" href="http://en.wikipedia.org/wiki/Integrated_development_environment" rel="wikipedia" target="_blank">IDE</a> to show 20 lines in the <a  title="Source code editor" href="http://en.wikipedia.org/wiki/Source_code_editor" rel="wikipedia" target="_blank">code editor</a> and that was effectively what he could concentrate on at once glance without having to scroll around. And he always had many small methods. It helped him focus and isolate code. If he ever felt that two methods needed to call the same third method and there was something wrong with it like <a  title="Duplicate code" href="http://en.wikipedia.org/wiki/Duplicate_code" rel="wikipedia" target="_blank">code duplication</a> etc, then he would redesign his methods. Of course he applied common sense where it was impossible to break into such small methods.

What I am getting at here is that it is very easy to test small methods. They take some inputs and provide only a small number of results. A method with a long if else ladder can provide tens of results and quickly become difficult to verify for all possible comments. Instead if that method just delegated to large number of small methods, then testing each of these small methods is equivalent to testing the whole if else ladder.

So if you want to effectively test your code, make sure it is broken down into small methods that do very specific tasks without much interdependence. If there are methods that are internal and do not really do much unless they are called from some other methods, then hide them as private methods.

Which beings us to the next question - which methods to test?
<h3>Which methods to test?</h3>
When you have a few standalone methods or methods on a class, there are only a subset of these that are invoked externally by external components or user interface - these are called public methods or the public interface of the application or module. These are the ones that can get invalid input or bad data etc and since these control the data going to the internal methods, these are the gatekeepers which determine if the internal methods get bad data or fail.

So it is very important that these are tested - in fact these are the methods that we should test.

Golden rule - always test your public interface.

Again, there are methods that do something and methods that don't really do much - like setter getter methods. You don't need to test those. Similarly, if you have too many small methods that do not do anything much on their own there is is not much to test - test the bigger method that calls all of these to give results.
<h3>OK, but when I am testing, how do I get a database connection or an MQ connection?</h3>
When you are running a test, then you are not really running the application, so all the resources or all external systems will not be available - this is a fact. So, you need to have either of two things
<ul>
	<li>The code that you are testing can and will work without all these</li>
	<li>Or you can mock these</li>
</ul>
It is easier said than done, but if you provide all necessary connections, properties etc externally, and if you use a dependency injection framework like Spring, then it is possible to set or inject the test versions of almost all of these required inputs.

Databases, MQ's etc though - you might not always have a test database or a test MQ that you can hook into, so you will have to provide mocks - entities that expose the same interface as the actual DB or MQ connection and return a value as if they worked or not depending on the test case.

But if you want to use any of these, then your design has to be such that no variables are initialized on declaration, values are set in constructors or passed in or using setter method, and it should be very easy to override these values for testing purposes.
<h3>Code evolves and so do tests</h3>
Code does not stay static  - there are change requests and enhancements that change the code significantly. With each of these the tests  and test results also change. Some tests become invalid. So writing tests or preparing code for tests is an ongoing process - each time you make a change to the code, you need to keep in mind the tests, the ease of testing and make the change so that it does not break any of the principles that we have discussed so far or make it any more harder to test.
<h3>Putting it all together and conclusion</h3>
Whatever I have described here is programming 101 or design 1o1 - but we don't always follow these simple things. We get drawn into deadlines and issues and all and take the quickest solution. Doing that means that we do not spend enough time thinking about testing. Later when we do find time and come back to add tests, we are trying to bolt on tests to something that was not built with testing in mind. We will have to change things and re-design to make things work. Most of us don't get to doing that - we just give up or have a fit.

If we take a proper approach from the beginning and always design the application with the intention to test it, then it is very easy to achieve good results and we can even do the feats that Google does.

&nbsp;
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://stackoverflow.com/questions/8359696/how-to-keep-my-unit-tests-dry-when-mocking-doesnt-work" target="_blank">How to keep my unit tests DRY when mocking doesn't work?</a> (stackoverflow.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://kimphanvn.wordpress.com/2012/02/27/test-driven-development-best-practices-and-benefits-of-using-unit-testing-on-real-life-projects/" target="_blank">Test Driven Development: Best Practices and Benefits of Using Unit Testing on Real Life Projects</a> (kimphanvn.wordpress.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.makinggoodsoftware.com/2012/01/27/the-evil-unit-test/" target="_blank">The evil unit test.</a> (makinggoodsoftware.com)</li>
</ul>
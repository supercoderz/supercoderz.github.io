---
layout: post
title: "Making sure your application can scale, with less effort"
date: 2012-06-07 00:03
comments: false
categories:
---

These days <a class="zem_slink" title="Application software" href="http://en.wikipedia.org/wiki/Application_software" rel="wikipedia" target="_blank">application</a> <a class="zem_slink" title="Scalability" href="http://en.wikipedia.org/wiki/Scalability" rel="wikipedia" target="_blank">scalability</a> is an implicit requirement in whatever you build - you might be doing a few hundred users on day 1 but as time goes by everyone is expecting your code to deliver for thousands of users, or maybe for few users with <a class="zem_slink" title="Near real-time" href="http://en.wikipedia.org/wiki/Near_real-time" rel="wikipedia" target="_blank">near real time</a> performance. Even a second of delay is not tolerated. This applies not only to the web applications like <a class="zem_slink" title="Twitter" href="http://twitter.com" rel="homepage" target="_blank">Twitter</a> or <a class="zem_slink" title="Facebook" href="http://facebook.com" rel="homepage" target="_blank">Facebook</a> but also to enterprise applications that are used for boring back office processing.

We have heard of stories where some guy or some company we know of has completely rewritten their application using lots of custom voodoo so that it can scale infinitely and handle every load thrown at it without breaking a sweat. Sure is is doable, but you always think that you will be more intelligent than the other guy and build your application correct the first time so that a) you get the brownie points and the bonus and b) you can save the time and effort saved to do bigger and better things.

But, it never really happens that way and you end up slogging to scale your application and fix that one little issue somewhere that brings down a part of not the entire application.

But there are people who manage to do this scalability voodoo without much hassle - how do they do it?

<!--more-->
<h2>Do the simplest possible thing, not!</h2>
I read somewhere in a presentation about writing <a class="zem_slink" title="Beautiful Code" href="http://oreilly.com/catalog/9780596510046/" rel="homepage" target="_blank">beautiful code</a> that one should write the simplest possible code without getting into elaborate architectures and designs. The author said that elaborate architectures, otherwise known as enterprise, can complicate your code or application.

I disagree - elaborate architecture is not called enterprise, and it does not have to complicate your design or application. Enterprise or enterprise architecture is something that is really important if you want to build applications that interact with one another in a standard manner and can using common means and patterns.

But anyways, that presentation and a lot many more on the internet proclaim that doing the simplest possible thing will ensure that your application will be able to easily scale when the need arises. This is not 100% true. Take for example, getting some data from a system to another system once in a day - the simplest way would be to put the data in a file which gets sent to the other application and it processes it. But this simple solution will fail if the data gets too big. We would then have to change the  approach to use  a database or a <a class="zem_slink" title="Message queue" href="http://en.wikipedia.org/wiki/Message_queue" rel="wikipedia" target="_blank">message queue</a>. Now, if we did the simplest thing and added some code to just read the file and process it, then we would have to spend some time to change the code so that it can insert to a database or a queue or read from a database or a queue instead of a file. However, if we had spent some time in designing the applications in such a way that the code that processes the data and the code that writes or reads the file is separated and built using some known design pattern, then we can easily swap those parts of the code - this will be made possible by the pattern.

So the solution that would make it easy to change the code with less effort is actually one where you spend some time to design the architecture of the application.

Now, if you are someone who says that doing the simplest thing means that you take care of all these <a class="zem_slink" title="Design pattern (computer science)" href="http://en.wikipedia.org/wiki/Design_pattern_%28computer_science%29" rel="wikipedia" target="_blank">design patterns</a> etc in the code and that you would also write unit tests and build continuous integration system for this code - great! you are with me and we are on the same page.
<h2>Handling concurrency correctly</h2>
When I was <a class="zem_slink" title="Learning Java" href="http://www.amazon.com/Learning-Java-Patrick-Niemeyer/dp/0596008732%3FSubscriptionId%3D0G81C5DAZ03ZR9WH9X82%26tag%3Dzemanta-20%26linkCode%3Dxm2%26camp%3D2025%26creative%3D165953%26creativeASIN%3D0596008732" rel="amazon" target="_blank">learning Java</a>, I was not sure how much I would use concurrency in applications later in my life - that was like 10 years ago or more. Ever since I got a job about 8 years ago, I have never written a single threaded application. That explains a lot.

When we build <a class="zem_slink" title="Thread (computer science)" href="http://en.wikipedia.org/wiki/Thread_%28computer_science%29" rel="wikipedia" target="_blank">multi-threaded</a> concurrent applications, there is always a shared state. Whether we use threads or we use different processes running on different cores or we use different applications co-operating using a middleware,  we need to ensure that there is a proper synchronized access to all these resources. We can use locks, concurrent data structures and a lot of means to achieve this synchronization.

When we have a 100 users working on the application, each process might have a delay x in getting or using a resource which is shared. When the number of users increases to thousands, the delay in getting a resource and the time spent using that also increases. We need to ensure that there are no delays in getting resources and that the resources do not get blocked for too long by any one process.

By sharing only the essential resources and making copies of everything else, we can ensure that there are very few delays in between processes.
<h2>Atomic code with less dependencies</h2>
Any application is a collection of atomic tasks that are performed on the input which results in a desired results. Sometimes we write applications which have long methods doing everything from pre-processing to the final publishing of results. If we had to scale such an application, then this long method would become a bottleneck because the processing of data and publishing of data is tightly coupled and cannot be separated. If the data processing and publishing logic was separate then we could split that into two threads that run independently and can process data at the fastest possible pace.

Decoupling your code is very important for scaling - by doing so you get the flexibility to take parts of the application an assemble them into independent threads or processes which will let each part concentrate on a certain kind of task. You can then deploy multiple instances of each part and increase the overall throughput of your application.
<h2>When something goes fast by simply not breaking it up into pieces</h2>
We assume that every task needs to be broken up and parallelized in order to make it go faster. However, there will be cases where this will simply do the opposite - make the application very very slow. Imagine  a task of you taking a large number of cards and stamping them with a particular color depending on the data on that. If you could so this all by your own, you could blindly check each card and if the data was what you were looking for then you would stamp it. However, you are asked to do this using 10 people. This means that you need to first split the cards among the ten people, tell them what to do and how and when they are done, you need to collate all the resources.

You will notice that the total time taken to do it this way will be more because you had to spend time to break up the task and collate the results. For certain problems, and certain large problems, the cost of breaking up the problem and collating the results will negate any benefits of splitting the task - in such cases you should avoid splitting the task.
<h2>Conclusion</h2>
These are just simple suggestions for making an application scale - what works for you will depend on the application and the architecture that you have, and also on what parameters you are trying to tune/optimize.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://www.slideshare.net/quipo/scalable-architectures-taming-the-twitter-firehose" target="_blank">Scalable Architectures - Taming the Twitter Firehose</a> (slideshare.net)</li>
	<li class="zemanta-article-ul-li"><a href="https://www.ibm.com/developerworks/mydeveloperworks/blogs/theTechTrek/entry/the_anatomy_of_latency5" target="_blank">The anatomy of latency</a> (ibm.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.javacodegeeks.com/2012/03/building-scalable-enterprise-systems.html" target="_blank">Building Scalable Enterprise Systems</a> (javacodegeeks.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.rackspace.com/cloud/blog/2009/11/18/writing-code-that-scales/" target="_blank">Writing Code that Scales</a> (rackspace.com)</li>
</ul>
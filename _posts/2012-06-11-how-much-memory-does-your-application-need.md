---
layout: post
title: "How much memory does your application need?"
date: 2012-06-11 23:55
comments: false
categories:
---

[caption id="" align="alignright" width="300"]<a href="http://en.wikipedia.org/wiki/File:Sample_sawtooth.jpg" target="_blank"><img  title="The &quot;sawtooth&quot; pattern of memory uti..." src="http://upload.wikimedia.org/wikipedia/en/thumb/4/4d/Sample_sawtooth.jpg/300px-Sample_sawtooth.jpg" alt="The &quot;sawtooth&quot; pattern of memory uti..." width="300" height="200" /></a> The "sawtooth" pattern of memory utilization: the sudden drop in used memory is a candidate symptom for a memory leak. (Photo credit: Wikipedia)[/caption]

I am a core <a  title="java development tools" href="http://download.cnet.com/windows/java-software/" rel="downloadcom" target="_blank">Java</a> developer, and have been doing that for about 8 years now, and when people come to me and ask me how much more memory my application will need or complain that Java applications eat up memory on the boxes - I just give them a dry smile, not wishing to comment or explain to them why my applications are no different from their applications. Even C++ or Python applications can and will use memory and that is what even they have <a  title="Memory leak" href="http://en.wikipedia.org/wiki/Memory_leak" rel="wikipedia" target="_blank">memory leaks</a>. Java is not the only memory hog out there - yes we demand our pound of flesh up front in the form of <a  title="Dynamic memory allocation" href="http://en.wikipedia.org/wiki/Dynamic_memory_allocation" rel="wikipedia" target="_blank">heap space</a>, but we live within that.

Anyways, when designing applications and deciding how many to run per box, especially Java applications, it is very important to know how much memory your application will need. Apart from the usual thing which is how much <a  title="Central processing unit" href="http://en.wikipedia.org/wiki/Central_processing_unit" rel="wikipedia" target="_blank">CPU</a> your application hopes to use. I have seen cases where on an 8 core server with about 48GB of <a  title="Random-access memory" href="http://en.wikipedia.org/wiki/Random-access_memory" rel="wikipedia" target="_blank">RAM</a>, we ran close to 40 Java processes each taking on an average of 500 <a  title="Megabyte" href="http://en.wikipedia.org/wiki/Megabyte" rel="wikipedia" target="_blank">MB</a> heap, some up to 1.5 GB heap and the box never went beyond 15% CPU usage. Of course someone complained once in a while and I gave a dry smile in response to their FUD.

This post is an attempt to understand how you measure the amount of memory needed by your application.

<!--more-->
<h2>A real life example of how things go wrong when you have too much in memory</h2>
I would like to give some example that I personally know, but since I cannot do that I will give you an example which I read almost once a month - the massive Foursqaure outage some time back, which did not affect me in any way since I don't use that site, but has good lessons for me. Read the below link, and come back to read on -

<a href="https://groups.google.com/group/mongodb-user/browse_thread/thread/528a94f287e9d77e">https://groups.google.com/group/mongodb-user/browse_thread/thread/528a94f287e9d77e</a>

What we can learn from this issue at <a  title="Foursquare" href="http://www.foursquare.com/" rel="homepage" target="_blank">Foursquare</a> is that when you have loaded too much of data into the memory and leave too little space for the application to maintain its runtime data, then the application will fail. This is true of the <a  title="Java Virtual Machine" href="http://en.wikipedia.org/wiki/Java_Virtual_Machine" rel="wikipedia" target="_blank">JVM heap</a> and also of the C++ or other <a  title="Machine code" href="http://en.wikipedia.org/wiki/Machine_code" rel="wikipedia" target="_blank">native applications</a> building caches in memory. In my Java world, I called it continuous full <a  title="Garbage collection (computer science)" href="http://en.wikipedia.org/wiki/Garbage_collection_%28computer_science%29" rel="wikipedia" target="_blank">GC</a> - where the garbage collector is trying to spend 90% of its time trying to free memory but is not able to free that much. Eventually it hits a limit and the thing crashes with a GC overhead exceeded or out of heap space.
<h2>So how much data do we need in memory?</h2>
The amount of data that we need to hold in memory depends on what we are trying to do with the data. If we are trying to write data at the fastest possible rate, then there is no point in keeping it in memory. We need to write it to disk or any storage so that it gets persisted and can be retrieved.

On the other hand if we are trying to read a lot of data, like with Foursquare above, and we need the reads to be fast and not take ages to complete, then it makes sense to keep the data in memory.

There is also another kind of application, my specialty, called messaging adapters or messaging applications whose job is to get the data from one end point to another at the fastest possible speed - we don't deal with writing or reading data - our aim is to quickly process and pipe data to other applications at the highest speed - and the less we have to deal with the faster the application is. So these applications take small chunks of data and feed them across the pipe - at any point there are  few hundred chunks being processed at once and this is fairly constant - so you always need enough to hold these 100 chunks and some processing code.

In fact once we ran a 500 messages per second processor in a JVM with 75 MB of heap space - it never failed.

So the first step is to determine which operation you need to optimize and how much data you need to keep in memory can be determined based on that operation. Cache services, databases and web servers maintain a lot of data in memory since they need to respond very fast to any inquiry.
<h2>OK, so I need a lot - how do I quantify it</h2>
Each application that needs to keep something in memory does so using a data type - primitive or custom. When you instantiate a type and assign it a value, it has a size - we need to know the approximate size of each object. The size of an object is the sum of all the sizes of the primitive or custom types that make up the object.

Once you have the size, you need to determine how many instances you will expect to keep in memory at once - this needs to be measured for the time that the application will be up - if that process needs to run for 8 hours a day and it can process or cache 100 requests per minute then the number of objects that you might expect to save assuming you need to save each object is 100*60*8=48000 objects. Assuming each is a byte in size, that will be 48KB of data - pretty small, but in real life each object is 1 KB or so in size, so the size you will need at least 46 MB for the data.

This is just a rough estimate, and you might find that in reality you need twice or thrice that data in memory and so you need more size.
<h2>Accommodating growth</h2>
When you build something you expect it to last a few years at least - so the application has to be able to handle the growth that the business expects. So when you design the application, you will need to sit with the experts and the users and figure out how much year on year growth they expect. If you say that you will need 100MB today and you expect a 20% year on year growth, then you should start with at least 120 MB and plan for increasing memory at acceptable intervals.

Also, you can see spikes in volumes, so you should keep a buffer of about 15-20% - so maybe start at 150 MB. These kind of decisions up front will save you lot of head aches in the long run.
<h2>Code also needs memory</h2>
All the code that you write and run will also need to be loaded into memory and will need space of its own. You would need to bring up the application an determine how much memory the code is occupying. Sometimes you might find that when the application starts, it will not load everything and as and when you use certain features more code gets loaded and it occupies more memory.

Also the application might clear some memory allocated to the code as it completes its processing - this will also impact the sizing of your memory.
<h2>Memory growth,releasing memory and garbage collections</h2>
I mentioned that the application can free up memory during its life cycle - the rate at which memory is freed up depends on the rate at which the application is processing data. Some applications are very efficient at releasing memory and some are not - that leads to memory leaks. The best practice is to ensure that you applications frees up any object as soon as it is done with it and does not have any future use.

In spite of this there will be long lived objects which are needed by the application to process and perform its functions. Sometimes these objects will get cached over the period of time and will increase in size - you might find that the memory used by the applications increases at a small rate because of this. Sometimes, this rate might jump.

Whatever memory you allocate or is available on the machine should be able to accommodate this growth.
<h2>Conclusion</h2>
It is not rocket science to determine how much memory your application will need - you just need to measure the data and code that you expect to use over the lifetime of the application.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://www.javacodegeeks.com/2012/05/outofmemoryerror-java-heap-space.html" target="_blank">OutOfMemoryError: Java heap space - Analysis and resolution approach</a> (javacodegeeks.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://stackoverflow.com/questions/10925169/appropriate-sizing-of-heap-and-old-gen-for-jvm-for-data-heavy-application" target="_blank">Appropriate sizing of heap and old gen for JVM for data heavy application</a> (stackoverflow.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.javacodegeeks.com/2012/04/java-heap-space-jrockit-and-ibm-vm.html" target="_blank">Java Heap Space - JRockit and IBM VM</a> (javacodegeeks.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.javacodegeeks.com/2012/03/jvm-how-to-analyze-thread-dump.html" target="_blank">JVM: How to analyze Thread Dump</a> (javacodegeeks.com)</li>
</ul>
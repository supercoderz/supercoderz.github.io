---
layout: post
title: "Thread safe usage of third party API"
date: 2012-04-22 22:35
comments: false
categories:
---

[caption id="" align="alignright" width="300" caption="A multithreaded process with two threads executing in time clearly showing that the threads execute separately and execute mutually exclusively in time. (Photo credit: Wikipedia)"]<a href="http://commons.wikipedia.org/wiki/File:Multithreaded_process.svg" target="_blank"><img class="zemanta-img-inserted zemanta-img-configured" title="A multithreaded process with two threads execu..." src="http://upload.wikimedia.org/wikipedia/commons/thumb/a/a5/Multithreaded_process.svg/300px-Multithreaded_process.svg.png" alt="A multithreaded process with two threads execu..." width="300" height="283" /></a>[/caption]

<a class="zem_slink" title="Thread (computer science)" href="http://en.wikipedia.org/wiki/Thread_%28computer_science%29" rel="wikipedia" target="_blank">Multithreaded</a>  programming is is not rocket science but is still difficult to get right. Anyone who has done a moderately complex solution with more than two threads knows how things can quickly get out of hand. In fact it is possible to get things in a mess even with two threads.

If you are building your own library and have to deal with threads, then you can use the various mechanisms in most modern <a class="zem_slink" title="Programming language" href="http://en.wikipedia.org/wiki/Programming_language" rel="wikipedia" target="_blank">programming languages</a> and get the correct solution. With enough testing and enough experience, you  an consistently get the right solution.

However, if you are using a third party library which might or might not be thread safe - then you are in trouble.

This post is an attempt to understand some such scenarios.

<!--more-->
<h3>Is this really a problem?</h3>
If you think about it, most modern software is built by reasonably well minded and capable developers, they probably built everything correct. But then, most software these days is way more complicated than it used to be and with one person working on a small part of the whole, things can be missed. Also, when building something, you test it for certain benchmark loads, scales and usage scenarios. In real life, things are more often than not pushed to the limits, and when limits are breached then things break.

I have a friend in performance engineering and he often points out a simple truth - things that work at 10000 simultaneous requests often die for no good reason at 20000 requests. At those loads, each operation gets about a fraction of a millisecond to complete and if it does not go well and gets into locks, then the whole damn thing will just die.

So, even with modern software built by the best brains using the best practices, <a class="zem_slink" title="Thread safety" href="http://en.wikipedia.org/wiki/Thread_safety" rel="wikipedia" target="_blank">thread safety</a> can be missed.
<h3>What can happen if the third party software is not thread safe?</h3>
In the simplest case, the <a class="zem_slink" title="Shared resource" href="http://en.wikipedia.org/wiki/Shared_resource" rel="wikipedia" target="_blank">shared resource</a> is locked and you will get an exception - like a table or table row being locked, file being already open or socket being busy, or running out of connections. All of these almost always result in an exception and will fail.

It is easy if the above exception propagates to the main application - however, if the exception is ignored or results in a retry loop, then the main application is not sure what is going on and may timeout.

In other cases, data might get corrupted.In a recent case that I encountered, we were getting intermittent data glitches that lasted a few milliseconds and got corrected. Over a day, this happened a few hundred times and was never a big impact. However, once in a while the incorrect data would get stuck and would not correct itself. It turned out that the data contained that we received from the source was not thread safe and this issue would manifest when we did over a 1000 messages a second. Since the data latency was not an issue, we could get away with adding more locking around the whole thing and also throttling the rate at which we processed it.

Sometimes though locked threads an cause an <a class="zem_slink" title="Crash (computing)" href="http://en.wikipedia.org/wiki/Crash_%28computing%29" rel="wikipedia" target="_blank">application crash</a> and then you will have to dig through the core files to figure out what happened and why.
<h3>How do we determine if the API that we are using is thread safe?</h3>
The answer is that there is no way to do so. Most <a class="zem_slink" title="Application programming interface" href="http://en.wikipedia.org/wiki/Application_programming_interface" rel="wikipedia" target="_blank">API documentation</a> specifies if there is some usage that is not thread safe, but this is not always the case. A good rule of thumb is to check if the call involves any kind of disk or other resource usage or if it mutates the state of the application. These kind of calls are the ones that most likely require thread safety.

Another quick check is to see the load or scale of the usage - if there is something that is going to get called at say 1000 times per second then that requires locking. Also, something that can be simultaneously called from different parts of the application or different users requires synchronization.

Shared resources ALWAYS require locking and thread safety.

If the API that you are using has any one of these characteristics then it requires thread safety.
<h3>Your own threads messing up with the API threading</h3>
This is also possible. Sometimes the API that you are using has internal threads to perform a variety of tasks. If you put a lock on some resource that is also being used by the internal threads, then it can mess up things too. There is no clean way of knowing this except maybe to look at the documentation or to profile the application.
<h3>How can we find out about all the threads in the API or the application?</h3>
These days most languages come with some sort of profiling mechanism that gives you an insight into the runtime state of the application. Java for example has a number of ways to monitor a running virtual machine. We regularly make use of JVisualVM which is part of the <a class="zem_slink" title="Java Development Kit" href="https://jdk6.dev.java.net/" rel="homepage" target="_blank">JDK</a> these days to profile the memory usage and number of threads that are loaded before we release any application to production.

Another more crude way to check is to check the  number of <a class="zem_slink" title="Operating system" href="http://en.wikipedia.org/wiki/Operating_system" rel="wikipedia" target="_blank">OS</a> threads being spawned when the application is being run on the server - we did this a few times when we did not have the ability to run the profiling tool on a server.
<h3>Catching threading issues during testing</h3>
It is possible to catch threading issues in the testing phase - provided that we spend enough time on it and we are able to replicate the real time loads in test cases. Doing <a class="zem_slink" title="Load testing" href="http://en.wikipedia.org/wiki/Load_testing" rel="wikipedia" target="_blank">load test</a> with controlled loads is not always a correct measure.

These days due to the quick turn around needed on releases and more emphasis on business cases, people try to get the functionality testing out of the way using automation and nightly builds and concentrate on getting things delivered on time in aggressive schedules. However, if the application needs to be around for a long time and perform under heavy loads, then sufficient time and attention needs to be given to testing for threading issues.
<h3>Paying attention from the design phase</h3>
It is very important that you know exactly what load you are <a class="zem_slink" title="Design" href="http://en.wikipedia.org/wiki/Design" rel="wikipedia" target="_blank">designing</a> for - because that determines whether you will have threading issues or not. If you are building an application that has to go as fast as the hardware will allow it to go and are willing to throw more cores at the problem, then you must reduce the amount of shared resources and shared state. You can gain speed by decoupling all parts of the application and if necessary duplicating data and state among the threads. Similarly,if there is a chance that there will be loots of reads or writes then you need to design in such way that all the reads and or writes are synchronized and do not interfere with one another. Since we cannot control how a third party API will use resources, we need to be very careful right from the design phase. Sometimes, the best way will be to consult the API vendor and use their best practices.

Another design consideration might be to completely ditch the multithreaded approach and go with a single thread design. This is sometimes the best solution because the cost of actually building the logic to ensure thread safety and the processing overhead might not be worth it and you might actually get things done faster by letting a single thread do the task as fast as it can and delegating everything to this thread.
<h3>Conclusion</h3>
It is possible to shoot yourself in the foot by assuming that the API that you are using is thread safe. It is as important to consider threading issues with third party API's as it is to do with your own API. The golden rule should be to build as much safety as you can on shared resources.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://supercoderz.in/2012/02/04/using-linkedblockingqueue-for-high-throughput-java-applications/" target="_blank">Using LinkedBlockingQueue for high throughput Java applications</a> (supercoderz.in)</li>
	<li class="zemanta-article-ul-li"><a href="http://blog.pluralsight.com/2012/04/13/video-making-java-threads-play-nice/" target="_blank">Video: Making Java Threads Play Nice</a> (pluralsight.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://stackoverflow.com/questions/9671515/java-threads-randomly-stopping" target="_blank">Java threads randomly stopping</a> (stackoverflow.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://stackoverflow.com/questions/9549418/how-to-safely-terminate-objects-running-on-different-threads-in-qt" target="_blank">How to safely terminate objects running on different threads in QT</a> (stackoverflow.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://stackoverflow.com/questions/9147082/streamreader-threadsafe-issue-possibly" target="_blank">StreamReader ThreadSafe Issue? Possibly?</a> (stackoverflow.com)</li>
</ul>
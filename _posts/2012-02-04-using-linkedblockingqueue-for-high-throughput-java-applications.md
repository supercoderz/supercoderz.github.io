---
layout: post
title: "Using LinkedBlockingQueue for high throughput Java applications"
date: 2012-02-04 14:20
comments: false
categories:
---

Java provides a LinkedBlockingQueue as part of the standard library from Java 5. This is a very easy to use blocking queue to share data between two <a class="zem_slink" title="Thread (computer science)" href="http://en.wikipedia.org/wiki/Thread_%28computer_science%29" rel="wikipedia">threads</a> and not run into any concurrency issues. I have used this as part of many uses cases where we had to deal with a producer creating a large number of objects to be consumed in a very short period of time and a set of <a class="zem_slink" title="Consumer" href="http://en.wikipedia.org/wiki/Consumer" rel="wikipedia">consumer</a> threads running to process these. There have also been cases where the producer is free to create objects at will, but the consumer controls how they are delivered to the upstream and needs a queue to hold things in between.

In all these cases, the performance was pretty damn good. But recently I ran into an issue which highlighted some of the subtle caveats around the usage of this class. This post attempts to explain those issues. I cannot divulge or replicate the server configuration, so this discussion will be purely theoretical with pointers to other literature on the internet where applicable.

<!--more-->
<h2>The Problem</h2>
The problem we had was that on a blade server running <a class="zem_slink" title="Red Hat" href="http://www.redhat.com" rel="homepage">Red Hat</a>, our <a class="zem_slink" title="Java (software platform)" href="http://en.wikipedia.org/wiki/Java_%28software_platform%29" rel="wikipedia">Java application</a> which consumes messages from a source locked up and hung when processing more than 2000 messages per second. Simple.  We had resolved a previous issue with ConcurrentHashMap and had all the usual bells and whistles like
<ul>
	<li>Use concurrent hash maps with putIfAbsent instead of put</li>
	<li>Do not share any maps where not required</li>
	<li>Enough cores and memory</li>
	<li>Heap size more than twice normal required amount</li>
	<li>No complex processing in each thread</li>
</ul>
When I did a thread dump, I could see that the LinkedBlockingQueue.poll(<a class="zem_slink" title="Timeout (computing)" href="http://en.wikipedia.org/wiki/Timeout_%28computing%29" rel="wikipedia">timeout</a>) method that we used in the consumers would call awaitNanos() and would be waiting on that.
<h2>Similar issues on the internet</h2>
I searched around on the internet for any known issues similar to mine and I found <a title="blocking queue issue" href="http://sourceforge.net/tracker/?func=detail&amp;aid=3086040&amp;group_id=69637&amp;atid=525264" target="_blank">this issue</a> which was very similar to mine. The consumer threads blocking up and not responding.

Also there was some discussion about Unsafe.park having issues on <a class="zem_slink" title="Solaris (operating system)" href="http://oracle.com/solaris" rel="homepage">Solaris</a> because of some <a class="zem_slink" title="Operating system" href="http://en.wikipedia.org/wiki/Operating_system" rel="wikipedia">OS</a> patches or issues in earlier builds of Java 6 like <a title="deadlock in earlier java 6 builds" href="http://stackoverflow.com/questions/1393139/deadlock-in-threadpoolexecutor" target="_blank">this one</a>.

Looking at my thread dumps and these issues pointed me to inspect the way I used my blocking queue.
<h2>LinkedBlockingQueue usage</h2>
In my case, the queue was created with the default max size of Integer max value. Since I would never even in theory exceed 2 billion messages, I could get away with this. Also to avoid any overhead in waiting, I used the offer() method instead of put() in the producer.

But on the consumer side, I used poll(1 sec) so that I could periodically give up the <a class="zem_slink" title="Central processing unit" href="http://en.wikipedia.org/wiki/Central_processing_unit" rel="wikipedia">CPU</a> when the timeout expired. There were few checks and logs after that poll to indicate that no message was received and then resume polling.

From all my logs, I could see that when the locking happened, I could not see all those logs after poll, and there was the thread dump showing Unsafe.park called from that poll.

LinkedBlockingQueue has a good performance for simultaneous insert and read as described here - <a href="http://www.javacodegeeks.com/2010/09/java-best-practices-queue-battle-and.html">http://www.javacodegeeks.com/2010/09/java-best-practices-queue-battle-and.html</a> - so clearly the usage was correct and the usage of poll(timeout) was most likely causing issues.
<h2>LinkedBlockingQueue <a class="zem_slink" title="Source code" href="http://en.wikipedia.org/wiki/Source_code" rel="wikipedia">source code</a></h2>
I looked up the source code for LinkedBlockingQueue at this site - <a href="http://kickjava.com/src/java/util/concurrent/LinkedBlockingQueue.java.htm">http://kickjava.com/src/java/util/concurrent/LinkedBlockingQueue.java.htm</a>.

You can see that poll(timeout) and take() are almost identical except that poll(timeout) has a for loop that wait in nano seconds if the item is not available.

This slight difference could potentially be important when messages come at 3 messages per millisecond and also given that conversion between clocks and timers can be erroneous. I have had cases where I calculate time taken and I get negative results.
<h2>Changes made to the code</h2>
In order to improve the performance I changed the code to use take() instead of poll(timeout). Running this at various rates of messages and taking thread dumps revealed that although the thread dumps showed the code waiting at take(), the overall performance improved a lot and I could see that now the application was able to handle loads greater than 3000 messages per second and not lock up.

Ultimately, we stopped at 4000 messages per second because that seemed reasonable considering the file I/O that was also involved.

Also with this change to take(), the average time taken to process each message fell by a few hundred milliseconds and was mostly in 2 digits all the time.
<h2>Conclusion</h2>
I might have missed a few finer details here and there, but overall it appears that using a poll with timeout is not a very good option at high message rates when the producer is continuously inserting messages. This issue could have been probably resolved by checking the OS patch and <a class="zem_slink" title="Java Development Kit" href="https://jdk6.dev.java.net/" rel="homepage">JDK</a> versions, but since that is not possible, using the take() method ensures that application performs better even with  these limitations.

Additional improvement can be achieved by using put() method if you feel that the queue can exceed maximum capacity.
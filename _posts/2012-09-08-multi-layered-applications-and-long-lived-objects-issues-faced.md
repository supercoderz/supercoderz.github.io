---
layout: post
title: "Multi-layered applications and long lived objects - issues faced"
date: 2012-09-08 10:59
comments: false
categories:
---

On my day job I build applications that need to get data in and out fast to the other services on our <a  title="Distributed computing" href="http://en.wikipedia.org/wiki/Distributed_computing" rel="wikipedia" target="_blank">distributed architecture</a> - speed is they key here and so is the ability to be able to reuse and build new services with existing code. We are more than happy to be able to build something that is configuration driven and can be easily re-purposed and deployed for another requirement by just changing the <a  title="XML" href="http://en.wikipedia.org/wiki/XML" rel="wikipedia" target="_blank">XML</a> files. All this leads to a design that is flexible, separates out responsibilities, has distinction between interface and implementation and more importantly layered. The layer that consumes does not enrich and the layer that enriches does not publish back again.

This works and works fine for most throughput scenarios, but once in a while it fails. Why does this fail? Is it a problem with the layers? Is it external? Is it the way each layer behaves? Lets find out.


<h2>Objects and references</h2>
We know that objects are passed around by reference. So lets say a piece of code calls a method with an object, then the <a  title="Object (computer science)" href="http://en.wikipedia.org/wiki/Object_%28computer_science%29" rel="wikipedia" target="_blank">object reference</a> is passed to that method so that it can manipulate it as it feels fit. If that method in turn calls other methods and passes this object, then it again sends a reference and this goes on until the object has been passed n levels deep and the logic is completed.

Since it is a reference that is being passed, if the calling method were to manipulate this reference, then all the downstream method calls that are using this reference will see the changes being made to the object.

But if you are calling a method, you surely should not be able to change the object until the method returns - right? Yes this is true, but it holds good only in single threaded environments. Lets say the method that made the initial call with the object reference picked this object from a queue or called it in a thread - then it is possible that while this object is being used the code that put the object in the queue or the code which stated the thread can manipulate this object. And this is what almost always causes the problems.

What kind of issues can we face?
<h2>Dirty reads, data corruption</h2>
If you have worked with any database then you know what a <a  title="Write–read conflict" href="http://en.wikipedia.org/wiki/Write%E2%80%93read_conflict" rel="wikipedia" target="_blank">dirty read</a> is - when you are able to read the changes done by a transaction even before it is fully committed. This happens to objects too. One of the main issues that I have seen with long lived objects that pass through multiple layers of code is that the values change unexpectedly. This does not always happen, but it shows up when the data rate is very high.

Typically when I get an object from an external <a  title="Application programming interface" href="http://en.wikipedia.org/wiki/Application_programming_interface" rel="wikipedia" target="_blank">API</a>, I take it though a series of calls where it is processed and is sent on to another service. This takes in the region of 50 milliseconds. That would mean that 200 messages per second can pass through my code. Sometimes the message rate is higher and the code slightly slows down and processes the data without any issues.

But in cases where the data rate say goes to 1000 messages per second, I notice that the amount of time taken to receive a new message is just a fraction of the time taken to process it completely. In these cases what I see is that if I don't acquire a lock, then the data gets corrupted.

In 99% of the cases it turns out that the API that I am using uses the same object to send the data again and again - the API wants to be quick, avoid memory issues and provide a good amount of performance - so it makes sense that it uses the same object to populate data for each message and then send to my call back method. I need to take care of this, but I forget and have issues.
<h2>Locking</h2>
I said I acquire a lock - there are cases where I put a synchronized block (Java speak) over the message that I picked from the API. This will mean that no other thread can modify the variable. This has caused the underlying API to hang and not be able to process more messages. This is not a very common case that I see and happens very rarely. The logical option then is to lock on another object.

But it is better to avoid locking if you can - like I will explain later.
<h2>Shared data</h2>
I love concurrent hash maps - they let me share data between threads without any issues and very easily. I use them a lot in my threads to maintain caches. These and linked blocking queues are very very much the life blood of my designs. However, one case that I run into is how I use the ConcurrentHashmap.

I found that if I use the default constructor and let it create the 16 buckets, then in the initial stages of my application when the cache is small, each bucket ends up having only one entry, and each time I do an insert, the map tries to re-hash the bucket. This becomes critical as I approach 1 message per millisecond. In those cases I have seen the application slow down to a crawl because it is has two threads waiting on the shared concurrent <a  title="Hash table" href="http://en.wikipedia.org/wiki/Hash_table" rel="wikipedia" target="_blank">hash map</a> to finish insert operation. Or a read waiting for insert to finish on the bucket.

The solution is to instantiate the concurrent hash map with a different constructor and specifying exactly how many buckets you want.
<h2>Are there any quick and easy solutions?</h2>
I could not find any solution for locking and shared data issues which I could say solved all my problems. But for the issue where the object gets modified while it is still being processed, I found a way out and it is fairly simple - I ensure that as soon as I get the data from any external API, I copy the data into a local variable and then pass it to further downstream threads.

What this means is that I am creating one object per message - if the messages are large or I need to cache too many of them then I will run into memory issues. This is a valid concern because when you are running a <a  title="Java (software platform)" href="http://www.java.com" rel="homepage" target="_blank">Java application</a>, you need to reserve enough <a  title="Dynamic memory allocation" href="http://en.wikipedia.org/wiki/Dynamic_memory_allocation" rel="wikipedia" target="_blank">heap space</a> in advance. And people dont really like it when you take only 10% of the <a  title="Central processing unit" href="http://en.wikipedia.org/wiki/Central_processing_unit" rel="wikipedia" target="_blank">CPU</a> but hog all <a  title="Random-access memory" href="http://en.wikipedia.org/wiki/Random-access_memory" rel="wikipedia" target="_blank">RAM</a> in a server. The solution to this has been to limit the amount of caching to just the key fields and not caching all the fields.

if you have an application that has  to run only a fixed number of hours every day, then you are lucky because all you need to ensure is that it has enough memory to get through the day. It might have a heap dump if it ran for 30 hours, but if it can get though 8 hours before you restart it - then cool!
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://plumbr.eu/blog/how-much-memory-what-is-retained-heap" target="_blank">How much memory do I need (part 1) - What is retained heap?</a> (plumbr.eu)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.drdobbs.com/article/print?articleId=210604448&amp;siteSectionName=parallel" target="_blank">Writing Lock-Free Code: A Corrected Queue</a> (drdobbs.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.javacodegeeks.com/2012/07/understanding-concept-behind.html" target="_blank">Understanding the concept behind ThreadLocal</a> (javacodegeeks.com)</li>
</ul>
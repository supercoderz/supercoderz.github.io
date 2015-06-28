---
layout: post
title: "Locking issues with Java blocking queues"
date: 2014-02-23 10:42
comments: false
categories:
---

A while ago I had posted about issues with poll() vs pol(timeout) on the Java concurrent blocking queues, and issues with the bucket sizes of concurrent hash maps. Those solutions worked well - but the issues with locks seems to love me so they are back. And this time time it is on the other end of the operation - the offer() and offer(timeout) calls.


<h2>The basic problem and solution</h2>
The application that we use need to resend some data at periodic intervals. So we take the data that was sent the first time and we cache it using ConcurrentHashMap instance. At the time of publish, we take this data and we offer to a linked blocking queue which is polled by a consumer thread. Simple, and works.

One day, we found that the cache had all the messages, but most of them were not republished at all.

This was happening in only one place in the code; in the other everything was working fine.

After a bit of digging we found that due to another issue on the incoming feed, the thread processing the incoming messages was putting messages on this queue too. So we had a multi producer single consumer blocking queue scenario.

The last time we resolved this issue, since it was always a single producer single consumer scenario we were sure that the queue would be empty or at least not full each time we started to push the messages. So we used the offer() method on the LinkedBlockingQueue class. And there was less contention for the putLock on the queue. But with about a 1000 messages being offered from the cache and messages coming on the feed too the queue was locking up between these two producers and none of them was getting the lock.

The issue was resolved after we converted the offer() to an offer(timeout) with a timeout of 500ms.
<h2>Can we avoid this problem?</h2>
The general opinion around this issue among my colleagues was that we could simply move the whole thing to use less threads and then we will have less contention issues to solve. That is valid. But in our case we need to consume the incoming messages as fast as we can or end up getting throttled or kicked off. Its because of the type of data, and that we cannot take the data out of sequence.

Our main issue was locking within the queues, not the capacity. So if we assume that we can create a queue of any size, and that there will be no instances of queue being full, then the core issue comes down to the lock that needs to be shared between the two threads.

Looking around on the internet I found 2 posts on lock free queues in Java - what these seem to be doing is using the Java atomic operations and lazySet to set the values on the queue without using a lock object. This means that each thread that is putting a message on the queue ,provided the queue is not full, will be able to put the message on the queue without waiting for other threads. The atomic classes push down the locking logic down into the OS and hardware, that is more efficient than we trying to lock using code.

Here are the links -

<a href="http://psy-lob-saw.blogspot.sg/2013/03/single-producerconsumer-lock-free-queue.html">http://psy-lob-saw.blogspot.sg/2013/03/single-producerconsumer-lock-free-queue.html</a>

<a href="http://psy-lob-saw.blogspot.sg/2013/10/lock-free-mpsc-1.html">http://psy-lob-saw.blogspot.sg/2013/10/lock-free-mpsc-1.html</a>

We will try these on our code some time soon. The one case where I think this might fail is if the atomic operations run on 2 different cores - in that case I am not sure how the queue access will work. Have to test that in our case.

Hopefully this will be the end of our issues with locks and queues.
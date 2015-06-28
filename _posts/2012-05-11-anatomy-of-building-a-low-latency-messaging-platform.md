---
layout: post
title: "Anatomy of building a low latency messaging platform"
date: 2012-05-11 00:00
comments: false
categories:
---

[caption id="" align="alignright" width="300"]<a href="http://commons.wikipedia.org/wiki/File:The-amqp-model-for-wikipedia.svg" target="_blank"><img  title="AMQP Model" src="http://upload.wikimedia.org/wikipedia/commons/thumb/9/9d/The-amqp-model-for-wikipedia.svg/300px-The-amqp-model-for-wikipedia.svg.png" alt="AMQP Model" width="300" height="225" /></a> AMQP Model (Photo credit: Wikipedia)[/caption]

Disclaimer - I am not going to reveal here that <a  title="Message" href="http://en.wikipedia.org/wiki/Message" rel="wikipedia" target="_blank">messaging</a> systems are built from a secret alien technology that none of us knew about. We all know that messaging <a  title="Application software" href="http://en.wikipedia.org/wiki/Application_software" rel="wikipedia" target="_blank">applications</a> are built using simple sockets, <a  title="Transmission Control Protocol" href="http://www.techopedia.com/definition/5773/transmission-control-protocol-tcp" rel="techopedia" target="_blank">TCP</a> or <a  title="User Datagram Protocol" href="http://en.wikipedia.org/wiki/User_Datagram_Protocol" rel="wikipedia" target="_blank">UDP</a> protocol, some sort of mechanism to represent queues or topics, some storage on the back end to persist messages between restarts, a means to subscribe to data and receive call backs. These are generally all that you have in a messaging system.

If you never knew about messaging systems, then the above probably gave you an idea about what they are built of.

What I am going to tell here is what parts of this need to be super tuned and super fast in order to get a <a  title="Low latency" href="http://en.wikipedia.org/wiki/Low_latency" rel="wikipedia" target="_blank">low latency</a> messaging platform. This is based upon my experience of building and using some such platforms.

<!--more-->
<h2>What is a low latency messaging platform?</h2>
A low latency messaging platform is one that takes a message from a source and gets it to the destination in the shortest time possible. It has to be able to do this again and again no matter what the load on the system. Simple. If you think about it, all the  platform has to do is to take the message from the source, put some headers and routing information on that, stream it out on a socket to a server and from there to the <a  title="Subscription business model" href="http://en.wikipedia.org/wiki/Subscription_business_model" rel="wikipedia" target="_blank">subscriber</a> or directly to the subscriber in some cases.

Just doing these fast will be enough - right? Well yes and no - you need to do all these, do them fast enough and also ensure that can be done equally fast when the load increases to twice or thrice what you planned for.
<h2>Problem 1 - <a  title="Serialization" href="http://en.wikipedia.org/wiki/Serialization" rel="wikipedia" target="_blank">data serialization</a></h2>
When you want to send across a message from your application, you will send text, <a  title="xml tools" href="http://download.cnet.com/windows/xml-tools/" rel="downloadcom" target="_blank">XML</a> or objects - these cannot be sent as it is and will need to be converted to a stream of <a  title="Byte" href="http://www.techopedia.com/definition/23955/byte" rel="techopedia" target="_blank">bytes</a> that can be sent over a socket connection so that they can be received at the other end. Different languages convert these objects or strings to a stream of bytes in different ways. If it is a string - text or XML - then converting it to a byte stream is easy - pick each character in the string and send across the byte representation of that.

Objects and types are difficult to convert. You need to send information about the object, then its fields, the values of the fields and their types and any meta data that might be required to re-create the object or type on the other end. If your object is a collection or a container with many sub objects then you will need to send the same kind of detail about all those objects.

Programming languages provide means to do all this - the problem is that they are sometimes not fast enough. If you need to send across a message in 2 milliseconds and the language takes 10 milliseconds to serialize the object then you might as well forget about it.

So, if you want to build a low latency messaging platform, you need to identify how much you can allow for each message to be serialized so that it wont add up to your total latency. Then you need to pick a library for the language of your choice which lets you achieve that speed.

You can also build your own protocol based on different encoding techniques for data using field ID's , positional fields, custom byte formats or even <a  title="Cross-platform" href="http://en.wikipedia.org/wiki/Cross-platform" rel="wikipedia" target="_blank">cross platform</a> serialization tools like Google Protocol Buffers.
<h2>Problem 2 - direct to destination or via the server?</h2>
This actually depends on the application of the platform. I once built a messaging system where it did not matter to the applications if the messages arrived in real time or not. They were happy for messages to get queued on the server and take them slowly. But for us it mattered how fast we could get them into the queues so that the network was not clogged.

So we used dedicated TCP connections from all clients to the server to send the messages as fast as possible and queue them up.The clients and servers were organized in such a way as to minimize the number of connections on each server and let the server handle all the load from the connections.

In another case where I was a consuming application, it mattered how quickly we could get the data - so there was a custom protocol over UDP, with special OS patching that allowed the source to quickly multicast the message to a few hundred subscribers. The data itself was encoded in format optimized for quick decoding.

Based on which approach you take, an additional hop is added to your flow and this adds to the total latency. You must decide this based on the requirements of each application in you platform.
<h2>Problem 3 - topology</h2>
I briefly mentioned in the previous section about organizing servers and clients. It is common sense that the fastest route between source and destination is when they are directly connected to each other. The next best is when they are connected to the same server. If they were connected to two different servers and the replication time between these two servers itself was very high then it would be very very bad.

Again if everything connected to the same server then it will be very bad. You can only allow some to connect to the same server and the rest must wait their turn.

You could however, connect a bunch of servers in such a way that the data replicates fast to all of them and then connect clients to these servers - this is called server topology. You can follow any of the patterns that you learnt in your CS course about network topologies and build a server topology that minimizes the latency for the message to be available on all servers.

Once the message gets on the servers, then it is the subscribers responsibility how fast they want to process the messages.
<h2>Problem 4 - persistence</h2>
If you were to be passed a ball, the fastest way to give it to the next person would be to just throw the ball back. If you had to do something with the ball and then throw it, then it would definitely take time and resources. The same applies to the messaging platforms - if the server has to make the messages persistent before they are made available to the subscribers or put on a queue, then there will be a delay and total latency will increase.

If the server were to just take the message and make it available in memory then it will be much much faster - there will be the overhead of having to maintain lots of stuff in memory and the huge memory needed to do this - but it will be fast.

Whether you want to persist the messages or not depends on how important the messages are and whether you can easily recreate the message or if there will be a loss if any of the messages is lost. If you don't care if a message is lost when the thing is restarted because you can resend or recreate the message then you can skip persistence and be the fastest messaging system.

But if you do need persistence then you need to pick a mechanism which is fast enough - you could use a file, a database, some custom storage - but whatever you use should be fast. Otherwise the amount of time that you spend in storing the message will add up to your overall latency.
<h2>Problem 5 - message ordering</h2>
How fast a messaging system works also depends on whether it has to order the messages. If the application can send messages and the destination does not care which sequence these arrive in then the platform can simply route the messages without worrying about the sequence.

However, if the sequence of the messages matters, then the messaging platform has to maintain the sequence numbers, put them on each message and then have a mechanism to check and re-order the messages as required.

The algorithm used for this has to be fast enough for the whole platform to work faster.
<h2>Problem 6 - acknowledgements</h2>
When an application sends a message, it sometimes expects an acknowledgement from the destination to indicate that the message was processed correctly. In certain business critical use cases, this is the core requirement. But in many other cases, the acknowledgments can arrive in an async manner as separate messages and will be handled by the application.

If you want to build a really fast messaging systems, then the sender should not wait for the consumer to send the ack and should send as fast as it can.
<h2>Problem 7 - physical hardware</h2>
This is probably the most important issue - no matter how fast your code can go, and how quick your storage etc are, if the physical hardware or the network cabling is not up to scratch then everything will fail. You need to ensure that all disks and memory are allocated close to each other and there is not delay in the travel time between there.

If possible everything should be mounted on the same rack or connected with optical fiber, and possibly with more then one network interface for each server.

You could also consider using flash memory instead of traditional disks. There are so many ways to make hardware go fast.
<h2>Conclusion</h2>
As you can see building a low latency messaging platform is dependent on getting the key elements tuned and working at the best of their speed - this will ensure that the overall system goes very fast.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://www.rabbitmq.com/blog/2012/04/17/rabbitmq-performance-measurements-part-1/" target="_blank">RabbitMQ Performance Measurements, part 1</a> (rabbitmq.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://carpetbomberz.com/2012/03/21/2114/" target="_blank">500,000 requests/sec - Modern HTTP servers are fast</a> (carpetbomberz.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.datacenterknowledge.com/archives/2012/04/19/optical-networks-low-latency-for-data-centers/" target="_blank">Optical Networks &amp; Low Latency for Data Centers</a> (datacenterknowledge.com)</li>
</ul>
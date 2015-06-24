---
layout: post
title: "AMQP, ZeroMQ, JMS, TIBCO RV and so on - which messaging system to use?"
date: 2012-05-20 10:39
comments: false
categories:
---

[caption id="" align="alignright" width="300"]<a href="http://en.wikipedia.org/wiki/File:Rvrd-multicasting.jpg" target="_blank"><img class="zemanta-img-inserted zemanta-img-configured" title="300" src="http://upload.wikimedia.org/wikipedia/en/thumb/5/58/Rvrd-multicasting.jpg/300px-Rvrd-multicasting.jpg" alt="300" width="300" height="191" /></a> 300 (Photo credit: Wikipedia)[/caption]

[caption id="" align="alignright" width="300"]<a href="http://commons.wikipedia.org/wiki/File:The-amqp-model-for-wikipedia.svg" target="_blank"><img class="zemanta-img-inserted zemanta-img-configured" title="AMQP Model" src="http://upload.wikimedia.org/wikipedia/commons/thumb/9/9d/The-amqp-model-for-wikipedia.svg/300px-The-amqp-model-for-wikipedia.svg.png" alt="AMQP Model" width="300" height="225" /></a> AMQP Model (Photo credit: Wikipedia)[/caption]

I have been working with middleware systems for the last 8 years, building and using them in many ways. And even after all this I must say that there is lot of confusion in my head as to what a messaging platform must offer and what it should do. Confusion not because I don't know my stuff, but more because you can solve the problem in more ways than one.

Sometime back I was looking at the <a title="AMQP" href="http://en.wikipedia.org/wiki/AMQP" target="_blank">AMQP</a> standard to understand the state of the art in terms of messaging protocols other than <a class="zem_slink" title="Java Message Service" href="http://en.wikipedia.org/wiki/Java_Message_Service" rel="wikipedia" target="_blank">JMS</a> which I use a lot. I quickly realized that there was <a title="ZeroMQ" href="http://www.zeromq.org/" target="_blank">ZeroMQ</a> which was built by iMatix who were part of AMQP but moved away. While AMQP defines the wire format of the message and works on a broker based model, ZeroMQ tries a brokerless model. There is an interesting discussion on the <a class="zem_slink" title="RabbitMQ" href="http://www.rabbitmq.com" rel="homepage" target="_blank">RabbitMQ</a> website which talks about these two approaches - <a href="http://www.rabbitmq.com/blog/2010/09/22/broker-vs-brokerless/">http://www.rabbitmq.com/blog/2010/09/22/broker-vs-brokerless/</a>.

That made me think about all these years and what kind of problems was I solving with messaging. I realized that if you pick out the type of problem that you want to solve, then it becomes easy to decide the solution.

This post is a discussion of how to go about understanding the problem that you are trying to solve.

<!--more-->
<h2>A few stories from the past</h2>
I started my career building adapters for <a class="zem_slink" title="TIBCO Rendezvous" href="http://www.tibco.com/software/messaging/rendezvous" rel="homepage" target="_blank">TIBCO Rendezvous</a>. My company was a <a class="zem_slink" title="Tibco Software" href="http://www.tibco.com/" rel="homepage" target="_blank">TIBCO</a> partner, and we could get to build the cool adapters which were used for various <a class="zem_slink" title="Enterprise application integration" href="http://en.wikipedia.org/wiki/Enterprise_application_integration" rel="wikipedia" target="_blank">Enterprise Application Integration</a>(EAI) projects that everyone was doing. It was a great messaging platform which allowed publish subscribe and even request reply paradigms. There was guaranteed delivery too and messages could be recorded. Once in a while we found that when we used guaranteed delivery and the subscriber was not immediately available, the messages would get stored in journal files, the files would grow and then at some point the while messaging would slow down. So we tried to avoid these cases.

However, these became quite common and soon there were requirements on projects and support calls where the client wanted to store messages without losing performance - so we moved them to another TIBCO product called TIBCO EMS which was the JMS implementation from TIBCO. This allowed us the same publish and subscribe approach and occasional request and reply, but the added advantage being that when messages piled up, the system did not slow down. This was because instead of the TIBCO RV daemons storing messages in log files, EMS was a single centralized server that stored the messages in a more efficient manner and was able to service requests at the same speed independent of how many messages were stored.

Few years later though I came across a problem where the number of messages piled up was large and there was a noticeable degradation of performance in the system. We got around this by having multiple EMS servers and letting the slower subscribers connect to a different server than the quicker ones. Now this place was a bank, and there were few applications that needed very quick real time messaging  - even on our quickest route, there was a small delay which was not acceptable to them so they decided to use TIBCO RV and I remember one of them atleast went the way of writing their own custom approach.

Even now, in my day to day work, the number of applications that want to queue messages is very small, near zero.
<h2>The purpose of all the flashback we just had</h2>
The purpose was to bring out the types of systems that need messaging - as you can probably see, there are two types of systems
<ol>
	<li>Those that care whether a message comes to them immediately after they are sent; these consume messages very fast</li>
	<li>Those that do no care how quickly a message comes so long as it comes; these do not always consume the messages very quickly</li>
</ol>
If we further think about #1, these are usually banks, airlines,traffic monitoring,industrial monitoring kind of applications where the lifetime of an event is very shot before it gets overwritten by the next event. Once the event expires, there is no point in receiving or handling the event and at any point of time all we need is the most recent value.

#2 on the other hand, these are systems where getting the message is more important than when we got that and how quickly we processed that. This could be like at the end of the day a system getting the receipts from clients for all transactions done through the day - the core aim is that the receipts must arrive and be processed and it can take all night if needed. Or for example, orders or inventory updates coming from resellers or vendors to a large retail company.

#1 is the more real time applications and #2 is the more normal placed applications - business wise for both these systems the messages are critical and must be processed at all costs with the same kind of guarantees.

Whenever you are solving a messaging related problem - you will fall into one of these categories. It will not be black and white, but you will be able to find that you are more towards one of these than the other so you can approximate black and white.
<h2>Let us understand the platforms that I mentioned in the title of this post</h2>
This blog post is a very good explanation and comparison of TIBCO RV and TIBCO EMS, it summarizes most of what i said, but more importantly tells how TIBCO RV works - <a href="http://www.narendranaidu.com/2006/01/tibco-rv-vs-tibco-ems.html">http://www.narendranaidu.com/2006/01/tibco-rv-vs-tibco-ems.html</a>.

To paraphrase, TIBCO RV is a single daemon or a set of daemons which broadcast messages between themselves if they are on the same network. This is done using UDP and each daemon in turn then picks the messages on topics that the clients connected to that daemon are interested in. If there are daemons on different networks, then there is a <a class="zem_slink" title="RendezVous Routing Daemon" href="http://en.wikipedia.org/wiki/RendezVous_Routing_Daemon" rel="wikipedia" target="_blank">RVRD</a>, which routes between networks. And in the ideal case, they recommend one daemon per machine where there are clients wishing to connect to RV. If you did not do this and used just one RVD and connected everyone to that, then it will become something similar to the hub and spoke approach pf TIBCO EMS or other JMS providers like Websphere MQ or <a class="zem_slink" title="Apache ActiveMQ" href="http://activemq.apache.org" rel="homepage" target="_blank">ActiveMQ</a> etc.

JMS is an API specification for Java - you could build a Python API that mimics JMS - but at the end of the day, if you wanted the Python application to understand the data put on a JMS server by a Java application, then you need to come up with a schema, maybe XML, which both languages can parse and understand.

AMQP is a wire format spec that specifies how the data format should be - and as long as the client written in any language can understand this format it can work with AMQP. There is a broker in AMQP - this can be one single broker or a set of federating brokers - and they provide the various entities like exchanges, queues and message storage in queues. RabbitMQ and <a class="zem_slink" title="Apache Qpid" href="http://qpid.apache.org/" rel="homepage" target="_blank">Apache Qpid</a> and AMQP implementations and each has its own good points.

ZeroMQ does not care about the data format apart from its length in bytes, and it is a more peer to peer type of networking without a central broker where the socket connection itself can be considered the messaging platform.

I would say it is possible to build a platform which has a JMS API but uses AMQP as the wire format; or a platform where ZeroMQ is the way to connect to each other, but AMQP is the underlying data.

For that matter TIBCO RV can send across any type of data because it is a key value pair type of message. But it has its own API and I don't remember anyone doing a platform with JMS API but TIBCO RV as underlying transport - that is because RV is not suitable for hub and spoke kind of thing.
<h2>Centralized vs Decentralized</h2>
As mentioned in the broker vs brokerless approach link from RabbitMQ website which I gave at the beginning, having a broker adds to the delay in the end to end message latency, but provides a guarantee that you will be able to get to an endpoint  without you having to worry about whether the end point is available or not.

The three problems if you don't have a broker - discovery, availability and management.

You could have a set of federated brokers in a certain topology such that they can route you to the end points quickly than a single broker and that way you can get smaller end to end latency compared to single broker. TIBCO RV is something similar where there are a bunch of daemons that multicast to each other, but clients connect to a single daemon and get messages from that. And since a daemon knows what topics its clients are interested in, it can filter out messages received in the multicast and only take those which the clients are interested in.

A brokerless approach or peer to peer approach is a more extreme approach where you can think of it as merging the daemon and the client from the TIBCO RV approach. Each application has a small number of clients which are internal threads, and the application itself works as a daemon. It receives a large number of messages from the network and filters out only those which are required by the threads that are subscribing to it.

So you can see that its just a perspective thing whether you have a broker or where you have the broker.
<h2>You could just write to a database</h2>
If you look at <a title="Kombu" href="http://ask.github.com/kombu/">Kombu</a> - it provides virtual transports which allow you to use a database as a back end. What that means is that there will be cases where you do not require a messaging platform. I built a small web app where I had to queue tasks and there was no latency requirement. This was a Django application so I used a Kombu plugin for Django to just put the messages in the database. Then I used Celery which can subscribe from Kombu to get these messages from the database and work on them.

Your application might have messaging needs that might just work with having a database. In fact there was something called Oracle AQ which was a database backed messaging platform.
<h2>Importance of making the right choice</h2>
It is very important that you make the right choice upfront because once you have a large enough application, even if you bite the bullet and decide to revamp the whole thing, then it will take time and be painful. If you want to use JMS on day 1 and then sometime move to AMQP, you need to ensure that the API you are using or the interface that you are building is robust enough to transparently allow plugging in a wire format spec like AMQP.

Similarly if you wanted to plug in TIBCO RV or ZeroMQ style brokerless approaches, then you have more complexities of ensuring that your interface can handle this.

With a decent usage of design patterns you will be able to abstract out all these details to your applications with an API of your own. As of now there is no single API out there which will work with all messaging protocols.
<h2>Conclusion</h2>
The choice of which approach to use depends on your latency requirement and how much you can afford to relax it. All the approaches that I described will work, but you must prototype, benchmark some results and then decide. Plus you must leave in the room in your interfaces so that you can easily switch underlying transports.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://www.javacodegeeks.com/2012/04/connect-to-rabbitmq-amqp-using-scala.html" target="_blank">Connect to RabbitMQ (AMQP) using Scala, Play and Akka</a> (javacodegeeks.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://java.dzone.com/articles/migrating-jms-amqp-rabbitmq" target="_blank">Migrating From JMS to AMQP: RabbitMQ, Spring, Apache Camel, and Apache Qpid</a> (java.dzone.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.javacodegeeks.com/2012/04/rabbitmq-scheduled-message-delivery.html" target="_blank">RabbitMQ: Scheduled Message Delivery</a> (javacodegeeks.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://blog.lusis.org/blog/2012/02/06/zeromq-and-logstash-part-1/" target="_blank">ZeroMQ and Logstash - Part 1</a> (lusis.org)</li>
</ul>
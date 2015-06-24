---
layout: post
title: "Understanding the new messaging frameworks with virtual backends"
date: 2012-11-03 17:35
comments: false
categories:
---

[caption id="" align="alignright" width="300"]<a href="http://commons.wikipedia.org/wiki/File:The-amqp-model-for-wikipedia.svg" target="_blank"><img class="zemanta-img-inserted zemanta-img-configured" title="AMQP Model" alt="AMQP Model" src="http://upload.wikimedia.org/wikipedia/commons/thumb/9/9d/The-amqp-model-for-wikipedia.svg/300px-The-amqp-model-for-wikipedia.svg.png" height="225" width="300" /></a> AMQP Model (Photo credit: Wikipedia)[/caption]

A few weeks back when I was looking at how I could build a synchronous task execution system for a Django app, I came across Celery, and then I found something called Kombu (<a href="http://kombu.readthedocs.org/en/latest/">http://kombu.readthedocs.org/en/latest/</a>), which was a message passing framework for Celery.

Kombu claimed to be a messaging framework for Python. And when I started to look at the website, out of my usual habit of looking at <a class="zem_slink" title="Java Message Service" href="http://en.wikipedia.org/wiki/Java_Message_Service" target="_blank" rel="wikipedia">JMS</a> and <a class="zem_slink" title="Message queue" href="http://en.wikipedia.org/wiki/Message_queue" target="_blank" rel="wikipedia">MQ</a> systems, I looked for a Kombu broker. I could not find one. Surely something was missing - when I downloaded <a class="zem_slink" title="ActiveMQ" href="http://activemq.apache.org/" target="_blank" rel="homepage">ActiveMQ</a> it came with a broker just like all the messaging systems I used at work. And then it struck me, that maybe Kombu was just like the JMS API, something that needs to be implemented by a messaging provider like ActiveMQ.

I was wrong, and further digging around told me that that Kombu was an implementation of the <a class="zem_slink" title="Advanced Message Queuing Protocol" href="http://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol" target="_blank" rel="wikipedia">AMQP</a> interface, and that it needed an AMQP broker like <a class="zem_slink" title="RabbitMQ" href="http://www.rabbitmq.com" target="_blank" rel="homepage">RabbitMQ</a> to work. Just like what I was used to. Whew? No.

I then saw that Kombu had something called Virtual transports which let it use things other than RabbitMQ as a backend - something like a database or a key value store like Redis! This was awesome and it was one of the few things that I had in mind each time I read someone say something like 'can I use mongodb as a messaging system?'. Of course, those questions were about user to user messaging or task distribution etc - I even wrote a long post on <a href="http://supercoderz.in/2012/01/29/why-do-you-use-messagingmiddleware/" target="_blank">why do you use a messaging middleware</a>?

This post is an attempt to explain how a messaging server works and how these virtual backends are built, and how they can make it easy for us by not needing us to manage message storage etc.

<!--more-->
<h2>How does a message broker work?</h2>
A message broker like ActiveMQ or a commercial one like TIBCO EMS, are nothing but a server process that accepts connections from clients and sends or receives messages. These brokers implement one of the many messaging protocols like JMS and provide either publish subscribe or queue based message passing or both. They even support durable or stored messages. In order to so this, a message broker needs to
<ol>
	<li>Have a thread that accepts connections and maintains the state of each connection and related metadata</li>
	<li>Have a means to create and manage entities like queues, topics, messages etc in memory</li>
	<li>Have a means to get  a message from a client and convert it into the relevant entity - although usually the client API creates these messages and the server only manages the queues and topics etc</li>
	<li>Manage routing between all the queues and topics</li>
	<li>Manage storage of these messages</li>
	<li>Manage distribution of messages to other connected brokers if required.</li>
</ol>
The broker has an option whether it wants to really persist everything to disk or maintain in memory. There is usually a configuration that lets the broker know whether it has to persist the messages to a disk before delivering to the consumers or not. A message that is stored and then delivered experiences a slight delay. Similarly all the queues and topics that the messaging broker maintains can also be persisted so that they are available at startup. Some brokers provide ability to create these queues and topics on the fly and some provide the ability to have them created when the server is started up. In the second case, if the queue or topic is being created at start up, then the broker also stores any pending messages at the time when the server was shut down and these messages are available when the server comes back up again.

If we think of message creation as a client API side task, and messages persistence and creation of queues and topics as a server side process, then message routing is something that can be done on the server or the client - it is a grey area.

And this is what helps us in building these messaging frameworks with virtual backends.
<h2>Defining the back end of a messaging framework</h2>
If we say that we will build a messaging framework for JMS, and we will not build a broker, then what are the things that have to be done on the client side? We could say -
<ol>
	<li>A means of connecting to whatever provides the backend of the framework from where other clients can consume messages</li>
	<li>A means of creating the messages - like all client <a class="zem_slink" title="Application programming interface" href="http://en.wikipedia.org/wiki/Application_programming_interface" target="_blank" rel="wikipedia">API's</a> do</li>
	<li>A means of identifying the queue or topic in the backend and putting the message in that so that it can be consumed by other clients</li>
</ol>
Anything else is a backend job.

If we built a framework for AMQP, then we will have to support the concept of routing in the framework - like the logic to define an exchange, so that it can figure out the target queues or topics so that it can put messages into whatever represents these queues or topics in the backend.

If all that is needed for the backend is to accept connections and store the messages in logical entities like queues or topics, then it can be anything like a database or a key-value store or even a simple server that writes out to a file on the disk or <a class="zem_slink" title="SQLite" href="http://sqlite.org" target="_blank" rel="homepage">SQLite</a> database or a <a class="zem_slink" title="Berkeley DB" href="http://www.oracle.com/us/products/database/berkeley-db/index.html" target="_blank" rel="homepage">Berkeley DB</a>. Although the last two might mean that there will be only one write thread.

So we could use any storage system as a backend. All we need is to write all the client API and routing logic as a layer in the API, and then give it an abstract storage interface. This interface can then be implemented to store to anything.
<h2>It isn't something entirely new and can be pretty good</h2>
There was a product called <a class="zem_slink" title="Oracle Advanced Queuing" href="http://en.wikipedia.org/wiki/Oracle_Advanced_Queuing" target="_blank" rel="wikipedia">Oracle AQ</a> (Advanced Queueing) which exposed JMS like queues backed by RDBMS tables. You want to store a message in a queue - either connect to the JMS interface and send messages or just insert a row in the table! One way I saw this being used was where a large database with lots of business data would run a set of stored procedures, the procedures would complete some business processing and create data that will need to be sent out to other systems. They can just insert these into the queue table. The clients don't need to connect to the database, they don't need to be aware of the schema or anything. They just consume a queue.

The database provides pretty good data durability, consistency, reliability and recovery. You just have to worry about your subscriptions and not about data loss.

Similarly if we used something like Redis or <a class="zem_slink" title="MongoDB" href="http://www.mongodb.org/" target="_blank" rel="homepage">MongoDB</a> which have very good support for storing blobs of objects and querying them, and are efficient at replicating data, all your messaging frame work has to worry about is to put in the correct list or collection. Everything else comes for free.

Traditionally, messaging servers provided storage which probably worked only for certain kind of data or was accessible only to the client API. But by storing in something like MongoDB, you can store whatever data you want, and it can be accessed by any application which uses the API of the backend and doesn't have to use your messaging framework.

Also, you can choose the kind of backend that best suits your data and processing needs. If you were processing image files for example, you might want to use MongoDB as a backend. But if you were processing json data, then you can choose Redis. No rule that you should do that, but MongoDB support file storage better.

Or maybe you want to store data as geo coded with the location of the publisher or subscriber - the possibilities are countless.
<h2>You can even do an in-memory backend!</h2>
Who said messaging has to be between different applications? You can build an in-memory backend which runs within your application and provides messaging between different threads in your application. This is very useful for example in Java applications that have many threads doing different operations on the same data. This is usually done using blocking queues which are available in Java.

But if we could could move this in memory queuing to use a messaging framework that supported JMS or AMQP and worked as an embedded in memory broker, then it will be very easy to migrate the code if we ever wanted to move to a dedicated external broker.
<h2>Conclusion</h2>
Virtual backends for messaging frameworks are very useful because they can let us choose any backend that suits our data requirements.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://pypi.python.org/pypi/kombu/2.4.8" target="_blank">kombu 2.4.8</a> (pypi.python.org)</li>
	<li class="zemanta-article-ul-li"><a href="http://java.dzone.com/articles/backing-spring-integration-1" target="_blank">Backing Spring Integration Routes with ActiveMQ</a> (java.dzone.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.sys-con.com/node/2426985" target="_blank">Advanced Message Queuing Protocol (AMQP) 1.0 Becomes OASIS Standard</a> (sys-con.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://javainsider.wordpress.com/2012/09/25/simple-guide-to-java-message-service-jms-using-activemq/" target="_blank">Simple guide to Java Message Service (JMS) using ActiveMQ</a> (javainsider.wordpress.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.slideshare.net/piykumar/pycon-india-2012" target="_blank">PyCon India 2012: Celery Talk</a> (slideshare.net)</li>
</ul>
---
layout: post
title: "Why do you use Messaging/Middleware?"
date: 2012-01-29 20:58
comments: false
categories:
---

Just why do you use messaging? If you don't use messaging or have never heard about it, then - read on.

<a href="http://commons.wikipedia.org/wiki/File:EAI.png"><img  title="Enterprise Application Integration. Schema gem..." src="http://upload.wikimedia.org/wikipedia/commons/thumb/c/c8/EAI.png/300px-EAI.png" alt="Enterprise Application Integration. Schema gem..." width="300" height="205" /></a>

If I do a Google search for why do you use messaging, I get answers that are mostly
<ul>
	<li><a  title="Scalability" href="http://en.wikipedia.org/wiki/Scalability" rel="wikipedia">Scalability</a></li>
	<li>Distributed Asynchronous processing to take the load of the application or distributing a task on many nodes</li>
	<li>Robustness by building small components that communicate</li>
	<li><a  title="Remote procedure call" href="http://en.wikipedia.org/wiki/Remote_procedure_call" rel="wikipedia">RPC</a> - hmmm</li>
	<li><a  title="Legacy system" href="http://en.wikipedia.org/wiki/Legacy_system" rel="wikipedia">Legacy application</a> integration - hmmmmm</li>
</ul>
These are just some of the things that I see on the internet. Most of the people seem to talk in terms of web applications doing things like sending emails and queuing them on <a  title="Amazon Simple Queue Service" href="http://en.wikipedia.org/wiki/Amazon_Simple_Queue_Service" rel="wikipedia">Amazon SQS</a>, or things like web sites scaling by moving the time consuming tasks to a queue to be done slowly etc and once in a while someone mentions integrating with legacy apps or ESB. There was even a messaging system with Twitter integration! What is the world coming to? Is messaging only limited to web applications scaling or legacy applications talking to new apps?

Whatever happened to all those high frequency trading systems and all those B2B and B2C systems that used messaging? Or all those applications where systems in different regions communicated with each other using messaging not because they wanted to scale or distribute work load but because they wanted to exchange information?

Did the world go through a lot while I was sleeping?

Full disclosure, I was and am a messaging/middleware guy who re-built from scratch parts of bespoke messaging application, worked on what is called <a  title="Enterprise application integration" href="http://en.wikipedia.org/wiki/Enterprise_application_integration" rel="wikipedia">Enterprise Application Integration</a> using <a  title="TIBCO Rendezvous" href="http://www.tibco.com/software/messaging/rendezvous" rel="homepage">TIBCO Rendezvous</a> - a very fast messaging platform - and still does a lot of interfacing with messaging layers - the main aim always being to get the message across correct and fast. Scaling is a side effect in my job.

In this post, I will try to explain some of the use cases where messaging is used and which do not come under the scalability, distributed apps etc etc buzzwords. What I am trying to highlight here is that messaging is used to keep the enterprise, the business together and make it work smoothly rather than just scaling or distributing workload for asynchronous processing.
<h2>Just what kind of messaging will I talk about?</h2>
If you want to think of messaging in terms of a formal definition then you can think of it as two components communicating with each other by sending data in a agreed format, sometimes following a specified schema. Its like you write a letter and post it with stamps. Simple.

Since we said components, there can be two cases -
<ol>
	<li>Two parts of the same application talking to each other</li>
	<li>Two applications talking to each other</li>
</ol>
The first case is what we could think of as  two threads talking to each other. I would not call this *messaging* because this is not between two components that cannot directly talk to each other. Messaging for me is mostly the type covered by case 2 where two applications have to talk using an intermediary platform. For case 1 we could use things like a blocking queue in Java which work in the same application memory where both threads are running. We could use an embedded pub/sub system but that will be an overkill.

So, I am going to talk about the second kind of messaging between components where they cannot directly call each other and need an intermediary.
<h2>Some business cases that I myself worked on where it was not for scalability</h2>
Just a brief summary of cases where I used messaging - and in all these cases, the main aim was to let applications communicate and get data from each other rather than scale up or become more robust. These were not old systems built on mainframes, these were not even related to each other in terms of function - they just did different tasks in the enterprise which were part of a larger workflow.
<ol>
	<li>A telecom company running a large number of customer centers across multiple countries use a certain billing application to capture customer on-boarding and data. This data needs to be sent to all back office applications for use in reporting, advertising and other departments and also archived for regulatory purposes. Plus there was routing of data between these applications.The billing and back office  applications are not trivial and cannot be replaced by a do-all in house application, and that is not core competency of the company. The solution we provided involved making sure that every update to the billing application is captured and communicated to the back office applications. Time and latency were not a constraint but there were SLA's. Data on the queues needed to be persistent in the event of failure. Simply connecting all apps to a single database was not possible. We built this using a <a  title="Java Message Service" href="http://en.wikipedia.org/wiki/Java_Message_Service" rel="wikipedia">JMS</a> server and each application had a queue on it for incoming and outgoing messages - routing was achieved by <a  title="XML" href="http://en.wikipedia.org/wiki/XML" rel="wikipedia">XML</a> rules.</li>
	<li>A large bank had 100+ applications in middle and back office that needed means to route data between them in a clean and efficient manner and keep records of all data that was routed. Data was sent as per a defined schema and no two applications were allowed to publish the same fact. Data could be replayed from records for up to a year. Applications were distributed across the globe. This application was built using a JMS server, with servers across the globe connected to each other and replicating messages that were marked as global. Data was XML, validated against a schema and monitored using a web application. There were many helper components to provide value add services. The message latency was not important, but load scalability was - the delivery mechanism should not fail under load, apps can take time to consume messages -  the application was able to process 1 million messages within an hour with no backlog - all messages were delivered to all subscribers and successfully recorded.</li>
	<li>A large US food retailer wanted to interface with all his suppliers and logistics providers using industry standard <a  title="Electronic data interchange" href="http://en.wikipedia.org/wiki/Electronic_data_interchange" rel="wikipedia">EDI</a> format messages. The retailer exposed <a  title="Message queue" href="http://en.wikipedia.org/wiki/Message_queue" rel="wikipedia">MQ</a> endpoints where all the external business vendors provided data and invoices etc for capturing in the internal systems.</li>
	<li>An airline company that wished to receive information from all partners and update the booking details in its systems. Data was sent from all partners and booking agencies as EDI messages and XML messages. This was not the real time reservation system, but a pseudo real time system used for flight planning. The data would come in in periodic intervals and there would be end of day reconciliations.</li>
</ol>
I will admit that you could say that queuing email messages with messaging so that they can be sent later comes close to the cases I described above.
<h2>Messaging as a electricity or lifeline that gets stuff that you need to work or live, but not really scale the app or take load off the app</h2>
One of the managers I worked with described our work as follows - ' in the middleware group we are like the electricity, people don't notice us as long as we are there, but if the lights were to go out, we will be on the hook'. In US, Europe, Japan, Australia and many other places the lights don't go out so this analogy might sound weird - but we know very well in India that lights do go out and we lose electricity once in a while.

The main thing that he was trying to convey here is that messaging brings the data needed by all those hundreds of applications that used our platform. For most of then it was simply a method call to get data - but underneath that we went across the enterprise to different applications and databases to get all the data. And it was magically there.

This is a case where you do not realize that there is messaging. In my daily work, we move a large amount of data from various external systems that we work with and show seamlessly in a GUI - the data moves fast and we count things in milliseconds.  If you ask any of the component that gets the data exactly how it ends up on the GUI so fast, they could be excused for saying its magic.

We have a messaging layer that can get things across in milliseconds and with no loss. You could argue this is scalability, but I would argue back saying it is not the kind of application scalability  that all those blogs talk about. Here the main aim is to get a single message across the system in under a millisecond - it is not the application that is scaling, but the messaging platform that is running that fast. And yes, if on a day there were a few hundred thousand messages per minute and we managed to get them all across - then we scaled.

All the GUI does is say - I want this data - and the application makes sure that you get it as soon as it is available. GUI does not know what is happening under the covers. GUI is not scaling because of the messaging layer. You could say the task of getting data and then showing it is not distributed across two components - yes, but this is different from distributing the same task across many nodes using messaging. The GUI would be equally faster if it got the data on its own.

What we have here is a reliable high speed data distribution backbone.
<h2>Messaging does not really bring robustness in all cases</h2>
Does messaging really help in making an application robust by building lots of small components that work well? Yes - I would have to agree to this one, but partly. It depends on what you are solving - if you are solving the issue of data distribution to a large number of small components doing critical work - then yes. By building a single reliable messaging platform you have ensured that all components get data correctly and that there can be no failure in data delivery.

But, the overall application can in turn become less robust. When you build an application with a large number of small components, you are adding many moving parts. When some of these parts fall over, depending on how they fall over, the whole thing can get entangled and fail.

We all have heard cases where someone blogs about a epic failure and says - a failure in so and so system at such and such time resulted in a build up of backlogs that caused the component to slow down. This in turn caused further pile ups and brought it all to a alt. We had to do a full restart and check the data so it took so long.

The point I am trying to make here is that messaging does not add guaranteed robustness to you app - if it has a bad design then it will fail. So, care needs to be taken to ensure that your application is able to handle all the additional points of failure introduced by adding a messaging layer.
<h2>Messaging is like a glue that holds the enterprise together</h2>
If you have seen a skyscraper being built, then you will know that there is a steel frame around which the whole thing is built. You could say hundred things about why that is needed etc - but you cannot argue that without that, you cannot hold the building together and build everything else.

When you are building a large enterprise - not Facebook, Twitter, FourSquare - but maybe a bank that is spread over 100 countries, or DHL or Fedex or Amazon or a car  maker or retailer working to link their supply chain - you have to bring together systems as old as mainframes to systems as new as Ruby on Rails Web 2.0 applications built by a rock star programmer out of college and bring them into this huge workflow where data flows from the inputs, gets transformed by the enterprise into a product, goes out to the customer and brings in the return on investment.

This whole thing needs to be held together and made to work no matter how fast or cool some components are or how many times some of these components fail. And messaging gets this done - it brings in the buzz words somewhere on the way - but it keeps it together and from falling apart.
<h2>Conclusion</h2>
Messaging is not just used for scaling web apps or for making old uncool systems talk to each other - it is used to keep the business together and performing smoothly.
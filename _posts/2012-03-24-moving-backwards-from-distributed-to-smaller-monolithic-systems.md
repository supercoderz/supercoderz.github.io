---
layout: post
title: "Moving backwards from distributed to smaller monolithic systems"
date: 2012-03-24 01:39
comments: false
categories:
---

Before you decide to shoo-shoo this post and call me someone who has no idea how great <a  title="Distributed computing" href="http://en.wikipedia.org/wiki/Distributed_computing" rel="wikipedia" target="_blank">distributed systems</a> are - forget that idea. I am not talking about all the social networks or the high volume websites out there, I am not talking about the oh-so-agile and oh-so-great enterprise software that you built last night. I have worked all my life in building <a  title="Message" href="http://en.wikipedia.org/wiki/Message" rel="wikipedia" target="_blank">messaging</a> layers that facilitate distributed systems and I still swear by them. I am just going to talk about a different thing here.

<a href="http://commons.wikipedia.org/wiki/File:EAI.png" target="_blank"><img  title="Enterprise Application Integration. Schema gem..." src="http://upload.wikimedia.org/wikipedia/commons/thumb/c/c8/EAI.png/300px-EAI.png" alt="Enterprise Application Integration. Schema gem..." width="300" height="205" /></a>

So, lets take a case shall we - you have this large enterprise system dealing with live updates flowing in and out of it all through the day. You have followed all the <a  title="Service-oriented architecture" href="http://en.wikipedia.org/wiki/Service-oriented_architecture" rel="wikipedia" target="_blank">SOA</a> best patterns and isolated the components that do the boiler plate byte streaming in and and out of the platform and the components that persist, retrieve the data and apply <a  title="Business logic" href="http://en.wikipedia.org/wiki/Business_logic" rel="wikipedia" target="_blank">business logic</a>. All these talk to each other by means of the latest high speed messaging layer that beats the speed of light. Everything is messaging based and there can be no other way. In this mix, you are building a component that has to publish two business messages that need to go out and get an acknowledgement from your partner external applications - and you want an all or nothing commit on the other side. If one message fails, then both should roll back. You do not control the sequence of message once you fire them, the byte pumping components cannot have business logic.

How will you do it? It is not impossible, all you need is
<ul>
	<li>Create and fire those two messages</li>
	<li>Listen for response messages from the component that sends them out</li>
	<li>Or if that is not possible then poll and check with a delay</li>
	<li>If there is an issue with one message or the update did not happen in the time that you normally expect then fire a cancel to undo the change</li>
	<li>Listen for the response on the cancels</li>
	<li>If the cancels work, cool</li>
	<li>If the cancels fail - hmm, wait that should not happen right? Oh did it actually happen? Should I retry? Should I give up? What about integrity of the state?</li>
	<li>OK it worked in the external system but there is a delay in my own messaging layer that makes me feel that it has not worked.</li>
	<li>Oh my god! My messaging layer went down just after the first message had an error and just before I sent the cancel</li>
</ul>
OK some of these are exaggerated - but the last few items are the ones which make you wonder if you really had to distribute the damn thing so much. For get the external application, if you could do the business logic and sending in the same component, then all you would have to care for is the response from the external system. Well you could do that but would that not be the same as interlinking applications with adapters in  sphagetti mess - like plain old <a  title="Enterprise application integration" href="http://en.wikipedia.org/wiki/Enterprise_application_integration" rel="wikipedia" target="_blank">Enterprise Application Integration</a>(EAI) ?

This post is to discuss a few of these aspects, but in a different point of view.


<h2>The world before all these fancy distributed applications</h2>
When I first started development on my job, I used to build adapters. The adapter would connect to the applications, listen for changes in the application and publish out or listen for messages from other applications and do the updates in the application. I built 4 such adapters, each had its own logic, but all of them published back to the same <a  title="TIBCO Rendezvous" href="http://www.tibco.com/software/messaging/rendezvous" rel="homepage" target="_blank">TIBCO Rendezvous</a> bus. There was nothing called service, just applications that each did their own thing and published data out when they were done. Or took data, and processed it on their own sweet time. In all the productions support cases that I attended, I never saw any of our users trying to do a distributed processing based on these adapters.

In those days of legacy EAI, adapters and messaging were means to integrate data between different applications, period.

So, lets say there was some message that needed to be sent to the external application, no one tried to do a two phase commit - they selected the data, created the messages, put them somewhere where an adapter can pick them up and send them across. If there was something wrong, then they corrected it in the target application or resent the data. The aim mostly was to ensure that the messages are correct by the time they needed to go out.
<h2>Fast forward to today</h2>
I built similar applications in the last few years with all the distributed processing that is now possible because of messaging and the leverage that it provides. The volumes have increased - mostly because the billions of new customers in <a  title="Sino-Indian relations" href="http://en.wikipedia.org/wiki/Sino-Indian_relations" rel="wikipedia" target="_blank">India and China</a> - and every business wants to do huge volumes. So the service that takes input and provides data for the business message wants to take only input and process it very fats not bothering about everything else. So it takes the input and publishes it to a messaging layer. It will look back for a response in a few hundred milliseconds and wants an answer - so the component that validates the data subscribes to this data and runs through it very quickly. This validation service also needs to be do volume, so it republishes it again for another service which processes a bit more and republishes again and finally the data makes it back to the first service to show the user some response.

This works very fast, every day, with lots of data flowing between different office hundreds and thousands of miles apart. They are even trying to bring London and Tokyo closer by 60 milliseconds by running fiber optic cables through the polar route.

But since all this copper and fiber is shared, when someone else is doing higher volumes and has more traffic or when the <a  title="Server (computing)" href="http://en.wikipedia.org/wiki/Server_%28computing%29" rel="wikipedia" target="_blank">servers</a> and routers are loaded, there will be delays. The way ethernet is built, if there is more traffic and more data packet collisions then there will be more delays.

And then some days out of pure bad luck something will fail. Not the whole thing but some part of it and will cause ripple effects through the rest of it all.

This would happen in the old days too, but the application was either running or not - and since it was solely responsible for all these different operations, the whole thing would die. But today, only parts of the flow will die and the whole process will limp on for a while, eventually recovering or dying a slow painful death.

Instantaneous death is less painful.
<h2>So whats your intelligent idea of solving all this using monolithic systems?</h2>
It isn't much of a great idea, but here it is
<ul>
	<li>Although there are various parts of your application that can be built as distributed services and re-used, not all of them really need to be re-used. You can duplicate the same functionality, probably using the same code, in different components.</li>
	<li>All the operations cluster into logical groups - each group can be done independently of the other group to provide entire result. If lets say a particular group of operations need to be done for a million records, you can run 4 instances of that group and do 250K in each, and eventually sync everything up with all 4 instances. These 4 instances can be 4 small monolithic applications running independently, not impacted by failures everywhere else.</li>
	<li>These mini monolithic applications can be further grouped together by the data that they share. Instead of having them share data over messaging, they can read write from a single database - <a  title="SQL" href="http://www.iso.org/iso/catalogue_detail.htm?csnumber=45498" rel="homepage" target="_blank">SQL</a> or <a  title="NoSQL" href="http://en.wikipedia.org/wiki/NoSQL" rel="wikipedia" target="_blank">NoSQL</a> - and always see a consistent picture. If you want to know something as worked or not, just look at the DB. If you want to prevent a record that was erroneously created from being processed, then instead of firing a message and waiting to know the result, you lock it to prevent being read and delete it.</li>
	<li>These databases can then be kept in sync using some means- even messaging.</li>
</ul>
<h2>Hey! hang on - that is the same thing as distributed applications! Its not monolithic and nothing new at all!</h2>
Yes - it isn't anything new. But it is a different point of view.

I am saying do not be ashamed of duplicating functionality, don't duplicate code, but if functionality is same then duplicate it. Also if it makes sense to do everything in one application, do it and build a smaller monolithic application.

By breaking down the large monolith into too many distributed moving parts, we are making life difficult. By breaking it into a small number of mini monoliths we can still achieve the advantages of distributed applications, but we will also have smaller number of  moving parts. We will have smaller number of parts to fix if there was an issue and since related logic is grouped into a single monolithic application, you can rest assured that as long as you keep the format of data that is shared a constant, each application is free to do whatever it wants to do and however it pleases as long as it gets the required output data.

Taking the example I gave at the beginning, you could build a mini application that is both responsible for processing and sending out the data. Then, you have to worry about few less components in the scenario that I gave - you just have to deal with whether the update in the external application worked or not - everything else you control.
<h2>Conclusion</h2>
Not much to conclude here - we had mainframes which were slow, we moved to high speed very distributed applications. But these have too many moving parts which fail if not properly managed. What we need to achieve is something in between where we have a smaller number of mini applications that co-operate with each other to get the same speed and not be like the mainframes.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://supercoderz.in/2012/01/29/why-do-you-use-messagingmiddleware/" target="_blank">Why do you use Messaging/Middleware?</a> (supercoderz.in)</li>
	<li class="zemanta-article-ul-li"><a href="http://java.dzone.com/articles/getting-soa-right-%E2%80%93-thinking" target="_blank">Getting SOA Right - Thinking Beyond 'The Right Angles'</a> (java.dzone.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://teknotrendz.wordpress.com/2011/10/02/application-design-considerations-for-eai/" target="_blank">Application Design Considerations for EAI</a> (teknotrendz.wordpress.com)</li>
</ul>
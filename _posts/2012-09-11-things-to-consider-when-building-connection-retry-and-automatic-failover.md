---
layout: post
title: "Things to consider when building connection retry and automatic failover"
date: 2012-09-11 23:10
comments: false
categories:
---

Have you ever been in a situation where a connection to a resource was lost and your application either did not tell you about it, or did not try to reconnect or ends up in a mess trying to reconnect? How does that feel? You might have wondered that it would be great to have something that can automatically recover so that you don't need to intervene, or even avoid any manual recovery work that comes after a restart to recover from these issues.

<a href="http://commons.wikipedia.org/wiki/File:HTTP_pipelining.svg" target="_blank"><img  title="English: HTTP pipelining, client-server connec..." src="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fb/HTTP_pipelining.svg/300px-HTTP_pipelining.svg.png" alt="English: HTTP pipelining, client-server connec..." width="300" height="187" /></a> English: HTTP pipelining, client-server connection schema (Photo credit: Wikipedia)

This is a fairly common issue and it can be fixed - but to fix it you need to consider the exact scenario where it is happening and any specific requirements specific to your application or a resource. This post is an attempt to understand all these considerations.


<h2>So when do you lose a connection?</h2>
For the sake of this post, we will consider connections to network resources - things like <a  title="Database" href="http://en.wikipedia.org/wiki/Database" rel="wikipedia" target="_blank">databases</a>, web servers, message queues, <a  title="Secure Shell" href="http://en.wikipedia.org/wiki/Secure_Shell" rel="wikipedia" target="_blank">SSH</a> connections etc. It doesn't matter which one of these it is because at the core of it it is a connection over a socket using some form of the TCP/IP protocol. <a  title="User Datagram Protocol" href="http://en.wikipedia.org/wiki/User_Datagram_Protocol" rel="wikipedia" target="_blank">UDP</a> is connection less so doesn't really matter here.

These connections are lost when one of the following occurs -
<ol>
	<li>The physical network connection is damaged</li>
	<li>The application on the other end of the connection dies - like <a  title="Server (computing)" href="http://en.wikipedia.org/wiki/Server_%28computing%29" rel="wikipedia" target="_blank">server</a> crash or database crash</li>
	<li>The application disconnects because of an error or load</li>
</ol>
Obviously we will not consider the case where your application dies - that is another story called application recovery rather than connection retry.

#1 is quickly rules out from retry because if the physical connection is lost, and there is no redundant route, then you cannot get back until the link is restored. Even if we try to reconnect here, it is likely to be a long time before we actually succeed.

Lets take cases 2 and 3 - these are cases where you can assume that as a <a  title="Client (computing)" href="http://en.wikipedia.org/wiki/Client_%28computing%29" rel="wikipedia" target="_blank">client</a> of the application, you can try to reconnect to the application and you should be able to connect back again. And these are the cases where we talk about automatic <a  title="Failover" href="http://en.wikipedia.org/wiki/Failover" rel="wikipedia" target="_blank">fail over</a>.
<h2>Understanding connection retry and automatic fail over</h2>
These are two different things - connection retry is when we try to connect again to the same IP and port; automatic fail over is where we have multiple secondary connections and we can try connecting to these when the primary one is not reachable. Both work in a similar way that we try to connect again, but there are subtle differences.

In connection retry we will most likely see parameters like max number of retries and interval between retries. These are important because once an application loses a connection to lets say a database, if the database killed the connection for an error then it has to recover from the error and cleanly close the state associated with this connection. Once it has done this, it will be able to accept any new connections again. If the database was down, then it has to come up and recover before it can accept connections. In this context the interval between retries determines the time that we feel is reasonable enough time for the application to have recovered so that we can make another attempt. This can also include any time that we need to close any state on our end before we reconnect. Since we might or might not want to indefinitely attempt to reconnect - in the worst case it might be wiser to just throw an exception and raise an alert to someone to look at the issue - the max retries will help determine how many attempts we make.

If you talk about automatic fail over, we talk about secondary connections. Most business critical applications have multiple servers and will allow connections to either of them. Some applications manage the allocation to the servers automatically with a scheduling algorithm while others explicitly give multiple addresses. If the application manages the fail over then we just retry to the same address like above and it will work, unless the physical box is down. Otherwise we need to loop though all possible secondary <a  title="IP address" href="http://en.wikipedia.org/wiki/IP_address" rel="wikipedia" target="_blank">IP addresses</a> and connect to one that is available. This fail over to secondary connections is what is usually automated and is called automatic fail over.

One variation is where the application starts a connection retry and upon a signal, like a <a  title="Java Management Extensions" href="http://en.wikipedia.org/wiki/Java_Management_Extensions" rel="wikipedia" target="_blank">JMX</a> method, performs a fail over.

So that's it? No, here comes the messy part.
<h2><a  title="State (computer science)" href="http://en.wikipedia.org/wiki/State_%28computer_science%29" rel="wikipedia" target="_blank">Application state</a></h2>
When applications run , they have state. When we have a connection , we have things like sequence numbers for messages sent, acknowledgements, transaction states in the database etc. All these need to be managed when a connection is made and when a connection is closed. If it was a proper shut down then the applications usually have all this logic and bring things down closely. But when we talk about connection retry, it usually means that connection was lost in flight. And this means clean up.

Both on the client and the server, the state can be maintained in various ways. Since the client is one process, we can assume that the state is in memory and will remain until the application is shut down. On the server side, it is simple if it is just one process or just one server. If there are multiple servers, then the state needs to be shared and replicated between all these. Either way the server will need to maintain a state repository where it can update the state and in case of a connection failure it can mark it saying a connection was lost at this point and can recover from here, or has to start from zero again. If it has to start from zero then there might be a need to send and receive message again.

From a client side, if the server expects you to recover from where you left off, it is easy to reconnect without much hassle. However, if the client expects you to resend some data or clear and start from the beginning, then the client has to have logic to cleanly close the state before it can start again.

All this is not difficult, the only problem is that depending on how your application was built and the assumptions that you made, you might not have factored in all these.
<h2>You failed over - fine - but the primary will never come back up anytime soon</h2>
When an application is running, if there was a secondary connection, then the application can easily fail over to that. The reasonable assumption is that the primary will be back up very soon. But in some cases it so happens that the primary will take a long time to recover - days or even weeks. This exposes another loophole - applications need to restart.

All applications need to restart once in a while. When they do, they lose memory of the state they had when running. So they need to make sure they remember. If your application changed connections while running and the main connection will not be available when it restarts, then it needs to record that somewhere.

If you grab your configuration from a database, then it is fairly easy to update it when you retry. But if you read off an <a  title="XML" href="http://en.wikipedia.org/wiki/XML" rel="wikipedia" target="_blank">XML</a> file? You need a way to update the XML file or create an override file. This can get messy.

The other approach is to build the application in a way that at start up, it tries all connections until it gets connected - not just the primary.
<h2>Better ways to manage secondary connections transparently</h2>
There are better ways than trying all connections at start up if you want to keep things transparent and make it a simple case of connection retry for the client and manage fail over transparent. Taking these approaches will make managing your applications easy.

These approaches mean that instead of connecting on your own, you get a connection from a connection registry - for example JNDI, or DSN. You could even get connections from an LDAP server.

What all these do is provide a component in between you and the network application to manage connections. This component is responsible for making the connection and keeping a pool of connections to the one or many servers. When an application requests a connection, it passes one of the available connection to any of the working servers. If the client application loses the connection, then it retries and gets a new working connection from this component without having to really know which secondary IP to use etc.
<h2>Conclusion</h2>
These are just some of the things that you need to consider when trying to build a connection retry or fail over logic in your application. What works for you depends on your exact architecture and needs.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://java.dzone.com/articles/new-activemq-failover-and" target="_blank">New ActiveMQ failover and Clustering Goodies</a> (java.dzone.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://hazima.wordpress.com/2012/09/04/an-introduction-to-sql-server-clusters/" target="_blank">An Introduction to SQL Server Clusters</a> (hazima.wordpress.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://hazima.wordpress.com/2012/09/04/high-availability-cluster/" target="_blank">High-availability cluster</a> (hazima.wordpress.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.rackspace.com/knowledge_center/article/ip-failover-high-availability-explained" target="_blank">IP Failover - High Availability Explained | Knowledge Center | Rackspace Hosting</a> (rackspace.com)</li>
</ul>
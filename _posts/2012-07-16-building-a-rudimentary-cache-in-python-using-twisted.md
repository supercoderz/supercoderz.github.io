---
layout: post
title: "Building a rudimentary cache in Python using Twisted"
date: 2012-07-16 22:36
comments: false
categories:
---

Every now and then I try and see if I can take something that I built in <a class="zem_slink" title="Java (programming language)" href="http://www.oracle.com/technetwork/java/" rel="homepage" target="_blank">Java</a> and build it in <a class="zem_slink" title="Python (programming language)" href="http://www.python.org/" rel="homepage" target="_blank">Python</a>. After having thought of doing a socket application in Python using the <a class="zem_slink" title="Twisted (software)" href="http://twistedmatrix.com" rel="homepage" target="_blank">Twisted</a> framework, something other than the basic echo. Finally I did it today, by building a simple rudimentary <a class="zem_slink" title="Cache" href="http://en.wikipedia.org/wiki/Cache" rel="wikipedia" target="_blank">cache</a> with just PUT and GET commands, not even a delete. I admit it is lazy of me, but this illustrates the simplicity and this can be easily built on top of.

So what do you need? You need Python, I used version 2.7, and the Twisted framework - <a href="http://twistedmatrix.com/trac/">http://twistedmatrix.com/trac/</a>.

Once you have these two - read on.

<!--more-->
<h2>Basics of twisted - protocol</h2>
When you start writing sockets in Twisted, you need to have a protocol that will handle the data that comes from the <a class="zem_slink" title="Client (computing)" href="http://en.wikipedia.org/wiki/Client_%28computing%29" rel="wikipedia" target="_blank">client</a>. Twisted provides implementations of the most popular <a class="zem_slink" title="Communications protocol" href="http://en.wikipedia.org/wiki/Communications_protocol" rel="wikipedia" target="_blank">protocols</a>, and you can build on top of these or using the base protocol class. Once you have a protocol, it is recommended that you build a protocol factory that can be used to instantiate the protocol. This also is very simple. All this can be achieved in very very few <a class="zem_slink" title="Source lines of code" href="http://en.wikipedia.org/wiki/Source_lines_of_code" rel="wikipedia" target="_blank">lines of code</a> - I found that if I used something like Netty or Mina in Java, then I would write far more lines of code to get this done and doing it in Python is a breeze. The screenshot below shows all the code required to create the basic cache protocol that I built. More on why I built it using a <a class="zem_slink" title="DICT" href="http://en.wikipedia.org/wiki/DICT" rel="wikipedia" target="_blank">dict</a>, but this is pretty much it.
<p style="text-align:center;"><a href="http://supercoderz.files.wordpress.com/2012/07/protocol.png"><img class="aligncenter size-medium wp-image-368" title="protocol" src="http://supercoderz.files.wordpress.com/2012/07/protocol.png?w=300" alt="" width="300" height="256" /></a></p>

<h2 style="text-align:left;">Starting a <a class="zem_slink" title="Server (computing)" href="http://en.wikipedia.org/wiki/Server_%28computing%29" rel="wikipedia" target="_blank">server</a> using this protocol</h2>
There are many ways to start a server in Twisted, you can use a reactor to start a simple server that does not get daemonized, or you can use the twistd command to start a server using what is called the <a class="zem_slink" title="Application Layer" href="http://en.wikipedia.org/wiki/Application_Layer" rel="wikipedia" target="_blank">Application</a> object - by doing so Twisted will take care of starting the server, logging and also running it as a daemon. In order to do this though you need to write a .tac file which is just another python script. This is how it looks -

<a href="http://supercoderz.files.wordpress.com/2012/07/tac.png"><img class="aligncenter size-medium wp-image-371" title="tac" src="http://supercoderz.files.wordpress.com/2012/07/tac.png?w=300" alt="" width="300" height="256" /></a>

Once you have this, you can start the server using the command 'twistd -noy server.tac' and it will start up the server waiting for the requests.

<a href="http://supercoderz.files.wordpress.com/2012/07/running-server.png"><img class="aligncenter size-medium wp-image-372" title="running server" src="http://supercoderz.files.wordpress.com/2012/07/running-server.png?w=300" alt="" width="300" height="256" /></a>
<h2>A few words about the cache before we go to the client</h2>
As you can see the cache itself is a very trivial implementation with just a put and get functionality, and does not do any checking etc. This is just to illustrate the Twisted framework, and you can easily add more functionality. The cache uses a global dict to store the values - since keys in a dict are unique, this will ensure that we have only one value in the cache. Also dict has a lookup time of O(1) for any number of dict entries - this means that even when the cache has a million records, it will retrieve the results equally fast.
<h2>And now the client before we debate if this code has any real life use in the current state</h2>
The below two screenshots which are pretty self explanatory show how we can write client code and how we can use the server. It also shows the server logs with cache values.

<a href="http://supercoderz.files.wordpress.com/2012/07/client.png"><img class="aligncenter size-medium wp-image-373" title="client" src="http://supercoderz.files.wordpress.com/2012/07/client.png?w=300" alt="" width="300" height="195" /></a>

<a href="http://supercoderz.files.wordpress.com/2012/07/client-result.png"><img class="aligncenter size-medium wp-image-374" title="client result" src="http://supercoderz.files.wordpress.com/2012/07/client-result.png?w=300" alt="" width="300" height="195" /></a>

<a href="http://supercoderz.files.wordpress.com/2012/07/server-logs.png"><img class="aligncenter size-medium wp-image-375" title="server logs" src="http://supercoderz.files.wordpress.com/2012/07/server-logs.png?w=300" alt="" width="300" height="256" /></a>
<h2>Conclusion</h2>
This is very very very rudimentary code and does not have any application in any large enterprise system where there is need for reliability security etc. It cannot be used without heavy changes to add a lot of missing functionality. That said the code is not useless - it can be used to learn and also in small applications or testing where someone wants to mock a cache or whip up a simple cache on is own, this  can be used to do some ground work.
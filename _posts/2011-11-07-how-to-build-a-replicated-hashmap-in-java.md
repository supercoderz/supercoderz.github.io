---
layout: post
title: "How to build a replicated HashMap in Java"
date: 2011-11-07 23:03
comments: false
categories:
---

We all have heard about clustered <a  title="Application server" href="http://en.wikipedia.org/wiki/Application_server" rel="wikipedia">application servers</a> and databases and how they can replicate data between instances. We have heard about distributed cache implementations like <a  title="EHcache" href="http://ehcache.org/" rel="homepage">Ehcache</a>, Hazelcast etc and even NOSQL databases like <a  title="MongoDB" href="http://www.mongodb.org/" rel="homepage">MongoDB</a> that replicate data between all the instances. They do a great job of replicating data efficiently and ensuring <a  title="Data integrity" href="http://en.wikipedia.org/wiki/Data_integrity" rel="wikipedia">data integrity</a>. They provide different guarantees on <a  title="data replication" href="http://www.symantec.com/business/volume-replicator" rel="symantec">data replication</a> and availability.

Is it all black magic that is too hard to comprehend? No - its just simple logic built into the entities that store the data.

In this post I will describe how you can build a simple replicating <a  title="Hash table" href="http://en.wikipedia.org/wiki/Hash_table" rel="wikipedia">HashMap</a> in Java. I will build this replication using few lines of code in Netty - the easy to use NIO library from <a  title="JBoss application server" href="http://www.jboss.com/products/platforms/application/" rel="homepage">JBoss</a>.
<h3><!--more-->So what do we want to do?</h3>
We want to build a replicating HashMap - as in we create an instance of the map, add data to it and the data is magically transported across the ether to another HashMap running in a different JVM!
<h3>Wow! What high end tools do we need for this?</h3>
We don't need much - just the usual Java and Netty NIO framework. We will use basic Java ConcurrentHashMap and HashMap classes for data storage and Netty to build the back end communication. We could have built the back end communication in any manner - <a  title="Java Management Extensions" href="http://en.wikipedia.org/wiki/Java_Management_Extensions" rel="wikipedia">JMX</a>, Database or sockets. Netty provides an easy to use approach to build this using plain old sockets.
<h3><strong>The design aspects</strong></h3>
For the purpose of this example, we will replicate add and delete operations. In order to represent the operation being performed on the map we will need an entity. Lets call that Event. This has to be serializable because it has to be sent over the socket. This will have 3 fields - the operation, they key and the value of the entry.

Once we have this, we will implement a Map and override its add and remove methods so that it can send the data over a socket. This will be achieved by creating a Netty client in the Map. This client will connect to the Server and send the event for these two operations.

The server will simply listen to these messages and add or remove data from a DataCache. For the sake of proving that data can flow both ways, the server will echo back the event to the client. On the client side, the event is used to add or remove data from another DataCache. Data from both the caches is printed out for observing the results.

Lets now look at the code for all these - you will be surprised how less code we need.
<h3>The Event</h3>
<pre style="padding-left:30px;">package com.supercoderz.clusteredmap.event;

import java.io.Serializable;

public class Event implements Serializable {
	private static final long serialVersionUID = 1L;

	public enum Operation {
		ADD, DELETE
	}

	public Operation op;
	public String key;
	public Object value;

}</pre>
<h3>The ServerHandler</h3>
<pre style="padding-left:30px;">package com.supercoderz.clusteredmap.server;

import org.jboss.netty.channel.ChannelHandlerContext;
import org.jboss.netty.channel.ExceptionEvent;
import org.jboss.netty.channel.MessageEvent;
import org.jboss.netty.channel.SimpleChannelUpstreamHandler;

import com.supercoderz.clusteredmap.event.Event;
import com.supercoderz.data.DataCache;

public class ServerHandler extends SimpleChannelUpstreamHandler {

	<a  title="Annotation" href="http://en.wikipedia.org/wiki/Annotation" rel="wikipedia">@Override</a>
	public void messageReceived(ChannelHandlerContext ctx, MessageEvent e)
			throws Exception {
		Object o=e.getMessage();
		if(o instanceof Event){
			if (((Event) o).op.equals(Event.Operation.ADD)) {
				DataCache.cache.put(((Event) o).key, ((Event) o).value);
			} else if (((Event) o).op.equals(Event.Operation.DELETE)) {
				DataCache.cache.remove(((Event) o).key);
			}
		}
		System.out.println("-------------------------------");
		System.out.println(DataCache.cache);
		System.out.println("-------------------------------");
		ctx.getChannel().write(o);
	}

	@Override
	public void exceptionCaught(ChannelHandlerContext ctx, ExceptionEvent e)
			throws Exception {
	}
}</pre>
<h3>The Server</h3>
<pre style="padding-left:30px;">package com.supercoderz.clusteredmap.server;

import java.net.InetSocketAddress;
import java.util.concurrent.Executors;

import org.jboss.netty.bootstrap.ServerBootstrap;
import org.jboss.netty.channel.ChannelPipeline;
import org.jboss.netty.channel.ChannelPipelineFactory;
import org.jboss.netty.channel.Channels;
import org.jboss.netty.channel.socket.nio.NioServerSocketChannelFactory;
import org.jboss.netty.handler.codec.serialization.ObjectDecoder;
import org.jboss.netty.handler.codec.serialization.ObjectEncoder;

public class Server {
	public static void main(String[] args) {
		ServerBootstrap bootstrap = new ServerBootstrap(
				new NioServerSocketChannelFactory(
						Executors.newCachedThreadPool(),
						Executors.newCachedThreadPool()));

		// Set up the pipeline factory.
		bootstrap.setPipelineFactory(new ChannelPipelineFactory() {
			public ChannelPipeline getPipeline() throws Exception {
				return Channels.pipeline(new ObjectEncoder(),
						new ObjectDecoder(), new ServerHandler());
			}
		});
		bootstrap.bind(new InetSocketAddress(1111));
	}

}</pre>
<h3>The ReplicatingMap</h3>
<pre style="padding-left:30px;">package com.supercoderz.clusteredmap.map;

import java.net.InetSocketAddress;
import java.util.HashMap;
import java.util.concurrent.Executors;

import org.jboss.netty.bootstrap.ClientBootstrap;
import org.jboss.netty.channel.ChannelFuture;
import org.jboss.netty.channel.ChannelPipeline;
import org.jboss.netty.channel.ChannelPipelineFactory;
import org.jboss.netty.channel.Channels;
import org.jboss.netty.channel.socket.nio.NioClientSocketChannelFactory;
import org.jboss.netty.handler.codec.serialization.ObjectDecoder;
import org.jboss.netty.handler.codec.serialization.ObjectEncoder;

import com.supercoderz.clusteredmap.client.ClientHandler;
import com.supercoderz.clusteredmap.event.Event;

public class ReplicatingMap extends HashMap&lt;String, Object&gt; {

	private static final long serialVersionUID = 1L;
	private ClientBootstrap bootstrap;
	private ChannelFuture future;

	public ReplicatingMap(String hostname, int port)
			throws InterruptedException {
		bootstrap = new ClientBootstrap(new NioClientSocketChannelFactory(
				Executors.newCachedThreadPool(),
				Executors.newCachedThreadPool()));
		bootstrap.setPipelineFactory(new ChannelPipelineFactory() {
			public ChannelPipeline getPipeline() throws Exception {
				return Channels.pipeline(new ObjectEncoder(),
						new ObjectDecoder(), new ClientHandler());
			}
		});
		future = bootstrap.connect(new InetSocketAddress(hostname, port));
		future.await();
	}

	@Override
	public Object put(String key, Object value) {
		Event e = new Event();
		e.op = Event.Operation.ADD;
		e.key = key;
		e.value = value;
		future.getChannel().write(e);
		return super.put(key, value);
	}

	@Override
	public Object remove(Object key) {
		Event e = new Event();
		e.op = Event.Operation.DELETE;
		e.key = (String) key;
		future.getChannel().write(e);
		return super.remove(key);
	}

}</pre>
<h3>The ClientHandler to handle data echoed from Server</h3>
<pre style="padding-left:30px;">package com.supercoderz.clusteredmap.client;

import org.jboss.netty.channel.ChannelHandlerContext;
import org.jboss.netty.channel.ExceptionEvent;
import org.jboss.netty.channel.MessageEvent;
import org.jboss.netty.channel.SimpleChannelUpstreamHandler;

import com.supercoderz.clusteredmap.event.Event;
import com.supercoderz.data.DataCache;

public class ClientHandler extends SimpleChannelUpstreamHandler {
	@Override
	public void messageReceived(ChannelHandlerContext ctx, MessageEvent e)
			throws Exception {
		Object o = e.getMessage();
		if (o instanceof Event) {
			if (((Event) o).op.equals(Event.Operation.ADD)) {
				DataCache.cache.put(((Event) o).key, ((Event) o).value);
			} else if (((Event) o).op.equals(Event.Operation.DELETE)) {
				DataCache.cache.remove(((Event) o).key);
			}
		}
		System.out.println("-------------------------------");
		System.out.println(DataCache.cache);
		System.out.println("-------------------------------");
	}

	@Override
	public void exceptionCaught(ChannelHandlerContext ctx, ExceptionEvent e)
			throws Exception {
	}
}</pre>
<h3>The DataCache</h3>
<p style="padding-left:30px;"><span class="Apple-style-span" style="font-family:Consolas, Monaco, monospace;font-size:12px;line-height:18px;white-space:pre;">package com.supercoderz.data;</span></p>

<pre style="padding-left:30px;">import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

public class DataCache {
	public static Map&lt;String, Object&gt; cache = new ConcurrentHashMap&lt;String, Object&gt;();
}</pre>
<h3>The Client</h3>
<pre style="padding-left:30px;">package com.supercoderz.clusteredmap.client;

import com.supercoderz.clusteredmap.map.ReplicatingMap;

public class Client {
	public static void main(String[] args) throws InterruptedException {
		ReplicatingMap map = new ReplicatingMap("localhost", 1111);
		map.put("1", 1);
		map.put("2", 2);
		map.put("3", 3);
		map.put("4", 4);
		map.remove("4");
	}

}</pre>
<h3>Usage,Analysis and Conclusion</h3>
If you run the Server and Client classes in two command prompts, then you can see that the data that gets added in the client is replicated over in the Server. The data received in the Server is echoed back to the client and is replicated again. The ReplicatedMap is just sending data to the server which flows back to the client. This flow is setup to prove that data flows both ways - in reality you will probably route the data differenly with the ReplicatingMap sometimes sending data to multiple servers or maybe ReplicatingMap acting as a server that accepts connections and responds to all clients with data that is added to it.

Once this basic flow of data is setup its really up to you which way and how many connections or replications you want to add. Its easy to add logic on top that divides and splits the data to replicate it, checks for duplicates etc etc.

The amount of code that was actually written to get the replication working is very less  - you do see a lot of verbose Java code but that is Java - the main lines that get replication to work are really simple.

So next time someone talks about replication as being some form of higher level black magic, you know how it works under the wraps!
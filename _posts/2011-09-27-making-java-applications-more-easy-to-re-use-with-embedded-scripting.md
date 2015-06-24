---
layout: post
title: "Making Java applications more easy to re-use with embedded scripting"
date: 2011-09-27 23:58
comments: false
categories:
---

Lets admit it - writing an application in Java takes a lot more code lines and configuration than in other languages like <a class="zem_slink" title="Python (programming language)" href="http://www.python.org/" rel="homepage">Python</a> or Ruby. However, Java has been around for many years and it will stay around for many years simply because of the number of applications in various enterprises built around Java. However, of late with the new kid's on the block like Rails, Django and all these rapid development paradigms that are out there, poor Java programmers do feel left out. Of course we have Groovy that is borne out of Java and a couple of such options, we do have to stick to Java and the different Java frameworks like Spring, Hibernate etc for our day jobs.

&nbsp;

There are different ways in which you can build a <a class="zem_slink" title="Java (software platform)" href="http://www.java.com" rel="homepage">Java application</a> correctly using the age old known best practices and get the rapid development and agility that you want. In this blog I will describe a probably known but less frequently used way to add more agility to your Java applications. This could be used for web or non-web Java applications alike.
<h3><!--more-->So whats the silver bullet?</h3>
&nbsp;

Its not really a silver bullet, and it has been around for a while - its the <a class="zem_slink" title="Scripting for the Java Platform" href="http://en.wikipedia.org/wiki/Scripting_for_the_Java_Platform" rel="wikipedia">JSR-223</a> Java Scripting extension. This part of Java, available under the javax.script package enables developers to embed a scripting language of their choice in the Java application and let it execute scripts. These scripts can be user entered or maybe loaded from a file or socket.

The earliest I remember seeing something like this was in the JMS monitoring tool Hermes JMS. We used that to monitor and manage our TIBCO JMS servers and this provided a small embedded Jython console that let us interact with the application. I probably saw another application in one of old companies using something like this add interactivity and evalute user entered expressions. But that's it - nothing major.
<h3>How will we use this to make applications more easy to re-use?</h3>
&nbsp;

Before we get into how, lets spend some time revising some of the enterprise Java application use cases and see how we can apply this there. Being a messaging and connectivity guy, one of the most common problems that I have solved is getting a message from one application to the other, or from the messaging layer to the application and vice versa. This always involves only the following steps:
<ul>
	<li>Connect to the source system</li>
	<li>Get some data, parse and translate it</li>
	<li>Push it to the target system.</li>
</ul>
<div>There are only n number of source system types and another m number of target system types. If you account for common interface and <a class="zem_slink" title="Application programming interface" href="http://en.wikipedia.org/wiki/Application_programming_interface" rel="wikipedia">API's</a> then these are small numbers and there can be only so many combinations between these. I have almost always had the situation where I use the same type of source connectivity to the application and to the target messaging layer and push data on to the queues or topics. In each case, what differs is the data processing.</div>
<div>So my Java project ends up with about 10 classes for the common connectivity and one class for each type of these transformations. After becoming a Spring convert, I now have a set of spring configuration files and a <a class="zem_slink" title="Common Interface" href="http://en.wikipedia.org/wiki/Common_Interface" rel="wikipedia">common Interface</a> for all the classes that deal with my data processing.</div>
<div>If you think about it, there is only one part that varies. Each time a new application gets added, I write another class, rebuild the jar and configurations and restart the thing. Done.</div>
<h3>Thinking again with a more clear mind</h3>
<div>So what I do each time is</div>
<div>
<ul>
	<li>Write a new class to process data from known connections</li>
	<li>Edit spring configuration to add the new class as a bean, and reference it</li>
	<li>Re-build and deploy.</li>
</ul>
<div>Now, don't you think if I could just externalize the data processing logic into the configuration, then I don't need to touch the code at all? This would also mean less chance of breaking something. And less regression. And faster turn around. And more time to concentrate on tougher problems. More brownie points.</div>
</div>
<h3>So how do we bite the bullet?</h3>
&nbsp;
<div>Take a look at the code listing at the end of this blog - its a large blurb of code but I felt it made more sense to embed it rather than link to a source control. This code shows a class called Scriptable which creates  a <a class="zem_slink" title="Scripting language" href="http://en.wikipedia.org/wiki/Scripting_language" rel="wikipedia">Script</a> engine, default Jython (Java Python), and executes code sent to it using binding variables if any. This class is written and battle tested to do one thing and do it well - take a piece of code written in a scripting language and run it. As long as the script is tested, no problems. But if the script fails or has errors - no problem, just report the error. This kind of code is easy to write and test and will not fail.</div>
<div>The magic lies in how you use it. Lets take one deployment scenario and walk through it.</div>
<div>
<ul>
	<li>Your assignment is to connect to a database and execute a query on some data and publish that to your messaging layer.</li>
	<li>In future, you will connect to this database again and again and will run more queries and publish the data.</li>
	<li>Because the data is different and to help performance, each of these queries will be run as  a different batch or polling application.</li>
	<li>It will be the same type of database - Sybase, <a class="zem_slink" title="MySQL" href="http://www.mysql.com" rel="homepage">MySQL</a> etc and same messaging layer</li>
</ul>
<div>And this is how we solve it using our class given below.</div>
<div>
<ul>
	<li>Write the database connectivity and the messaging connectivity code</li>
	<li>When integrating these two, create an instance of the Scriptable class and pass it two binding variables - database connection and messaging connection.</li>
	<li>Write a small script in Python or Groovy or whatever you prefer and in that script write the logic to execute the query and process results.</li>
	<li>You might as well <a class="zem_slink" title="Hard coding" href="http://en.wikipedia.org/wiki/Hard_coding" rel="wikipedia">hard code</a> the query in there.</li>
	<li>You can pull and push data using the connection variables.</li>
	<li>Write the main code in such a way that it takes the script from somewhere and passes to the engine with the two connection variables.</li>
	<li>When creating the application configuration in <a class="zem_slink" title="XML" href="http://en.wikipedia.org/wiki/XML" rel="wikipedia">XML</a>, put this script in the XML and have the application load it and execute it.</li>
	<li>If not using XML config, put the script in a file and pass it as a parameter - whatever suits your deployment.</li>
	<li>When the application is run, it will take the script and do the magic.</li>
	<li>All this takes about a day or two to test thoroughly.</li>
	<li>Job done.</li>
</ul>
<div>Now a week later you are told to start publishing another query result - what do you do?</div>
<div>Write a script to do the processing, copy the deployment of the last service, replace that old script with the new one and deploy the application. The connectivity and the framework already work - the new script is tested - and unless your luck is really bad you are done in about 2 hours.</div>
</div>
<div>See, re-use wasn't so difficult or a pain!</div>
</div>
<h3>Does this really work or is it performance killer?</h3>
<div>Trust me, I have been using this for a year doing many many messages per second. It works.</div>
<h3>Code Listing</h3>
<h4></h4>
<h4>Scriptable.java</h4>
<pre style="padding-left:30px;">package com.supercoderz;

import java.util.Map;

import javax.script.Bindings;
import javax.script.ScriptContext;
import javax.script.ScriptEngine;
import javax.script.ScriptEngineManager;
import javax.script.ScriptException;

public class Scriptable {

	// the script engine that will be used to execute arbitrary
	// code configured to be executed in the application
	private ScriptEngine engine;

	// the scripting language - default Python
	private String scriptingLanguage = "python";

	public Scriptable() {
		ScriptEngineManager manager = new ScriptEngineManager();
		engine = manager.getEngineByName(scriptingLanguage);
	}

	public Scriptable(String scriptingLanguage) {
		this.scriptingLanguage = scriptingLanguage;
		ScriptEngineManager manager = new ScriptEngineManager();
		engine = manager.getEngineByName(scriptingLanguage);
	}

	/**
	 * Execute the given script and return the output (if any)
	 *
	 * @param script
	 *            The script to execute
	 * @return The result returned by the script - usually the value of the last
	 *         expression evaluated by the script.
	 * @throws ScriptException
	 *             In case of error in the script execution
	 */
	public Object executeScript(String script) throws ScriptException {
		return engine.eval(script);
	}

	/**
	 * Execute the given script using the given binding variables in the engine
	 * context. These variables will be available to the script.
	 *
	 * @param script
	 *            The script to execute
	 * @param bindingVariables
	 *            The variable names and the data to be assigned to them in the
	 *            script engine
	 * @return The result of the script execution
	 * @throws ScriptException
	 *             In case of error
	 */
	public Object executeScript(String script,
			Map&lt;String, Object&gt; bindingVariables) throws ScriptException {
		// create the bindings
		Bindings bindings = engine.createBindings();
		bindings.putAll(bindingVariables);
		// and set them at engine scope - only available to the scripts
		engine.setBindings(bindings, ScriptContext.ENGINE_SCOPE);
		// now delegate execution
		return executeScript(script);
	}

	// Setter Getter methods to allow altering the scripting language to
	// something
	// like Groovy or JRuby or JavaScript

	public String getScriptingLanguage() {
		return scriptingLanguage;
	}

	public void setScriptingLanguage(String scriptingLanguage) {
		this.scriptingLanguage = scriptingLanguage;
	}

}</pre>
<h4></h4>
<h4>ScriptableTest.java</h4>
&nbsp;
<pre style="padding-left:30px;">package com.supercoderz;

import static org.junit.Assert.assertNotNull;
import static org.junit.Assert.assertNull;
import static org.junit.Assert.assertTrue;

import java.util.HashMap;
import java.util.Map;

import javax.script.ScriptException;

import org.junit.Test;

public class ScriptableTest {

	/**
	 * Test Initialization with default engine
	 */
	@Test
	public void testInitDefault() {
		assertNotNull(new Scriptable());
	}

	/**
	 * Test initialization with given engine
	 */
	@Test
	public void testInitWithName() {
		Scriptable sc = new Scriptable("JavaScript");
		assertNotNull(sc);
		assertTrue(sc.getScriptingLanguage().equals("JavaScript"));
	}

	/**
	 * Test that giving an invalid name for the egine does not impact creating
	 * the object.
	 */
	@Test
	public void testInitUnknownScriptingEngine() {
		// This will still work - The engine will be null
		// This can be checked if we have a getter for engine
		// We will check in next test
		Scriptable sc = new Scriptable("UNKNOWN");
	}

	/**
	 * Test that trying to execute a script on a object created with an invalid
	 * engine name fails.
	 *
	 * @throws ScriptException
	 */
	@Test(expected = Exception.class)
	public void testInitAndExecuteUnknown() throws ScriptException {
		Scriptable sc = new Scriptable("UNKNOWN");
		sc.executeScript("dummy");
	}

	/**
	 * Test a simple execution that returns a null
	 *
	 * @throws ScriptException
	 */
	@Test
	public void testExecuteCodeNoBindingsNoReturn() throws ScriptException {
		Scriptable sc = new Scriptable();
		Object res = sc.executeScript("print 'hello'");
		assertNull(res);
	}

	/**
	 * Test a simple execution that returns a value
	 *
	 * @throws ScriptException
	 */
	@Test
	public void testExecuteNoBindingsButWithReturnValue()
			throws ScriptException {
		Scriptable sc = new Scriptable();
		Object res = sc.executeScript("1+1");
		assertTrue((Integer) res == 2);
	}

	/**
	 * Test that an exception is thrown for invalid scripts
	 *
	 * @throws ScriptException
	 */
	@Test(expected = ScriptException.class)
	public void testInvalidScript() throws ScriptException {
		Scriptable sc = new Scriptable();
		Object res = sc.executeScript("DUMMY");
	}

	/**
	 * Test execution of erroneous scripts
	 *
	 * @throws ScriptException
	 */
	@Test(expected = ScriptException.class)
	public void testErrorneousScript() throws ScriptException {
		Scriptable sc = new Scriptable();
		Object res = sc.executeScript("1/0");
	}

	/**
	 * Test execution of scripts in another language
	 *
	 * @throws ScriptException
	 */
	@Test(expected = ScriptException.class)
	public void testDifferentLanguageScript() throws ScriptException {
		Scriptable sc = new Scriptable();
		Object res = sc.executeScript("def test():" + "\n\tputs('test')\n"
				+ "end");
	}

	/**
	 * Test that you can supply binding variables and use them in the script
	 *
	 * @throws ScriptException
	 */
	@Test
	public void testExecutionUsingBindings() throws ScriptException {
		Map&lt;String, Object&gt; data = new HashMap&lt;String, Object&gt;();
		data.put("a", 5);
		Scriptable sc = new Scriptable();
		Object res = sc.executeScript("a*a", data);
		assertTrue((Integer) res == 25);
	}}</pre>
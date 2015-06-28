---
layout: post
title: "Managing code deployments in large distributed applications"
date: 2012-12-10 00:00
comments: false
categories:
---

Most of us have worked on applications that are small enough that they can be deployed to the users desktop, and also on applications that are deployed to the <a  title="Server (computing)" href="http://en.wikipedia.org/wiki/Server_%28computing%29" target="_blank" rel="wikipedia">servers</a>. This could be applications like web applications that are deployed to a web server - in this case the code is same across all servers. There can be cases where there are many components that use the same code base but are distinct enough that you can put different version of the code for each component. But generally, the way the code is deployed depends on the nature of the application. And depending on the deployment, there can be some issues with how you manage the code.

This post is an attempt to understand these different cases and the issues that are posed by the choices that we make.


<h2>So what does deployment depend on? And what sort of issues can it cause?</h2>
Deployment generally depends on the product and the application. If I was buying an OS, I would expect a CD to be shipped to me, or a link where I can download an <a  title="ISO image" href="http://en.wikipedia.org/wiki/ISO_image" target="_blank" rel="wikipedia">ISO file</a> or now with Apple, buy it in the store online and then download and update the software. In a large enterprise though there are many tools to provision and distribute software - there are many <a  title="Patch management" href="http://www.symantec.com/patch-management" target="_blank" rel="symantec">software management</a> tools from the likes of Microsoft and others that make it a charm to install software. In these cases the entire software is installed on the desktop.

In other cases like when you are deploying an application on the server, you will put all the libraries on the server - Java <a  title="JAR (file format)" href="http://en.wikipedia.org/wiki/JAR_%28file_format%29" target="_blank" rel="wikipedia">jar files</a> or C++ <a  title="Library (computing)" href="http://en.wikipedia.org/wiki/Library_%28computing%29" target="_blank" rel="wikipedia">shared libraries</a> etc - and then setup the components to start on the server. You might have an option to deploy the libraries per component or in shared location for all the components.

When you decide on how you will deploy the code for the shared components, that will determine how you setup the dependencies between them and how easily you will be able to manage them and deploy changes between them, and whether you need to take any extra care. Some common things that might happen include -
<ol>
	<li>Two components that share the same code not being built with the same version of the underlying library</li>
	<li>One of the components needing an emergency change that cannot be propagated to other components without major testing</li>
	<li>Especially in case of Java applications, because there is a large number of jar files on the common class path, applications might end up loading code that they will not use and this will lead to consuming more memory.</li>
	<li>The code needs to be deployed simultaneously to all the servers that run the application, but there are issues with this or sometimes code is not copied correctly</li>
	<li>Sometimes due to regulatory or compliance reasons you might want to put some code only on some servers, and then the whole system is in an inconsistent state.</li>
</ol>
You can argue that #1 should not happen because when you upgrade the version of any core library, all dependent components should also upgrade - but in a real business application we all know that we cannot change anything that fast without first proving that it will not impact anything. And this means that all components will not change at the same time.

The other cases are created because of support issues, defect fixes etc.

These are the main issues that we generally end up in large systems that are deployed across many servers - and this is not because the developers do not care but mainly because the requirements or the fixes being made will demand that you only fix small parts at once. And the pace of changes means that there is no time to take stock and fix things.
<h2>Having an efficient patch or hotfix strategy</h2>
Long time ago when I worked on a <a  title="Java (programming language)" href="http://www.oracle.com/technetwork/java/" target="_blank" rel="homepage">Java based</a> desktop product, we had the concept of patch and hotfix. This essentially meant that we had two folders which appeared first on the classpath and had higher precedence than the rest of the classpath. This later moved to using version numbers on the jars so that the latest version was loaded by the classloader - this was a custom thing that was built by the main vendor. But the main thing here was that the fix that was being deployed to any defect in the application was put in these folders instead of updating the main jar files. This meant that if we had to rollback some changes, we did not have to <a  title="Installation (computer programs)" href="http://en.wikipedia.org/wiki/Installation_%28computer_programs%29" target="_blank" rel="wikipedia">reinstall</a> anything - all we had to do was to delete the particular patch file.

This same approach can be used to fix all the issues that we listed above - except the one about code not being properly deployed to all servers. As long as you have a patch directory for all components and this takes precedence, you can put the patch in that folder and it will impact only that component. Every other component can continue to work as it did until it is ready to take this code change.

This also means that we should be careful to avoid a situation where we are not simply patching all the changes and we lose track of which version of the code we are running. For example if we used version numbers on the jar files in Java or the shared libraries of a C++ application then at a point we can look at the version and tell what we are running. But if we put a certain version in the component, and then patched every other change without a version , then there is no way of safely telling which version we are running.

We should avoid this patch hell!
<h2>Although the latest code is not deployed, we might deploy something that is built with the missing code</h2>
This case sounds weird, but let me explain with an example. There is a class A which is deployed to all servers. Now for a fix we add more methods to class A and we deploy that to just 2 servers. This deployment was created by running the code on a build machine that has all the latest code. Now for another change you create a class B which calls class A but not the new methods. This class B is built on the build machine using the latest version of class A and is deployed to all servers including those that do not have the latest version of A. Now that's a problem. Will this even work?

Turns out, in my experience which is mostly Java, that this works very well for Java. As long as the <a  title="New class" href="http://en.wikipedia.org/wiki/New_class" target="_blank" rel="wikipedia">new class</a> for class B has no references to the new methods on class A, the <a  title="Java Virtual Machine" href="http://en.wikipedia.org/wiki/Java_Virtual_Machine" target="_blank" rel="wikipedia">JVM</a> can load the old version of class A and the new class B and still work fine. All will work fine until one day you decide to add the call to the new methods in class A at which point your deployment will fail!
<h2>When two components with different versions of code serialize data, things always fail</h2>
Lets say you are working on a messaging application  or a <a  title="Service-oriented architecture" href="http://en.wikipedia.org/wiki/Service-oriented_architecture" target="_blank" rel="wikipedia">SOA</a> system - one of the services serializes data and sends it across some messaging system to the other components. If they need to deserialize the data and make something of it, then they need to run the same version of the code. If they don't then they cannot understand each other.

This is mitigated to a large extent by versioning the data and putting the ability to ignore unknown attributes and methods on objects - similar to type casting - but that doesn't always work.
<h2>Conclusion</h2>
There is no hard and fast rule that can say that a particular deployment approach is correct and that it will solve most of the issues. The approach that works depends on the application, the business drivers and the kind of changes being made to the application. We should keep ourselves aware of  the deployment choices that we are making and what kind of issues might crop out of that. Once we have a good understanding, we can always make the right decision based on the fix and then keep the impact to the minimum.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://java.dzone.com/articles/simple-powerful-concept" target="_blank">Simple but Powerful Concept: Packing Your Java Application as One JAR</a> (java.dzone.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://TimothyFitz.com/2012/11/25/paths-to-continuous-deployment/" target="_blank">Timothy Fitz: Paths to Continuous Deployment</a> (TimothyFitz.com)</li>
</ul>
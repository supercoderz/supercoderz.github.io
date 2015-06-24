---
layout: post
title: "Continuous deployment system and build validation"
date: 2011-02-01 22:56
comments: false
categories:
---

<div class="zemanta-img">

[caption id="" align="alignright" width="300" caption="Image via Wikipedia"]<a href="http://commons.wikipedia.org/wiki/File:Hudson_Screenshot.png"><img title="Hudson Screenshot" src="http://upload.wikimedia.org/wikipedia/commons/thumb/0/07/Hudson_Screenshot.png/300px-Hudson_Screenshot.png" alt="Hudson Screenshot" width="300" height="232" /></a>[/caption]

</div>
One of the recommended and widely followed practices in agile and fast paced projects is to use a continuous build system. Usually this is a <a class="zem_slink" title="CruiseControl" href="http://cruisecontrol.sourceforge.net" rel="homepage">CruiseControl</a> or <a class="zem_slink" title="Hudson (software)" href="http://hudson-ci.org" rel="homepage">Hudson</a> or some such build tool hooked into the source control system and scheduled to build periodically. With a disciplined team of developers who test their code and do not commit code that does not work and breaks everyones builds, this is a good thing.

But does this solve all problems?

<!--more-->

Anyone who has worked in a tightly controlled and regulated production environment like in a bank will know that the worst part starts after the build - when it comes to deployment. You need to make sure that the right version of the code is deployed on all servers with the right configurations etc etc. In a system that has been built from scratch and from the beginning compliant with deployment best practices, it is possible to have glitch free deployments. Also it is possible to have code deployed from tags or branches in the source control system and life will be good.

However, we all know, deployments go wrong. And they do so quite often.

How do we solve this?

I recently had an issue at hand which was downright embarassing and I had my pants in such a twist that it hurt. We have a decent ant based build script that requires me to just run a few scripts and I am ready with a tar file to be extracted. So far we never had issues with build versions, tags etc. But it so happened that an incorrectly named folder made its way to the source control. That folder was to be referred to in production only and not anywhere else was the real disaster waiting to happen. When the build scripts were run and we did a peer check on the folders. due to the incorrect name being very very similar to the correct one, it slipped our eyes. The result was that we looked like fools for an hour or so and are avoiding being seen on some floors of the office for a while.

What could we do better? What could anyone do for these kinds of issues?

We came up with  a solution that tries to mitigate these minor, but downright embarassing issues as much as possible. What we did is
<ul>
	<li>Draw up a list of all valid values and names that we can have</li>
	<li>Make a list of all similar sounding values and names that can break the thing</li>
	<li>Identified all the things that we check manually and the expected and normal values for them</li>
	<li>We then built a script using <a class="zem_slink" title="Groovy (programming language)" href="http://groovy.codehaus.org" rel="homepage">Groovy</a> that reads through our entire deployment directory structure and validates based on the above</li>
	<li>This script is plugged into our ant build</li>
	<li>Few times a day, our Hudson jobs kick in and run a build of the entire code</li>
	<li>This triggers another job which does a mock deploy on a local folder.</li>
	<li>The validation script is run on this mock folder and a list of all deviations from expected values is mailed to us.</li>
	<li>During the initial development rush of each release, the list is usually long.</li>
	<li>However as we reach closer to the deployment, the list comes to empty</li>
</ul>
This kind of continuous deployment and validation, in addition to the build and testing has helped us by identifying some issues that could have been really touch to get out of without embarassing ourselves.

We used Groovy because it was familiar to us. It could have been <a class="zem_slink" title="Python (programming language)" href="http://www.python.org/" rel="homepage">Python</a>, Ruby or even a shell script. The main thing here was to identify  the checks that will mean a successful deployment without actually starting the application. This check performed regularly will mean that post deployment when we start the application, the chance of a silly error like a missing or incorrectly named file is very less. What is left is a major issue like connectivity or more severe bugs which mean that the release is anyways dead.
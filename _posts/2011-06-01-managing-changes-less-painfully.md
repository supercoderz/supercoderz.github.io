---
layout: post
title: "Managing changes less painfully"
date: 2011-06-01 23:54
comments: false
categories:
---

<div >



</div>
Some of us have seen <a  title="Dexter's Lab" href="http://www.cartoonnetwork.com/tv_shows/dexter/" rel="hulu">Dexter's lab</a> on cartoon network as kinds and some of us have seen something like this in a movie or somewhere else - an elaborate setup where a small action like opening a door starts a process, a ball drops, it moves a lever which in turn starts a flow of water through a set of twisting and turning test tubes which does something else and finally after a long route some small result happens like a bulb glowing or a trap door opening. If it was setup by a bad guy in a movie then it probably kills someone or something bad. I forget what it is called but its an elaborate setup to achieve something which is a small goal compared to what must have gone in to build the setup.

This is exactly what change management sometimes feels like.


<h3>Change is the only constant</h3>
We all make changes on our software everyday. We might do a big release once in a quarter or once in a year but we make changes most often. Some enhancement, some improvement, some fix for a scenario that we did not think about or some weird bug that happened when the user logged in from home and entered the input using a Japanese keyboard. But we do make changes. Sometimes there are emergency changes when there is a production issue and sometimes we sneak in a change to control the damage before anyone notices.

Changes come in all priorities and some need lots of preparation, some need a system outage and some are plain stupid changes that can be done in sleep. However, all of these can impact the software and all the other components that interact with that. and sometimes these changes can cause more problems than they solve because of conflicts and bugs etc. This is where change management comes in - it tries to mitigate the risk of changes by managing what goes in.
<h3><a  title="Change management" href="http://en.wikipedia.org/wiki/Change_management" rel="wikipedia">Change Management</a></h3>
Change management is no silver bullet magic process that solves all problems on its own. It is a set of rules and practices that work on known parameters and try to asses the risk of a change. These parameters include
<ul>
	<li>What is the change?</li>
	<li>What systems are impacted by this?</li>
	<li>What are the related components that are impacted by this?</li>
	<li>Has this been tested and signed off with valid test results?</li>
	<li>Is there any outage related with this?</li>
	<li>What is the worst thing that can happen if this or something related goes wrong?</li>
	<li>Is there someone ready to fix or take the blame if things do go wrong?</li>
</ul>
<div>This list is not by any means exhaustive and you can add to it in more than one way. So, the change management team would look at the change, ask these questions, take the answers and see how satisfied they are with the answers and how convinced they are with those and approve or reject your changes. Most of the time they ensure that only the right kind of changes go in and that all changes are carrying the least risk. But things still go wrong.</div>
<h3>Why does change management fail?</h3>
Change management fails for many reasons like
<ul>
	<li>The team making the change missing out on something that cannot be caught in testing and occurs in production only. There are many bugs that occur only in production or the code works fine on the developers machine - we all know this one.</li>
	<li>The parameters checked by the change management team do no fully capture all the risks associated with this change.</li>
	<li>The change management team does not have complete understanding of this application and hence cannot make a sound judgement.</li>
	<li>The team making the change misses a step or mis-represents the change and the change management team cannot spot that because they are not fully familiar with the change.</li>
	<li>The change was a last minute change that was pushed through hastily</li>
	<li>Bad luck</li>
</ul>
<div>Again this list is not exhaustive.</div>
<h3>How do we avoid all these issues with change management?</h3>
Different organizations take different approaches to avoiding these kind of issues.
<ul>
	<li>One place where I worked for a while had a rule that any change needed to be in pre-production for a week and run smoothly without any issues.</li>
	<li>Another place had a rule that we create a set of steps that can be given to any person with zero knowledge of the system and the application and actually asked such a person to deploy to a test environment.</li>
	<li>Some places insist on double and triple verifying test results.</li>
	<li>Some need a 7 day or maybe even 2 week lead time and a dozen calls to present and re-present your change till you can talk about it in sleep</li>
</ul>
<div>Very few places have such strict controls that you completely give up making changes and even fewer places give you the freedom that you deploy a change and then go 'oops! I just deployed it'. But they all make your life difficult.</div>
<div>While these are all supposed to be for the good of all of us and help us make the system stable and reliable and all those goody goody things, they do become a pain when you want to implement a small change like editing a file. Or when the business does not see why you cannot get a change done yesterday. Sometimes you might be the only guy and have to juggle all these; sometimes the change manager might be in a different time zone 14 hours behind you.</div>
<div>Writing a <a  title="Application software" href="http://en.wikipedia.org/wiki/Application_software" rel="wikipedia">software application</a> is a small job than going through all these processes - or like my example at the beginning , an elaborate setup or trap - to get that change deployed. Worst case it might get rejected after all this and worst still is when what got rejected after all the pains is a small edit to an <a  title="XML" href="http://en.wikipedia.org/wiki/XML" rel="wikipedia">XML</a> file!</div>
<h3>Is there no middle ground that is less painful?</h3>
Organizations run IT for making more money with their core business and stability is the key to making more money which in turn pays for IT - so it is unlikely that the business will one day start allowing ad-hoc changes at the whim and fancy of IT. What probably can be done is that we could use different scales to measure different types of changes - and cause different levels of pain in implementing them. This is actually tried in some organizations and works in a subset of those.

What can be done is that the technology and business teams agree on fixed release schedules like once in two weeks or three weeks or better have a release calendar and then have someone dedicated to managing the changes and assessing them and getting them through change management. This will avoid each developer sitting through a one hour call and waiting 40 minutes in that to give a 2 min representation of his change. Plus they can concentrate on building the damn thing rather than worrying if it will be approved.

This approach is costly in terms of resources and the amount of planning and foresight required to group releases. Also it requires that the system be inherently stable so that frequent changes are not required.
<h3>Conclusion</h3>
Changes and the pains of change management will exist for as long as businesses run on IT. There is no easy solution to this and the best way to get around this is to make sure we get a balance between both the sides and make our work more enjoyable.
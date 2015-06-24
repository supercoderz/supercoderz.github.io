---
layout: post
title: "Code as a liability"
date: 2013-09-29 19:05
comments: false
categories:
---

Of late I have heard people talking about code as a liability. In the past I often heard people talking about <a class="zem_slink" title="Writing" href="http://en.wikipedia.org/wiki/Writing" target="_blank" rel="wikipedia">writing</a> less code to solve a problem and they usually meant it from a maintainability point of view, but no one ever said it was a liability. And this got me <a class="zem_slink" title="Thought" href="http://en.wikipedia.org/wiki/Thought" target="_blank" rel="wikipedia">thinking</a> - is a maintainability issue just another liability? Are there any real concerns with being <a class="zem_slink" title="Legal liability" href="http://en.wikipedia.org/wiki/Legal_liability" target="_blank" rel="wikipedia">liable</a> for a piece of code? And more importantly, does this apply to code that is written in large enterprises or doe sit apply only to the <a class="zem_slink" title="Open source software" href="http://en.wikipedia.org/wiki/Open_source_software" target="_blank" rel="wikipedia">open source code</a> that is collaboratively built on the internet?

This post is an attempt to explore these ideas.

Note: For those of you who noticed, I haven't blogged in almost 10 months. This has been mainly due to relocating to a new place and change in work patterns. I hope to keep a regular schedule now.

<!--more-->
<h2>Understanding when we write code</h2>
From the opinions I heard about code being a liability, I always found that the main issue seemed to stem form how we wrote the code - the purpose behind it. So lets first try to go over a few cases where we write code and see if we find any characteristics about each case.
<ol>
	<li>We write code on the job, for a given set of <a class="zem_slink" title="Requirement" href="http://en.wikipedia.org/wiki/Requirement" target="_blank" rel="wikipedia">requirements</a> to solve a problem and release it to a set of users.</li>
	<li>We write code for our contributions to the various <a class="zem_slink" title="Open Source" href="http://www.wikinvest.com/concept/Open_Source" target="_blank" rel="wikinvest">open source</a> projects that we think we are a part of.</li>
	<li>We build libraries that we think are common enough that others would also like to use</li>
	<li>We write code for ourselves - our blogs, websites, applications to manage our various tasks etc</li>
</ol>
This is a very broad classification but catches the essence of what situations we face.

If we take the first one of writing code on the job, the code that we write is always defined by the requirements that the users have. We sit with the users, we get the details, we throw in some of our own ideas and improvise but at the end the code always has to conform to what the users want to accomplish rather than the ideas that we have. Although we can design the code for long term strategy, if the users change their requirements then our strategy has to change too.

When write code for our contributions to open source projects, we write code for issues, or improvements reported by the users. However the key difference here is that instead of directly facing users who use this, in many cases, we face other developers who use the code. So the discussions are more technical and we get to choose what features we implement and the design that we use for them. This lets us build it in a way such that they meet the long term direction we have in mind. And we control the long term strategy.

When we build libraries, we are trying to build <a class="zem_slink" title="Use case" href="http://en.wikipedia.org/wiki/Use_case" target="_blank" rel="wikipedia">use cases</a> that we think will be most commonly use. We then add on top with specific cases that we find as the library evolves. We cannot always meet all the use cases so we build what we can and we try to let the users be able to build their own extensions, and maybe add in support for that later.

When we write code for ourselves, we are in full control. We make decisions that we think are correct at that point of time for us and we work on that. If something doesn't work out, then we don't have to worry about putting off people and we could if we wanted to just go an rewrite the whole thing. That's the ultimate freedom.

Now that we have the cases where we write code, lets see if and when we could end up in case where the code we wrote is a liability.
<h2>On the job</h2>
On the job we write code for users requirements - and these can change any day the users decide to do something else or they decide to change the way they do things. In the worst case of course you could see that some third party tool or a service provider that you use changes and you are forced to change. In this case you might have to rewrite the whole thing. But these cases don't really become a liability. Because in these two cases you are doing something for a new process or tool and you more or less always have the freedom to change things from scratch to suit how things should be. No one is going to hold you to an old piece of code and ask you why you wont write it the same way again.

The problem comes when a piece of code grows and takes a different path than the rest of the system and then comes back to bite you and make you wonder why you even wrote it - and then it becomes a liability. We all have cases where we went and built something for a user, as a stop gap, and he liked it so much that he kept on using it for years and then asked you to support this.

The main reason this happens is due to diverging requirements. In any large company no two sets of users will have the same set of requirements or preferences. Yet the business who pays for your time to code will want to use the same system for all these people. So what starts as an initial good generic design soon runs into a case like this - the application works in a certain way, but a set of users want a slightly different functionality. You can spend a month changing everything right from the design to engineer this the correct way which will require so much man hours or you can fork the code and write something for this use case now in a week. With a promise of course that you can merge it later at a date and clean it all up which almost always never happens.

This is the kind of code that becomes a liability mainly because
<ol>
	<li>Probably the guy who wrote it went away</li>
	<li>The approach was static and never grew with the way the application is changing and is now outdated</li>
	<li>It cannot be merged into the main code without significantly altering the main <a class="zem_slink" title="Codebase" href="http://en.wikipedia.org/wiki/Codebase" target="_blank" rel="wikipedia">code base</a> and impacting others</li>
	<li>No change is needed but this code is bringing down the performance of the application and people are not happy</li>
	<li>This piece of code works fine but usually creates incidents which make the whole app look bad and management does not like this</li>
</ol>
#5 is my favorite.
<h2>Contributions</h2>
As developers or designers or even managers we are proud of our work and how we do it. So when we build a collaborative open source project, even though we are supposed to be democratic and objective, just because of the amount of life we gave to it we do get emotional about the code. So any change that someone wants to make to it has to pass our judgement.

Our users are equally passionate in the way they are using the software and the issues they are facing with that. And the contributors are also passionate when they spend so much time devoted to the growth of our project. All this emotion leads to case where we think we should make a change and we run into a situation where most people don't agree or when it takes too much time to agree. The meritrocatic process in large projects often means that a <a class="zem_slink" title="Distributed revision control" href="http://en.wikipedia.org/wiki/Distributed_revision_control" target="_blank" rel="wikipedia">pull request</a> or a patch takes long time to get accepted. So for a while you end up working on a <a class="zem_slink" title="Fork (software development)" href="http://en.wikipedia.org/wiki/Fork_%28software_development%29" target="_blank" rel="wikipedia">forked</a> version of the code.

If you are working for a large company that is not too much into all these open source ideologies or simply has to guard their secrets then whatever you want to change in this software cannot be made known and you  must make your own fork of the code.

This leads to two problems - you end up using unsupported code and there is no guarantee that your code will make it to the mainline code. So you end up owning this code for the while that you are there. And you are responsible for all the issues. You might want to get it over with and let someone else take the code so that you can move on but you will be stuck with this. And god forbid if the project that you forked to contribute decided to go another way - you will have to own this code forever.

This is easy if you were just a contributor, you can just throw away the code and move on. But if not and you are using this in code that runs a business then you need to get someone to approve you some time to undo this and do this the correct way - which is never easy.
<h2>Building  libraries</h2>
Most of what we discussed above applies here too. But there are new twists. When we build a library we support a set of use cases. Over a period of time we add in some specific cases that we saw or were requested by someone. And then we add special cases to handle some scenarios which are bad. Almost always we are building a library for an underlying system resource or a functionality that we need to build using common system resources - socket IO for example.

What happens is that the OS or the underlying technology evolves and provides easier or more efficient ways of doing things and we have to accommodate them. So we probably decide to do it and we think that since not all the users will move with us we need to have some backward compatibility. And we add it in. And then things move on and they keep moving on such that at one point we have a lot of backward compatibility that is causing is to slow down in moving ahead and a good number of users that we cannot antagonize by removing these features.

Catch 22 or whatever nice words but this is a shitty position where you feel you are less backwards compatible and more backwards bending to please users who are not letting you move ahead. And sometimes the only way is to go out and write something better and let all the willing users move to that while the old ones take their own sweet time to move when they want.
<h2>Writing code for ourselves</h2>
When we code for ourselves, we face a bigger issue than all of the above - the issue of losing interest or moving on or becoming too busy. On a good day we feel enthusiastic and fork something and then we start using that big time. We run with it for years when we are free and then comes day when we are unable to maintain the same pace of things we had when we started to fork the code and use it in our own way.

Another issue we face is the amount of knowledge that we have locked up in our heads because we never had to share it with anyone like in the other cases - so it makes it even harder to just hand it over to someone and let them do it.
<h2>What the common causes that we see from all this?</h2>
What we can see here is that there are two main causes for code to become a liability for us to moving ahead -
<ol>
	<li>Forked code that does not keep up with the rest of the design and hard to merge back</li>
	<li>Use cases and legacy support which no longer makes sense but is being used by  a large number of users who are not willing to migrate</li>
</ol>
These are not things that we do on purpose but rather things that happen due to the nature of the environment and business that we operate in.

And this proves another point that we as developers don't always intentionally write some bad code that is a liability - although this is possible and happens in many cases, code review, QA etc do mitigate it to a large extent.

Also if we think about this, all these issues are what we could call as maintainability issues - if you think from a support for a higher up point of view. But from a developer point of view they can be called a liability. So this is just the same thing seen different ways.

In conclusion we could say that code can become a liability if we let it ow without a defined path - and the bets way sometimes to solve this is to take some time and think through the long term plan before we design something. And if we still run into the same wall then we just throw away and redesign the whole thing.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://alexgaynor.net/2013/sep/26/effective-code-review/" target="_blank">Effective Code Review</a> (alexgaynor.net)</li>
	<li class="zemanta-article-ul-li"><a href="http://news.slashdot.org/story/13/09/11/0048242/how-to-turn-your-pile-of-code-into-an-open-source-project" target="_blank">How To Turn Your Pile of Code Into an Open Source Project</a> (news.slashdot.org)</li>
</ul>
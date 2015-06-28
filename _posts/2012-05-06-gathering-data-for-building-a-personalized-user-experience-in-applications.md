---
layout: post
title: "Gathering data for building a personalized user experience in applications"
date: 2012-05-06 19:03
comments: false
categories:
---

If I told you that a person is visting you, gave you no information about that guy, and asked you to prepare a list of things for him to do while he was visiting, will you be able to do a good job of it? You might actually be able to get a so so result but the outcome of whatever you do will be far from good.

<a href="http://en.wikipedia.org/wiki/File:Facebook2007.jpg" target="_blank"><img  title="Facebook profile shown in 2007" src="http://upload.wikimedia.org/wikipedia/en/thumb/2/28/Facebook2007.jpg/300px-Facebook2007.jpg" alt="Facebook profile shown in 2007" width="300" height="311" /></a> Facebook profile shown in 2007 (Photo credit: Wikipedia)

Similarly if I gave you the weather prediction for a week, but the data was accurate to a precision of 10 degrees, then you will have data but not be able to make a informed decision about it since it was not correct to begin with. Whatever you decide will only be a best guess.

These two trivial and known examples illustrate a simple point - you need proper data to make a decision. This applies to large enterprises making business decisions, people planning their vacations and everyone who is making a decision in general.

These days all the websites that we use a providing a very personalized view to us - <a  title="Facebook" href="http://www.zdnet.com/topics/facebook?tag=header;header-sec" rel="zdnet" target="_blank">Facebook</a>, Amazon etc have vast amounts of computing power thrown at the data they have just to figure out the right recommendation for you. Users who are used to this will demand similar customization in the applications that they use elsewhere - even in the office.

This post tries to understand what is required to build such capability in these applications.


<h2>Its nothing new - it was all there in the past too</h2>
User level customization and <a  title="Personalization" href="http://en.wikipedia.org/wiki/Personalization" rel="wikipedia" target="_blank">personalization</a> is nothing new - ever since we had Windows 95 and probabaly even in 3.1 as well, if you used a text editor it had the list of recently used files that let the user figure out what he was editing. There was wallpaper and themes, and I am sure there will be <a  title="Unix" href="http://www.unix.org" rel="homepage" target="_blank">UNIX</a> users who will point out that even the command line tools and simple editors had some level of customization - even in VI editor.

So this is not something new that has been brought on us by Facebook or other new websites. What they did however is to take it to a new level.
<h2>The underlying need for user personalization</h2>
User personalization is not a cosmetic issue - there is an underlying need for this in order to improve producivity.  In an enterprise application that for example deals with customer information, a customer service representative will be very happy to see a list of customers that he recently worked with. Or for example a list of customers that he or she deals with on a regular basis. That way they can quickly grab the details.

Similarly, if they needed to enter something, users would prefer to have big fonts and easily obvious controls with prompts to autofill what they enter more often. Think Apple and you will know the importance of <a  title="Graphical user interface" href="http://en.wikipedia.org/wiki/Graphical_user_interface" rel="wikipedia" target="_blank">GUI</a> friendliness.

Making things obvious and clear will make it less error prone and users will be able to fluidly use the application without making mistakes or facing issues.
<h2>The elements of user personalization in applications</h2>
What do we need to be able to personalize an application for the users? We need
<ul>
	<li>A clear understanding of the functionalities provided by the application and how they work</li>
	<li>A clear understanding of how the user actually uses it - does he or she use the mnu or use a shortcut, do they prefer keyboard or mouse etc</li>
	<li>A clear idea of what are the critical elements of the application that serve as entry points or can be summarixzed for quick reference</li>
	<li>The relative importance of each of the key elements and any sub elements</li>
	<li>An understanding of how best the users would represent these if they had to write the same thing on a paper</li>
</ul>
Once we have these,we can build anything. All this is nothing but data - and if we have good data then we can build a good user experience in the application. The last point is very important - each of us has a way of writing down notes in a way that makes sense to us. If we collect how a set of people write notes on a given topic, we will find some commonality in that - each problem domain or use case has some common things that everyone does the same way. These common things can be hard coded into the application, others can be customized.

In order to build the user applicaion we need gather all this information.
<h2>Design time vs runtime information gathering</h2>
It is not possible to get all the details on how the users will use the application in the design phase - this is because once they are given the application they might come up with their own ways of using it. Also they might want to customize some parts of the application so we need to provide that.

However, as the users use the application there will be some data generated like the files they used more often or the product they worked with or the customer they serviced - this information is also golden and needs to be captured in the application. This will help build a more evolving system.
<h2><a  title="Data" href="http://en.wikipedia.org/wiki/Data" rel="wikipedia" target="_blank">Data storage</a>, archival and retention times</h2>
Once you start letting users customize the application or start gathering runtime information, you need ot store that somewhere. This storage has to be resilient, quick to access and easy to update and most importantly should not slow the application. Local databases like <a  title="SQLite" href="http://sqlite.org" rel="homepage" target="_blank">SQLLite</a>, custom files or <a  title="XML" href="http://en.wikipedia.org/wiki/XML" rel="wikipedia" target="_blank">XML</a> or even <a  title="Windows Registry" href="http://en.wikipedia.org/wiki/Windows_Registry" rel="wikipedia" target="_blank">Windows registry</a> are good options.

Once you store the data, you should be able to prevent the other users from seeing this data - this is very important of the application deals with confidential information.

Another issue is retention period - how long would you need this information? If I worked with one customer today and that gets recorded, how long would I need this if I dont work with that person again for many months? This is an important decision since that will determine the cost in terms of storage etc that is needed to maintain this information. The more data that application has to store and process the slower it might be.

This is something that cannot be determined at design time without involving the users. Also this might change as the application evolves, so we should be able to handle this.
<h2>Efficiently processing the data that we collect</h2>
We have the data to show and we know how to show it - but are we processing it to show meaningful information? Raw data makes no sense and a user might feel more at ease if we processed the information and showed something that makes more sense. Lets say we want to say that a customer has not been contacted in many months - instead of saying customer was not contacted since some date, if we said that a customer was last contacted on so and so date about this issue and not after that , then it gives more context and makes it easier to reach a conclusion on what to do.

This <a  title="Decision making" href="http://en.wikipedia.org/wiki/Decision_making" rel="wikipedia" target="_blank">decision making</a> can be extended to make even more complex decisions and give more context - like show all customers who are likely to have the same issue. This would involve mining all the customer information and determining which customers fit the same pattern and then making a decision. This is a time consuming operation and needs to be done on the back end.
<h2>It is an evolving process</h2>
<a  title="User experience" href="http://en.wikipedia.org/wiki/User_experience" rel="wikipedia" target="_blank">User experience</a> is an evolving thing and never remains constant. As the application grows or the user base grows there will be new requiements that would come up. Doing everything from scratch each time something has to be changed can be tough. The application needs to be able to evolve and easily handle change.
<h2>Conclusion</h2>
Building user personalization into application is all about data and using that correctly. Once you know what data to show, it becomes very easy.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://www.zdnet.com/blog/virtualization/knoa-software-managing-end-user-experience/4448" target="_blank">Knoa Software - managing end user experience</a> (zdnet.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.cygnismedia.com/blog/gamification-and-ux-design/" target="_blank">Gamification and UX Design</a> (cygnismedia.com)</li>
</ul>
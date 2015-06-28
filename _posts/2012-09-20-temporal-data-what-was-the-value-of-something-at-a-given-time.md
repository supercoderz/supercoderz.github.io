---
layout: post
title: "Temporal data - what was the value of something at a given time?"
date: 2012-09-20 23:41
comments: false
categories:
---

[caption id="" align="alignright" width="300"]<a href="http://commons.wikipedia.org/wiki/File:MontreGousset001.jpg" target="_blank"><img  title="Pocket watch, savonette-type. Italiano: Orolog..." src="http://upload.wikimedia.org/wikipedia/commons/thumb/4/45/MontreGousset001.jpg/300px-MontreGousset001.jpg" alt="Pocket watch, savonette-type. Italiano: Orolog..." width="300" height="261" /></a> Pocket watch, savonette-type. Italiano: Orologio da taschino (cipolla). Español: Reloj de bolsillo. ગુજરાતી: ખિસ્સામાં રાખવાની ઘડિયાળ. עברית: שעון כיס. Македонски: Џебен часовник 日本語: 懐中時計. Polski: Zegarek kieszonkowy. Português: Relógio de bolso. Русский: Карманные часы. Slovenščina: Vreckové hodinky. Slovenščina: Žepno uro so izumili leta 1510 v Nemčiji. Suomi: Taskukello. ไทย: นาฬิกาพก. 中文: 怀表. (Photo credit: Wikipedia)[/caption]

We all know data, we all know consistency of data when dealing with transactions. There is another aspect of data - temporality, meaning data at a given point of time. What is the value of something now? and what was the value of it as of yesterday morning? I haven't worked too much with temporal data, but have used few applications that provided this - in their own ways. I was reading through the new <a  title="Google" href="http://google.com" rel="homepage" target="_blank">Google  research</a> paper on <a title="Spanner" href="http://research.google.com/archive/spanner.html" target="_blank">Spanner</a> - their global time aware database, and came across the TrueTime <a  title="Application programming interface" href="http://en.wikipedia.org/wiki/Application_programming_interface" rel="wikipedia" target="_blank">API</a> - this forced me to think about the temporality of data and how important that is.

This post is an attempt to explain what I know about this. I admit I haven't fully read the Google paper so if you find any inconsistencies in my understanding of that, please point out in the comments and I will be happy to correct it.

<!--more-->
<h2>So, data ages with time?</h2>
Not exactly ages, but changes with time. To take the example that was first thrown at me when I heard about temporality of data - lets say you have a table where you store inventory updates. You have a column for item, a column for the size added or deleted from inventory, a column for update time. Now if you are updating data in there for today and you entered a value incorrectly. Not end of the world because tomorrow morning you remember that and want to fix it. You go and correct the value in the database and you are done. But there is a catch, you did not change the updated time, and there is a report that was run last night on this data. When someone looks at this report  it will now be incorrect. OK fine you run this again and its fixed.  Or you update the time updated. Simple.

Now lets take a more formal situation where all your updates must be tracked and all reports must be frozen and cannot be randomly re-run without a valid reason. And things get icky. You not only need to save values, but you also need to save when they were updated, and also the time as of which they were valid. So you could end up in a case where you have an addition of 5 items to your inventory done at 4 PM on 1 Jan 2012, but which was actually effective from <a  title="12-hour clock" href="http://en.wikipedia.org/wiki/12-hour_clock" rel="wikipedia" target="_blank">8 AM</a> that day.

So what you can see here is that from an end of day perspective, it doesn't matter when the stock was updated as long as the counts match up, but of someone asks you how much stock you had as of a certain time, then it can change throughout the day. You can say that at 1 PM I had X amount of something as of 8 AM, but at <a  title="5pm" href="http://www.5pmweb.com/" rel="homepage" target="_blank">5 PM</a>, your answer will be that as of 8 AM you had X+5 of that item!

So by taking an update time and a as of time, you can paint a very varying picture of the same fact at different times.
<h2>OK, so it is just two more columns to my table and magically my data is not temporal</h2>
Yes kind of, sort of or I should say - yes that's basically it from a technology point of view anyway. All you do is make your users enter not one but two time values - when they are inserting a piece of data and another field for as of when that is effective. In most cases they will be the same - so you don't even need to ask them to enter two values all the time - give them a check box or something to ask if they want to back date the fact and then record it. You are ready to rock and roll!

But how would you do it if you had to do it with just one column? Key the data on a pair of columns - the field that identifies the update and the time - a combination unique key - you cant change an item twice at the same instant can you?

See its simple.

OK, there is another problem - I don't use an RDBMS - I am the cool guy who uses <a  title="NoSQL" href="http://en.wikipedia.org/wiki/NoSQL" rel="wikipedia" target="_blank">NoSQL</a> - OK, fine have a column or a field or whatever you call it in your objects that stores the time stamp. Googles <a  title="BigTable" href="http://en.wikipedia.org/wiki/BigTable" rel="wikipedia" target="_blank">Big Table</a> mapped a key and timestamp pair to a value. They key was a 64 bit time stamp.

Cool!
<h2>What if two processes don't see the same time?</h2>
Eh? Lets take a simple case where the process that writes the data to the database and the one that generates these as of time reports are on the same server. Since time is maintained by the OS, they both see the same time. So when it is 5 PM it is 5 PM for both processes. And any updates done as of 5 PM will look the same.

Now, lets say the processes were on two different servers - and one of them was 5 seconds ahead of the other. And this one runs the process that records data. So if someone made a really important million dollar update at exactly 5 PM - the recording server thinks it is 5 seconds past 5 and puts that. The report server runs a report as of exactly 5 PM - and there is a mismatch! An escalation perhaps, a major incident etc etc. Soon you realize the issue when you check manually and you fix it.

So what happened here?
<ul>
	<li>Two servers did not see the same time - so they recorded data about an event as happening at different times</li>
	<li>When one of them tried to present the information, it got a wrong picture - an inconsistent picture</li>
</ul>
This means that having a proper <a  title="Synchronization" href="http://en.wikipedia.org/wiki/Synchronization" rel="wikipedia" target="_blank">time synchronization</a> between all servers is important. More like all data reads and writes should happen at the same instant of time and this instant of time should be seen correctly from all participating servers.
<h2>How do we solve this? (Before Google)</h2>
There are network <a  title="Time server" href="http://en.wikipedia.org/wiki/Time_server" rel="wikipedia" target="_blank">time servers</a> that are used to send clock signals to sync servers. Also there are means to determine latency between servers and identify the lag etc between servers to be accounted for in the time sync. All these work to a large extent and provide reliable accuracy. Financial systems make use of this a lot.

There are issues, and when things are spread over a large geography, errors do creep in. But there are ways to fix this.

If you know that you last synced up to a time server a minute ago, and your clock drifts by X every second, then if you get a request for something at the 30 second point, you know that you are ahead or behind by 30X and you can factor that into your time logic.

What Google did is do this on a global scale using Atomic clocks, <a  title="Global Positioning System" href="http://en.wikipedia.org/wiki/Global_Positioning_System" rel="wikipedia" target="_blank">GPS</a> and cool algorithms that I know nothing of.
<h2>The Google way of solving this</h2>
This is my over simplified understanding of and explanation of TrueTime API.

Google has the money, so they attached GPS and Atomic clocks to the time master servers in their data centers. Then they told their cluster management software that each of the nodes that stores data, should sync up with a given set of master servers every 30 seconds. Each master also syncs with the other masters.

Each master server holds a pessimistic value by which it assumes it is drifting every second. And advertises this. This is reset to 0 each time it syncs. The servers that sync with the master use this to calculate that when it says 5 PM, it could be plus or minus half of this drift from 5 PM. So, time is not just a single point of time but a set of possible values.

Its like this - on <a  title="Windows XP" href="http://www.microsoft.com/windows/windows-xp/default.aspx" rel="homepage" target="_blank">Windows XP</a>, system clock can resolve only up to 10 milli seconds. So it it tells you that it is 1000 milli seoconds past an epoch, then it could be anywhere between 995 to 1005 milli seconds past the epoch.

So, they got a reliable way of determining this and attached such a time stamp to every piece of data - a key and time range are used to uniquely refer a value.

Since the potential error is embedded in the time stamp, if someone says get me a value at an instant, and the process sees a time within the advertised error interval from this requested time - then it waits until that interval has passed - just to be sure that the time has happened.
<h2>Conclusion</h2>
I did not cover all aspects of temporal data - maybe I will do another post on that. But as you can see, you can record different versions of the same data varying by time. This is prone to errors if there is a large number of servers that don't see the same time. However, it can be corrected easily if we know the amount by which the time is drifting.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://gigaom.com/data/googles-spanner-a-database-that-knows-what-time-it-is/" target="_blank">Google's Spanner: A database that knows what time it is</a> (gigaom.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.blacklistednews.com/Google_Spans_Entire_Planet_With_GPS-Powered_Database/21581/0/0/0/Y/M.html" target="_blank">Google Spans Entire Planet With GPS-Powered Database</a> (blacklistednews.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.rackspace.com/blog/open_source_data_synchronizati/" target="_blank">The Official Rackspace Blog - Open Source Data Synchronization between Servers</a> (rackspace.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.zdnet.com/google-reveals-spanner-the-database-tech-that-can-span-the-planet-7000004421/" target="_blank">Google reveals Spanner, the database tech that can span the planet</a> (zdnet.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.wired.com/wiredenterprise/2012/09/google-spanner/" target="_blank">Data Centers: Google Spans Entire Planet With GPS-Powered Database</a> (wired.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://datamining.typepad.com/data_mining/2009/02/temporal-information-retrieval.html" target="_blank">Temporal Information Retrieval</a> (datamining.typepad.com)</li>
</ul>
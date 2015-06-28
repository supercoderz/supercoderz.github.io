---
layout: post
title: "What is a NoSQL database? And why would you use it?"
date: 2012-02-10 00:31
comments: false
categories:
---

I first heard about <a  title="NoSQL" href="http://en.wikipedia.org/wiki/NoSQL" rel="wikipedia">NoSQL</a> databases in an interview when a i-am-the-dude developer asked me about my experiences with NoSQL. I had no idea and later looked up the internet about these and found that they are key-value stores - like a hash map. Or like <a  title="Berkeley DB" href="http://www.oracle.com/us/products/database/berkeley-db/index.html" rel="homepage">Berkeley DB</a>  and used to store objects - In fact I had worked on building a huge messaging platform where we used Berkeley DB JE to do just that - store Java beans representing messages so that we can easily reconstruct them.

So a NoSQL database is something that stores data in terms of key-value pairs as opposed to tables in an <a  title="Relational database management system" href="http://en.wikipedia.org/wiki/Relational_database_management_system" rel="wikipedia">RDBM</a> - thus saving us from the ORM hell and having to maintain all those mappings between objects and tables. Simple.

But people said I was wrong and that NoSQL meant lack of ACID, NoSQL meant BASE, NoSQL was webscale etc etc.

I think there are a lot of misconceptions about NoSQL, and this Stackoverflow question summarizes most of the confusions and clarifications around this - <a href="http://stackoverflow.com/questions/2608103/is-there-any-nosql-that-is-acid-compliant">http://stackoverflow.com/questions/2608103/is-there-any-nosql-that-is-acid-compliant</a>

This post is an attempt to describe what is the difference between ACID and BASE, key differences between NoSQL and RDBMS databases and why exactly should you use them.

<!--more-->
<h2>Different use cases for data storage</h2>
We all want to store data and retrieve it later for different purposes - its a very simple statement. But if you dig deeper, there will be some interesting scenarios like
<ul>
	<li>Do you really want all the data to be successfully stored or are you prepared for data loss</li>
	<li>Once recorded, how long should it be stored</li>
	<li>Are you looking to quickly write large amounts of data?</li>
	<li>Are you looking to write data transactionally and don't care if it takes ages to do so?</li>
	<li>The data that you store - is it broken down or do you have to break it down before you save it?</li>
	<li>If the data is broken down, do you want to reconstitute it later?</li>
</ul>
I have just scratched the surface here - you can find many such questions. Lets take an example - lets say you are building an application to create users and manage users in a big platform of some sort. When you add a user to it, you expect  the user to get an email with a password and be able to login immediately. It would not make sense if the email went like an hour after you created the user or the user registered himself. That sort of turnaround can be achieved even if you had the user call up and someone manually created the <a  title="User identifier" href="http://en.wikipedia.org/wiki/User_identifier" rel="wikipedia">user ID</a> at their leisure and then sent the email. You want the <a  title="Registered user" href="http://en.wikipedia.org/wiki/Registered_user" rel="wikipedia">user registration</a> to complete within the few minutes that the user or the admin spends on the application. You need transactionality - plus, if the data was to be replicated across many instances of your application you would expect that it happens fast too. You wont like it if you registered on Facebook but could not login because you were redirected to a server where the data has not been replicated yet.

Lets take another case, you built a large messaging platform where millions of messages flow around. Each message has to be recorded for audit purposes. If the sender and receiver wait for a message to be recorded before they process it, then there will be a delay. So you send the message to a queue to get recorded asynchronously. The data can be recorded slowly as long as it is not lost - the main aim here is to ensure that the message taken off the queue is written to the database and not lost in between. Same as above case, but here it doesn't matter if the message gets written an hour after it is published as long as it is written correctly. Similarly, if  the message is not seen in all the downstream applications for a few hours more, then it doesn't matter either.

As you can see data storage use cases can be categorized into two - one where storing and replicating everywhere immediately  is important, and one where storing is important and it doesn't matter how long it takes to do so.

See this good article on this - it explains it in a more detailed manner - <a href="http://arstechnica.com/business/news/2012/01/the-big-disk-drive-in-the-sky-how-the-giants-of-the-web-store-big-data.ars">http://arstechnica.com/business/news/2012/01/the-big-disk-drive-in-the-sky-how-the-giants-of-the-web-store-big-data.ars</a>
<h2>ACID and BASE - they are not related to <a  title="SQL" href="http://www.iso.org/iso/catalogue_detail.htm?csnumber=45498" rel="homepage">SQL</a> or NoSQL</h2>
ACID (Atomic Consistent Integral Durable) and BASE (Basically Available Soft state Eventually Consistent) - these two acronyms are exactly the two scenarios that I explained above.

These are not new great inventions, they are what we would expect to happen with our data.

As you can see, there is no relation between how we want to store the data and SQL. It is common that we store all transactional data in RDBMS which provide an SQL interface. We built something that stored objects in Berkeley DB JE, a file based object database with no interface except an API. We ensured that each key-value pair that we stored was written to disk and replicated on <a  title="SRDF" href="http://en.wikipedia.org/wiki/SRDF" rel="wikipedia">SRDF</a> and was guaranteed to be recovered.

Similarly we wrote stuff to a Hibernate cache and let it write to the DB when it felt like writing - no hurry. We read from the cache and get on with our work and by end of the day the data will be in the database for tomorrow.

ACID and BASE can be achieved irrespective of whether we use the old RDBMS or the new NoSQL database.

The only thing that is characteristic about NoSQL database is that as the name suggests, you don't have to write any SQL. Whew! No need to worry if your procedure will compile on both Sybase and Oracle.
<h2>So tell me again why should I use NoSQL is it is not for BASE?</h2>
<a  title="Object-relational mapping" href="http://en.wikipedia.org/wiki/Object-relational_mapping" rel="wikipedia">Object Relational Mapping</a> (ORM) - this was created in response to the requirement that you needed to store beans in database. Let me rephrase that - because you needed to store business entities in a database. If you have done any simple OOP programming, you know that there are classes that encapsulate the attributes and behavior of everyday business objects. We all know the car example of OOP.

When you build an application - web or desktop or server - for a business, it deals with a certain problem domain and there are entities from that domain that need to be represented and processed in the application. This could be books in a library, items in an inventory yada yada.

So you represent a entity in you application as a class with public properties or a bean with getter and setter methods and use it. But when it comes to putting it in a DB you have to convert it to tables! Hmm - Ok we will write a stored procedure and pass all properties to the procedure as parameters. That will solve it.

Turns out that many years ago, it solved the problem, but the problem became unmanageable because each time you had to change you entities, you had to rewrite your procedures and re-map the parameters. Hmm .. there should be an easy way and so ORM were born. They would take your classes and some <a  title="XML" href="http://en.wikipedia.org/wiki/XML" rel="wikipedia">XML</a> definitions of mappings between the <a  title="Class (computer programming)" href="http://en.wikipedia.org/wiki/Class_%28computer_programming%29" rel="wikipedia">class attributes</a> and table columns and manage everything about storage for you. Since they knew the mappings, they could easily store the data generating necessary SQL dynamically. Added a new attribute? No problem add it to the XML and the column is created and populated with a default value and updated each time the object is saved.

But even this does not work well always - ORM has to decompose your objects to rows in a table. What if you wanted to reconstruct the object - ORM does this but there is cost. What if you wanted to do all this very fast - there is caching. Even faster? It will go and work but will fail at some point.

Again, lets say you designed a good hierarchy of classes and used ORM to create tables etc. Now along came a change request which required you to make some changes which break your current setup - you need to rebuild your tables and data!

No sane manager would like to go to the business and tell them that you will rebuild the database just because there is a small change request. So you will put in things like custom SQL over ORM and other sch stuff which will eventually turn it into a mess.

So - getting back to my point - why NoSQL?. If we could store all these objects, business entities directly in a database - without having to decompose them to tables and retrieve them as it is then would it not be much easier to manipulate them? You added a new attribute to a class then there is no need to add  a column, update indices or create XML mappings. There will be the question of taking all the old objects and updating them with this new field - but that can be solved easily. If you used something like Google Protocol Buffers then it can manage object versioning for you and you don't need to update all those old objects.

Now this is a good use case.
<h2>The real power of NoSQL databases</h2>
The real advantage of NoSQL databases is not that they are BASE or ACID or web scale - the advantage is that they let you retrieve and manipulate objects. These databases still store data in collections , as key-value pairs and even have indices to help fast search. They offer many of the advantages of the RDBMS world, but they let you manipulate objects.

Since you manipulate objects in the code and store them as it is in the database, no time is spent in converting from one format to another format. This speeds up applications. If you extracted an object from the database and performed an operation on it to change its state, then when you save the object back, the state is implicitly stored. You do not have to store the state separately.

For example, lets say you created a user object to store in the database. The user is not yet active because he has not clicked the activate link. If you used an object database, then you can store the inactive flag and the state that the activation is not complete on the same object. You do not have to store them in separate tables - this is needed in some cases.

If you had to store object trees like a user object with a list of all entities he owned or the user profile image as an image field on the user object - then you can easily store and retrieve the data from a NoSQL database without an ORM or having to put the file name in database and the file somewhere else on a file system.
<h2>Problems with NoSQL</h2>
Of course there are problems. NoSQL databases do not allow joins for example. But if you think about it, RDBMS does not allow joins either. It is the SQL engine that takes your join query and parses it to get data from different tables and then filters it as per your requirements in the join conditions.

So, if you want joins in NoSQL - then you could write that into your framework or build a library to be used by everyone. This is actually available in some of frameworks out there.
<h2>So where does BASE come in? Or ACID?</h2>
Like I said before, ACID and BASE are just how we want to save the data. Like it is mentioned in the link I gave in the beginning, there are ACID NoSQL databases. They just guarantee that data will be written to disk before the call to save() returns. Even in those cases where the database is a BASE compliant one, you can choose whether you want to ensure data is stored or not. If the database is clustered, then you can choose how many copies are made.

So it is possible to build ACID or BASE compliance into a NoSQL database.
<h2>Conclusion</h2>
ACID vs BASE and SQL vs NoSQL are two different comparisons and two different problems. They should not be mixed or confused. There is not reason to say that NoSQL should not be ACID or that SQL or RDBMS databases should not be BASE. You need to choose the database as SQL or NoSQL based on how you want to use the data. Once you do that, you should choose what kind of data storage use case you are interested in and then decide to make your system ACID or BASE.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://developers.slashdot.org/story/11/11/16/1948253/first-look-oracle-nosql-database">First Look: Oracle NoSQL Database</a> (developers.slashdot.org)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.diversity.net.nz/survey-shows-increased-adoption-of-nosql/2012/02/08/">Survey Shows Increased Adoption of NoSQL</a> (diversity.net.nz)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.rackspace.com/cloud/blog/2011/10/07/nosql-or-sql-do-you-have-to-choose/">NoSQL or SQL? Do you have to choose?</a> (rackspace.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://go.theregister.com/feed/www.channelregister.co.uk/2012/02/07/relational_databases/">Cloud proves that OldSQL is still cool</a> (go.theregister.com)</li>
</ul>
---
layout: post
title: "Passing entities between two systems that do not allow the same set of operations"
date: 2012-11-09 00:47
comments: false
categories:
---

<div class="mceTemp"></div>
If you have ever worked on any sort of <a  title="Adapter" href="http://en.wikipedia.org/wiki/Adapter" target="_blank" rel="wikipedia">adapter</a> - a piece of <a  title="Source code" href="http://en.wikipedia.org/wiki/Source_code" target="_blank" rel="wikipedia">code</a> that takes entities from a particular system and then makes it available to a different system - then you definitely must have come across a situation where the two <a  title="System" href="http://en.wikipedia.org/wiki/System" target="_blank" rel="wikipedia">systems</a> do not support the same set of operations, or mutations on the entity. There will be string reasons for this, and since it is two different systems there will be another thousand reasons why each was built the way it was built.

But none of these matters to the requirements of the adapter and you will eventually come up with a way that all the operations from the source system can be propagated to the target system. If the adapter is one way, then life is sometimes easier.

This post is an attempt to understand how we can handle these cases and what kind of issues we can run into.


<h2>Why do differences arise and what sort of cases can we run into</h2>
Why do differences arise? They usually arise because of the way the business requirement was analyzed and the design decision that was taken when building the system. Lets take the example of <a  title="Bookkeeping" href="http://en.wikipedia.org/wiki/Bookkeeping" target="_blank" rel="wikipedia">book keeping</a> - accounts. If I made an entry in a book saying I have a credit of 100$, and I want to correct it, then ideally I need to cancel this old 100$ entry, and then make an entry for the new amount, lets say 150$. If you were manually making a <a  title="Book entry" href="http://en.wikipedia.org/wiki/Book_entry" target="_blank" rel="wikipedia">book entry</a> with pen and paper, you can either strike off the old entry and make a new one, correct the old entry by overwriting or make a debit of 100$ and then a credit of 150$. While the last case implicitly keeps an audit of what was changed when, the first two cases require that you somehow maintain the audit trail using maybe a side note.

So, if we are building a software for this we see that there is a decision that needs to be made about audit trail. And we also need to make a decision about how we will support the editing of the value - even you need an audit, you can edit the value in place and log an audit entry; or you can delete/cancel the entry and then make a new entry.

Thinking in OOPS and ACID terms, it is either an update to an existing object or table row or the creation of a new object or table row with an update to the existing one marking it as cancelled.

If we build a system that follows the update model and then build another one that follows the make a new entry model, then we have a difference. If we were propagating changes from the update in place system to the one that made new entries, since the source system is doing less operations, and on the same object or row, we can easily fit it into the target system. On the other hand, if the source was the system where we made a new entry and that was being propagated to the system that updated in place, then you need to somehow tell the target system that this new update is related to an old record which must be updated in place!
<h2>These are nothing but interface definitions!</h2>
Yes - these rules about one change creating many changes or many changes converging into one change when moving data between systems is nothing but a definition of the interface between the two systems. There are many ways to represent this in <a  title="XML schema" href="http://en.wikipedia.org/wiki/XML_schema" target="_blank" rel="wikipedia">XML schema</a> like the <a  title="Electronic data interchange" href="http://en.wikipedia.org/wiki/Electronic_data_interchange" target="_blank" rel="wikipedia">EDI</a> format or using logic implemented as code in the adapter.

It is possible to build rules that cover 99.999% of all cases that might arise. Developers and architects usually do a pretty good job and there are systems that work perfectly fine for many many years.

After all that is what adapters are built for.
<h2>But we do run into non standard <a  title="Use case" href="http://en.wikipedia.org/wiki/Use_case" target="_blank" rel="wikipedia">use cases</a></h2>
Things can easily go wrong. If we take the example where the multiple entry book keeping approach was built using an <a  title="Relational database management system" href="http://en.wikipedia.org/wiki/Relational_database_management_system" target="_blank" rel="wikipedia">RDBMS</a> - then for each entry in the book we can assume there is a row in the database. So for the 100$ credit that was cancelled, we can do two things
<ul>
	<li>Make an entry when the credit was done, and upon debit update the same entry as cancelled</li>
	<li>Make an entry when credit was done and when the debit is done, make a new entry but in opposite direction</li>
</ul>
Second approach is similar to recording events on an entity rather than updating the entity itself - this is the kind of thing that is called temporal data or temporality of data.

First approach is the more traditional approach.

Both have weaknesses, but the most obvious one is with the key used in the first approach. Lets say you made and cancelled a 100$ credit in the book and then you made a credit for 150$. One of the fields was used as a key. Now you realize that the 150$ credit was incorrect and 100$ one was indeed correct, then after you mark the 150$ as cancelled, you can either go and update the old 100$ entry and make an <a  title="Audit trail" href="http://en.wikipedia.org/wiki/Audit_trail" target="_blank" rel="wikipedia">audit log</a> somewhere. Or you can try to insert a new row, using the same field as the key.

If you did the second approach, you will get a duplicate key error.

So while you handled he update in one direction, a repeat update to restore the data back was not possible. If an adapter was doing this between two systems, then they are out of sync - and the implications can be pretty much anything depending on how critical the applications are.
<h2>This is what is called a conflict, and we should avoid them</h2>
Conflicts are common in any application that processes a lot of data or processes data in a time sensitive manner. When such a process involves a data transformation then there will be more conflicts. Trying to eliminate conflicts totally will only work so far and after that will give you grief. Its not possible to cover all cases no matter how much testing and what kind of testing you do.

The smart way to fix these is to setup monitoring and exception rules such that these conflicts are raised and flagged as soon as they happen. You then need the means to correct these and less red tape to get in your way of correcting these.
<h2>Conclusion</h2>
We have seen by means of an example just how easily we can have conflicts between two systems exchanging data when they both don't allow the same set of operations or follow the same approach to mutate the entity. These conflicts cannot be avoided, but they can be easily rectified with proper monitoring.
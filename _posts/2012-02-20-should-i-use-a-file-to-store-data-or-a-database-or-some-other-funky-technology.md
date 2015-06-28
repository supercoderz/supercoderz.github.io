---
layout: post
title: "Should I use a file to store data? Or a database? Or some other funky technology?"
date: 2012-02-20 00:03
comments: false
categories:
---

No matter what technology you use, you always have to work with files - source code, compiled binaries, configurations etc. File is ubiquitous when you work with computers. But then have you ever suggested to someone that you should probably store certain data in a file instead of that funky-new-storage-application because it will be simpler? And having done that have you ever heard them saying - ' who uses files anymore?'.

I am guessing your answer will be yes. I have heard that too. It is surprising that so many people consider using files as not a great approach compared to say using a <a  title="SQL" href="http://www.iso.org/iso/catalogue_detail.htm?csnumber=45498" rel="homepage">SQL database</a> or something like that.

So why do we think that way? Are files really so un-cool and out of fashion?

<!--more-->
<h2> What does a file provide?</h2>
A file provides a very basic interface - open it, write some bytes, close the file. The data will be written to disk and will be available when you open it again. Nothing more. A file doesn't care what the bytes you write into it, and it doesn't try to fit those bytes into any structure that it enforces. So, lets say you wrote a set of alphabets into a file, then it doesn't care what they are or if they make a meaningful word or sentence. It ensures the data is correct, and you worry about how to interpret the data in your application.

This simple interface is not sufficient for use in applications. Applications will have to build logic that can understand the data in the file or convert data into a format that can be written into the file. Also, there is a limitation that you can either write or read a file but not both at once. And this has to be done from only one thread.
<h2>Working around the problems with a file</h2>
The problems with using a simple file can and have been overcome - the fact that we have all these applications today is because we managed to solve the problems of using a simple file. And we solved it very well. You will not see a library out there which solves this problem for you by letting you write anything to a file or read any format of unknown data from a file. Instead you will see that each programming language provides the ability to serialize and de-serialize the data to and from files. This is then applied to complex structures like object trees, collections etc to store them in files and read back successfully.

There are libraries like Google Protocol Buffers and <a  title="Apache Thrift" href="http://thrift.apache.org/" rel="homepage">Apache Thrift</a> etc that can create a cross platform and cross language serialization of data. These libraries work on simple primitive types or bytes and create higher level data structures. The library ensures that whatever data you give it can be correctly written and retrieved so that you can work with building the application.

The problem with reading and writing to a file at once has been solved - you store the file as chunks, each chunk of fixed size in a separate file - that way you can read the chunk that represents the start of the file while you are still writing the chunk that represents the end of the file. All this low level chunking is transparent to you because of the library. Google's BigFile or <a  title="Hadoop" href="http://hadoop.apache.org/" rel="homepage">Apache Hadoop</a> are based on these principles.
<h2>Hang on why the refresher about all the file stuff that we all know?</h2>
OK, so maybe you know all that I told so far. So why the refresher? Just to refresh the basic issues.

So we grew from the medieval times of using raw files to applications that do cool things with files. In the end the tables of your database or the versions of code in your distributed version control system are stored in files. Even the million dollar per year licence enterprise software that you use is based on files, but in a way that is transparent to you.

But the point I am trying to make here is that files had a simple interface that was cumbersome, but fast. We built large applications with custom formats and logic using files to store data. These provide consistency and reliability and they work well. But they have an overhead. And sometimes that overhead is important. In those cases, it might be important to throw away all the applications and work with the files directly.
<h2>So why would we like to dump the reliability of all these applications and the standard formats?</h2>
Microsoft Excel is a cool tool - I have seen people use this on trading floors to do some awesome stuff. It is an advanced version of spreadsheets like <a  title="Lotus 1-2-3" href="http://www.ibm.com/software/lotus/products/123/" rel="homepage">Lotus 123</a> and is by far the best way to store and view tabular data. But still, when it comes to processing data or dumping data, people tend to use the CSV format - simple comma separated data as opposed to <a  title="XML" href="http://en.wikipedia.org/wiki/XML" rel="wikipedia">XML</a> format of <a  title="Microsoft Excel" href="http://office.microsoft.com/en-us/excel" rel="homepage">XLS files</a>.

The reason for this is that if you want to read an XLS file using a program other than Excel and manipulate the data or load it to the DB, spending time to parse the XML and understand it is a waste of time. Also, if the XML got corrupted, then there is no easy way to read it and fix it. But if you take a <a  title="Comma-separated values" href="http://en.wikipedia.org/wiki/Comma-separated_values" rel="wikipedia">CSV file</a>, all you need to do is read a line and look for comma's. If the data got corrupted, you could delete a few lines and process the rest - you can delete the lines safely from a notepad!

Conversely, if you want to dump data from a DB to Excel, it is far more easier to dump it to CSV rather than trying to write the XLS format.
<h2>Surely reliability is more important too</h2>
Yes reliability is more important. Unless you are building a trivial application that no one will ever use, there will be responsibility on your part that data that is stored is correct and can be read back correctly.  Databases and standard application file formats were created to solve this problem.

There are means of encoding the file in such a way that the application opening it can check the correctness of the file. Each time you get a warning from an application that the file is incorrect is because the application is trying to recognize if the data is correct or not. This is also a security feature because you cannot make the application load malicious data into memory thinking that it was a valid file.
<h2>Can we get around these checks?</h2>
We tried this trick with <a  title="Microsoft Word" href="http://office.microsoft.com/en-us/word/" rel="homepage">Microsoft Word</a> few years ago where we opened a <a  title="DOC (computing)" href="http://en.wikipedia.org/wiki/DOC_%28computing%29" rel="wikipedia">DOC file</a> and added a few extra bytes at the end to see if the file would get corrupted. When we opened the file, Word was able to detect and ignore those characters at the end and we could still see the file. If we added those at any location in the middle of the file, then of course the file was lost for good.

In another case, we had large number of TIBCO BusinessWorks project files that needed to be updated with correct <a  title="XML Schema (W3C)" href="http://en.wikipedia.org/wiki/XML_Schema_%28W3C%29" rel="wikipedia">XSD</a> paths. Since TIBCO stores the files as XML, one of our teammates was able to write a quick Java program to read the files and do a string replace. And it worked.

These are cases where we were able to tweak the file within the limits of integrity, but this is not always possible, and for a good reason.
<h2>So where should we use simple files? Or why should we even use it?</h2>
Like I mentioned some paragraphs before, there is an overhead in using an application to store data in a reliable and consistent manner. This overhead is in the processing required to take the data and put it in the format that the application uses to guarantee reliability and then write to a file. This few hundred milliseconds or seconds of delay might not be important for the average application but for applications that have to process large amounts of data very fast or deal with huge amounts of data in every process it can add up and kill performance.

Imagine Google search being built on a database which becomes slow after a million records are added or after a few billion records are added. Its not a pretty thought. If you have ever tried running the 'grep' command in UNIX on a 10GB file, you would have noticed that it is fairly fast. It is faster than having to load that file in an editor and then searching. Google's BigFile works on something similar to work on small file chunks rather than a database.

Each use case demands its own approach.
<h2>Conclusion</h2>
So you can see that there is nothing wrong with using files - it depends on your application whether you want to use a basic file or an application to manage the storage of your data. If you use a file, then you will have to handle all the boiler plate code to handle the file. If you use an application to manage the storage, then you will have an overhead.

In most cases, it is not necessary to make this call early unless you are building a really high performance application. You can build out your application, check the performance of the application and them decide what to do.
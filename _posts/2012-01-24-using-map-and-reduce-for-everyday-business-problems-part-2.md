---
layout: post
title: "Using map and reduce for everyday business problems - part 2"
date: 2012-01-24 12:37
comments: false
categories:
---

In my <a title="map reduce part 1" href="http://supercoderz.in/2012/01/21/using-map-and-reduce-for-everyday-business-problems-part-1/">last post</a> I described what kind of scenarios in our everyday business problems can be solved using map and reduce - we can do this even though we don't have the kind of computing power that <a  title="Google" href="http://google.com" rel="homepage">Google</a> or Facebook have. In this post I will show how we can implement a map and reduce approach to solve a pseudo-real business problem in <a  title="Python (programming language)" href="http://www.python.org/" rel="homepage">Python</a>. Python provides in-built functions for map and reduce which we will use in this example.

<!--more-->
<h2>The problem</h2>
The problem that we will use is creating a large number of database records. We will use Python and <a  title="MongoDB" href="http://www.mongodb.org/" rel="homepage">MongoDB</a>. The scenario is creating a large number of entries in the database. This is something that we do each time we process a large input data set and import that into our database.

For simplicity sake, the input data is created as a range of numbers from 1 to 2000000. The setup consists of a Python script running on my core i5 laptop with Windows 7 and inserting data to a MongoDB on a Ubuntu <a  title="Virtual machine" href="http://en.wikipedia.org/wiki/Virtual_machine" rel="wikipedia">virtual machine</a>. The virtual machine runs on 500 MB RAM with one core, and the other 3 cores are free for the Python script.

We will do this in three ways - do all 2000000 in a sequence, process the records with normal map in sets of 10000, process the records in sets of 10000 using Pool.map.
<h2>Approach 1 - In sequence</h2>
The code for this is as follows
<pre>    from pymongo import Connection
    from time import time
    from random import random
    #create these upfront
    db=Connection('192.168.1.29',27017)
    entries=db.entries.entries
    def create_entry(name):
        return entries.insert({'name':name,'value':random()*100})
    def concatenate(x,y):
        return x+y if x else y
    if __name__=='__main__':
        num_positions=2000000
        names=range(1,num_positions)
        start=time()
        positions=[create_entry(name) for name in names]
        print time()-start</pre>
Its a very simple case - we create a list of numbers and insert them one after the other into the database. On my laptop this took average of 254 seconds on 3 runs. By the third run my MongoDB had 1 GB of data. This definitely has scope for improvement.
<h2>Approach 2 - using map and reduce</h2>
For this, we will use the simple map and reduce methods in Python.We will divide the data into sets of 10000 and pass them to map.
<pre>    from pymongo import Connection
    from time import time
    from random import random
    #create these upfront
    db=Connection('192.168.1.29',27017)
    entries=db.entries.entries
    def create_entry(name):
        return entries.insert({'name':name,'value':random()*100})
    def concatenate(x,y):
        return x+y if x else y
    if __name__=='__main__':
        num_positions=2000000
        step_size=10000
        steps=int(num_positions/step_size)
        names=range(1,num_positions)
        start=time()
        positions=[map(create_entry,names[step_size*i:step_size*(i+1)]) for i in range(0,steps)]
        result=reduce(concatenate,positions)
        print time()-start</pre>
This version is better at performance - this runs on my laptop in 220 seconds on average in three runs. This is on top of all the data inserted in the previous run.
<h2>Approach 3 - using Pool.map from multiprocessing</h2>
This third approach uses the Pool.map function from multiprocessing. This is a parallel version of map that distributes the load over all the cores.
<pre>    from pymongo import Connection
    from multiprocessing import Pool
    from random import random
    from time import time
    #create these upfront
    db=Connection('192.168.1.29',27017)
    entries=db.entries.entries
    def create_entry(name):
        return entries.insert({'name':name,'value':random()*100})
    def concatenate(x,y):
        return x+y if x else y
    if __name__=='__main__':
        num_positions=2000000
        step_size=10000
        pool=Pool(3)
        steps=int(num_positions/step_size)
        names=range(1,num_positions)
        start=time()
        positions=[pool.map(create_entry,names[step_size*i:step_size*(i+1)]) for i in range(0,steps)]
        result=reduce(concatenate,positions)
        print time()-start</pre>
This one ran on my laptop in 182 seconds on average in three runs.
<h2>Analysis of results</h2>
As you can see there is an improvement by parallelizing the task over all the cores. In a proper setup, MongoDB would be running on a proper machine instead of a virtual machine with more RAM and would be performing better. With 3 cores for Python and one core for MongoDB, under limited RAM we could see a difference of 60 seconds on the processing time. This will get amplified on better machines.

Using this approach might not give you a large amount of improvement in one shot - you would need to customize to suit your needs. For example, you could use a pool of connections instead of one connection like I did, you can create a connection for each map - I had issues with Windows TCP buffers when I did that, you could increase the batch size. You could even try batch updates on the database.
<h2>Conclusion</h2>
This post merely shows that you can use map and reduce functions in Python along with multiprocessing to get better results. You can extend this to get desired results on your application on your setup.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://supercoderz.in/2012/01/21/using-map-and-reduce-for-everyday-business-problems-part-1/">Using map and reduce for everyday business problems - part 1</a> (supercoderz.in)</li>
</ul>
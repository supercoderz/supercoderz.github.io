---
layout: post
title: "Using map and reduce for everyday business problems - part 1"
date: 2012-01-21 11:05
comments: false
categories:
---

<div class="mceTemp"><a href="http://www.flickr.com/photos/26672996@N07/4861146813"><img  title="MapReduce" src="http://farm5.static.flickr.com/4119/4861146813_c915e6639e_m.jpg" alt="MapReduce" /></a></div>
Most of us have heard about <a  title="MapReduce" href="http://en.wikipedia.org/wiki/MapReduce" rel="wikipedia">MapReduce</a> - the <a  title="Google" href="http://google.com" rel="homepage">Google</a> framework for solving problems in parallel - which is based on their <a  title="BigTable" href="http://en.wikipedia.org/wiki/BigTable" rel="wikipedia">BigTable</a> and <a  title="Hadoop" href="http://hadoop.apache.org/" rel="homepage">Hadoop</a> which is an open source implementation of MapReduce. These are tools that are used by Google and Yahoo and many other companies to build scalable websites and all that. All these need many nodes to be setup and work load distributed across these nodes to get the results that we want.

But in everyday life we hardly ever solve problems of this scale - and we don't need or cannot afford to pay for so many hundreds of nodes for our work. Not with the current cost cutting. Does that mean we cannot use these cool tools? No we can,  map and reduce are paradigms borrowed from <a  title="Functional programming" href="http://en.wikipedia.org/wiki/Functional_programming" rel="wikipedia">Functional Programming</a> which Google built into a very efficient state. We can use any language, like <a  title="Python (programming language)" href="http://www.python.org/" rel="homepage">Python</a>, that supports map and reduce and achieve the same kind of parallel processing - albeit on a single server spread across all the cores.

So how can we do it? Where is it not possible to use this?

<!--more-->
<h2>A bit of a background</h2>
This blog post by Joel Spolsky from way back in 2006 is a very good explanation of map and reduce fundamentals -

<a href="http://www.joelonsoftware.com/items/2006/08/01.html">http://www.joelonsoftware.com/items/2006/08/01.html</a>

What it says is this - if you have piece of code that needs to be applied to a long list of objects, and for each object, there is no external state that is required from the processing done on the previous object, then you can use <a  title="Map (higher-order function)" href="http://en.wikipedia.org/wiki/Map_%28higher-order_function%29" rel="wikipedia">map function</a> to achieve that.

If you have a set of results from some operations that you want to aggregate into a single result by summation or appending to a list of formatting to <a  title="XML" href="http://en.wikipedia.org/wiki/XML" rel="wikipedia">XML</a> - and this can be done using a common piece of code - then you can use reduce function to do this.

Now, if you have a combination of map and reduce and the ability to create that long list of items or objects that need to be processed, then you can use map-reduce to speed up your work.
<h2>So where can we use it?</h2>
If we think of what we do on our jobs daily, there are many cases where we take a list of entities from our business model and do something on them. And in some cases we collect the result of all these and send that out.

Example scenarios include
<ul>
	<li>A bank evaluating all their positions in a particular security at end of day based on the close price</li>
	<li>A retailer going through all items in inventory and updating them in some way or getting the details like amount in stock etc.</li>
	<li>Someone processing a large number of files or records to identify a pattern</li>
</ul>
These are scenarios that occur commonly. We can use the map and reduce approach on these problems.

Once we built a messaging system that required that each message that was sent was archived for 2 years. We used <a  title="Berkeley DB" href="http://www.oracle.com/us/products/database/berkeley-db/index.html" rel="homepage">Berkeley DB</a> to store these messages on a disk. The service would go though each message on a special queue and try to write it to disk. This was a serial process because it was in Java. We could have used map and reduce to run this operation on all the 8 cores on the server if we were using Python or if Java supported map and reduce. Hadoop would have been too much for our use.
<h2>Where can we not use it?</h2>
Since we do not work in Google and our work does not involve searching the whole internet and indexing the pages - which obviously does not depend on which page gets indexed when - we will have cases where we cannot use map reduce. These are cases where the order of performing the operations matters.

For example, in the same scenario that I explained above, if a requirement was added that no message should be lost and that you should not pick the next message until the previous one was written to disk, even then we could use map and reduce and ensure that <a  title="Propositional formula" href="http://en.wikipedia.org/wiki/Propositional_formula" rel="wikipedia">the map method</a> has retry code.

But, if the sequence of writing these messages mattered - it could matter for example if you were counting acknowledgement messages for each of these message. In that case we would not be able to use map and reduce.
<h2>It is not the silver bullet</h2>
Even in scenarios where it can be used, map and reduce is not the silver bullet. There is an overhead in splitting a task into so many parts and ensuring that these can execute independently of each other. Also, when a task is broken into too many pieces or too parallelized, it might actually run slow - Amdahl's Law.

So it might not be possible to get the optimization that we want by simply using map and reduce.
<h2>Follow up</h2>
In my next post, I will post some examples for the scenarios that I described above to show how we can use map and reduce in our daily jobs.

&nbsp;
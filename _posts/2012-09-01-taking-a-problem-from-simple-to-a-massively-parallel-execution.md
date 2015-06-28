---
layout: post
title: "Taking a problem from simple to a massively parallel execution"
date: 2012-09-01 23:57
comments: false
categories:
---

[caption id="" align="alignright" width="300"]<a href="http://commons.wikipedia.org/wiki/File:Data-Centric_Figure2.jpg" target="_blank"><img  title="English: Pig program translation to MapReduce" src="http://upload.wikimedia.org/wikipedia/commons/thumb/4/41/Data-Centric_Figure2.jpg/300px-Data-Centric_Figure2.jpg" alt="English: Pig program translation to MapReduce" width="300" height="183" /></a> English: Pig program translation to MapReduce (Photo credit: Wikipedia)[/caption]

Distributed computing and <a  title="Parallel computing" href="http://en.wikipedia.org/wiki/Parallel_computing" rel="wikipedia" target="_blank">parallel computing</a> used to be something I considered very very high tech stuff that I was not working on. But over the years I figured out that what I was working on some of these - without me knowing. Early on I realized that I was building <a  title="Distributed computing" href="http://en.wikipedia.org/wiki/Distributed_computing" rel="wikipedia" target="_blank">distributed systems</a> when I got to work on a redesign of a messaging layer at an investment bank - the amount of components that touched the messages and the whole way in which we distributed the logic and load across a set of services made me realize that what I had worked on previously was also something similar - only that I had called it <a  title="Service-oriented architecture" href="http://en.wikipedia.org/wiki/Service-oriented_architecture" rel="wikipedia" target="_blank">Service Oriented Architecture (SOA)</a>.

But it also made it clear to me that parallel processing or parallel execution (or grid computing as some call it) is different from distributed computing - and that it involves a very key point - that you need to break the problem in such a way that it can be worked on by different worker nodes at once.

Without proper guidance and when you are not a Computer Science graduate, you take time to understand things - but this article bu <a  title="Joel Spolsky" href="http://joel.spolsky.com" rel="homepage" target="_blank">Joel Spolsky</a> made it very very clear to me - <a href="http://www.joelonsoftware.com/items/2006/08/01.html">http://www.joelonsoftware.com/items/2006/08/01.html</a>. It is an awesome piece that I first read in 2008. This post is basically a run down of how I interpreted this article in my head and how I think of solving a problem.

<!--more-->
<h2>How we solved a simple problem before we learnt to say OOPs</h2>
I started programming with <a  title="GW-BASIC" href="http://en.wikipedia.org/wiki/GW-BASIC" rel="wikipedia" target="_blank">GWBASIC</a> and <a  title="QBasic" href="http://www.microsoft.com/" rel="homepage" target="_blank">QBASIC</a>, and then moved to C before I fell in love with Java. In BASIC and C, and <a  title="Python (programming language)" href="http://www.python.org/" rel="homepage" target="_blank">Python</a> which I am beginning to love these days, if you write a piece of code using the procedural paradigm,  all you are about is the parameters that are being passed in, and the result that you return or the exception that you throw. You have to probably have to look up global variables for constants and shared values; and very very rarely you write to global variables to store results. You would rather use a file or some such shared location to store results if they cannot be simply returned. But using something like a struct in C, you could potentially return any damn data format you wanted to.

Lets take an example - calculating the compounded future value of an investment after a term t at an interest rate r. If I were to write this as a function in Python, this is how I would write it
<pre>def future_value(present_value=1,rate=0,term=1):
    fv = present_value*math.pow(1+rate,term)
    return fv</pre>
Now, if I was just a person who wanted to know how much my 1000$ invested at 1.5% for an year would earn, I would just call this with the values and I will get an answer. Now that is a simple requirement which probably did not even need a program, let alone software at a large scale to solve it - you can do that with a calculator. But if we take a bank for example, then this has to be done for many terms or for many principal values and so they will need  a way to do it again and again.
<h2>Solving a simple problem again and again</h2>
We will formally dump OOP here - not that I have anything against what I use everyday - I just feel that it has too much baggage and cannot fir what I am trying to explain here.

Lets say a bank has decided to invest 100 million USD in a risk free fixed deposit. It can do so from terms as small as 3 months, to as high as 10 years. Each term has an interest rate and the bank wants to know how much it will make at each term. In pure banking terms this is called valuing a position at different tenors - although the example position I took about investing 100 million USD in a fixed deposit will probably make me a laughing stock among bankers - but my purpose is to explain how to parallelize a problem and not to make money.

So anyways, speaking in Python terms, given a list of tuples representing the term and the interest rate of the term, and knowing that the principal is 100 million USD - we can get a list of returns for each term as follows -
<pre>def get_future_values(present_value,data):
    future_values = []
    for term,rate in data:
        future_values.append(future_value(present_value,rate,term)
    return future_values
data = [list of (term,rate) pairs]
present_value=100000000
print get_future_values(present_value,data)</pre>
Now that is one way of solving a problem again and again. Good.

Except banks have thousands of such positions or lets say thousands of <a  title="Present value" href="http://en.wikipedia.org/wiki/Present_value" rel="wikipedia" target="_blank">present values</a> for which they need future values. We can do that too, just another <a  title="For loop" href="http://en.wikipedia.org/wiki/For_loop" rel="wikipedia" target="_blank">for loop</a>.
<pre>data = [list of (term,rate) pairs]
present_values=[ large array ]
for present_value in present_values:
    print get_future_values(present_value,data)</pre>
OK that works!
<h2>OK but there is still a problem</h2>
If you have done a decent amount of programming then you will know that doing things sequentially in a loop one after that other is slow and also the looping kills the computer if you don't do it right. In order to avoid this they created multi-threading where you start many small processes within the main process and they do the job together sharing the same processor. You split the work between the processors so it goes faster. This is not truly parallel yet, but can be considered parallel within the process. And it is not easy to implement too - you need to ensure that the task does not depend on any external common resource that all threads will wait for and choke on. Our simple problem does not have this issue, and lets say we had to split this into two threads, we will do it as follows
<pre>def process_present_values(present_values,data):
    for present_value in present_values:
        print get_future_values(present_value,data)

set1 = list_of_present_values[0:half]
set2 - list_of_present_values[half+1:end]

t1=<a  title="Thread (computer science)" href="http://en.wikipedia.org/wiki/Thread_%28computer_science%29" rel="wikipedia" target="_blank">Thread</a>(process_present_values(set1,data))
t2=Thread(process_payment_values9set2,data))
t1.start()
t2.start()
t1.join()
t2.join()</pre>
Now this will cause the execution to be a bit ore faster. But still not super speed - so if the present values list itself was a few million or if data was a large set of terms and rates or if you needed to do this for each present value with a list of data arrays? You can get creative and it will get complicated and it will eventually slow down.
<h2>Multiprocessors - on the same machine</h2>
This is like the first level of making it parallel - you have a server with lot of processors or processor cores - and as opposed to starting threads which are within a process and so run on the same core, you will create different processes which run on all of the cores of your server and thus will execute faster. In order to do this, you will use something called map function in Python. There is a non multiprocessing version of this too - and it is the same. All it does is take a function and a list of values and apply that function to all the values. Essentially it is for loop - but since you are not writing the loop, it saves you a few lines of code. And the language can decide how the for loop is built.

Now this part about for loops and how they can be implemented is what I had trouble figuring out and this is what the article by Joel Spolsky made it so clear to me. If I were to write the above for loop as a map, I would do
<pre>res=map(process_present_values,set1)</pre>
This is all I have done - if it were the normal map, then it will do basically the same thing that I did in the for loop. No difference. But let us say we used the multiprocessing map function - in that case Python will apply each function call as a separate process on a different core on the server - the runtime takes care of tracking and consolidating the results. And since you ran on all cores, if your server had 50 cores, then you can do the thing in 1/50th the time of doing it on one core.

And this is where the magic of parallel begins!

If you wanted to pass data between the various parallel executions - for other more complex logic problems - you have means like queues, databases, files etc. As long as there is no contention and choking on these, the processes will run as fast as they can and you will get the results very very quickly. This works even better if all the resources that need to be accessed by the code are accessed in a standard interface, and that interface itself can be run as multiple instances and abstracted as a pool.
<h2>Main requirements of making a task parallel on many cores</h2>
The main requirements that a task must have to be parallelized are -
<ul>
	<li>No global variables or state other than the input parameters or any common external storage like database, file or messaging layer</li>
	<li>No long running executions or loops</li>
	<li>No logic that needs to wait for a resource or requires multiple resources for long periods of time thus creating contentions and bottle necks</li>
	<li>Least possible dependency on things other than the parameters</li>
</ul>
If these are satisfied then you can run many instances of the task on many cores.
<h2>And now making it massively parallel on many computers</h2>
Once you have a task that can be run in parallel on many cores of a PC with no inter dependency, you can easily make it parallel on many many computers. It is just a matter of breaking the data into pieces and making sure that all the computers that have to execute this code get this data.

In summary you need to ensure that -
<ul>
	<li>All computers have access to the same version of the code</li>
	<li>All computers have access to the libraries that are needed for the code to execute</li>
	<li>All computers have access to the data in a common file or a database</li>
	<li>There is a master node or process with which all worker nodes communicate to receive task and post results</li>
	<li>Once the task is submitted, the master should be capable of identifying the input data and splitting it</li>
	<li>This split can be done using a user provided function</li>
	<li>Once a set of data becomes available the master should select a worker and give it the function name to execute and the data</li>
	<li>Once the worker is done, it should tell the master node that it is done and provide the results</li>
	<li>Once the master node knows that all the data is processed, it should consolidate and return the results.</li>
</ul>
This sounds tough to build right? You bet it is - there are many implementations like Hadoop, Google's MapReduce, Data Synapse and many such parallel grid platforms. Sometimes they come up with their own data storage systems like Google's BigTable or Hadoop FileSystem which are built in a way that they suit the processing platform. In these cases you just write the data to the files on the storage system and tell each task to process a particular file.
<h2>Conclusion</h2>
Building a parallel processing platform is tough - but as long as you can appreciate that in the end it just takes a function and applies it to a given data set, you can always design your code in such a way that it can be parallelized in a very short time.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://vikrantravi.wordpress.com/2012/08/26/parallel-programming-with-c-data-parallelism/" target="_blank">Parallel Programming with C# - Data Parallelism</a> (vikrantravi.wordpress.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://morepypy.blogspot.com/2012/08/multicore-programming-in-pypy-and.html" target="_blank">Multicore Programming in PyPy and CPython</a> (morepypy.blogspot.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.javacodegeeks.com/2012/08/what-makes-parallel-programming-hard.html" target="_blank">What makes parallel programming hard?</a> (javacodegeeks.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://insidehpc.com/2012/08/11/paper-massively-parallel-computing-on-cog-ex-machina/" target="_blank">Paper: Massively-Parallel Computing on Cog ex Machina</a> (insidehpc.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://disco.ethz.ch/lectures/podc_allstars/" target="_blank">Principles of Distributed Computing (lecture collection)</a> (disco.ethz.ch)</li>
	<li class="zemanta-article-ul-li"><a href="http://aws.typepad.com/aws/2008/11/parallel-comput.html" target="_blank">Parallel Computing With MATLAB On Amazon EC2</a> (aws.typepad.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://robklopp.wordpress.com/2012/08/07/who-is-massively-parallel-hana-vs-teradata-and-maybe-oracle/" target="_blank">Who is Massively Parallel? HANA vs. Teradata and (maybe) Oracle</a> (robklopp.wordpress.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.drdobbs.com/parallel/parallel-evolution-not-revolution/240005407" target="_blank">Parallel Evolution, Not Revolution</a> (drdobbs.com)</li>
</ul>
---
layout: post
title: "Idiots guide to building a compute grid"
date: 2011-07-26 00:26
comments: false
categories:
---

<div >



</div>
We all have heard about <a  title="Grid computing" href="http://en.wikipedia.org/wiki/Grid_computing" rel="wikipedia">grid computing</a>. If you have a computer science degree, then you probably have had a paper on this subject and maybe built or worked on a grid in your college. Grid computing can be simply defined as distributing a large <a  title="Task (computing)" href="http://en.wikipedia.org/wiki/Task_%28computing%29" rel="wikipedia">task</a> over a large number of computers so that they can all execute the task <a  title="Series and parallel circuits" href="http://en.wikipedia.org/wiki/Series_and_parallel_circuits" rel="wikipedia">in parallel</a> and we get the result quickly.  And we all know that grids are complicated - you have to create a task, you have to submit it to a master <a  title="Server (computing)" href="http://en.wikipedia.org/wiki/Server_%28computing%29" rel="wikipedia">server</a> that sends it to one of the many workers, it collects the results, checks for failures, automatically executes failed jobs, puts all required resources in shared locations, provides a monitoring console etc etc etc. In short, its not simple. It can be built by guru's and we use it.

From the point of view of a non computer science guy, this picture seems pretty reasonable to me. But is it really the case?

The only way to prove this to myself was to try it - and the following long blog post describes exactly what I did. By the end of this I will show you a simple grid that works and does a task in parallel. Read on to find out more.
<h3>Basic components of the grid</h3>
Before we begin, lets outline the key basics of a grid that we can read from any of the many books on this topic:
<ul>
	<li>The workers on the grid each execute a small and definite task that does not depend on other tasks and can be completed by itself</li>
	<li>The entire task is large enough and each individual task takes long enough time that it has to be executed in parallel to achieve best results.</li>
	<li>All the workers have access to all the <a  title="Library" href="http://en.wikipedia.org/wiki/Library" rel="wikipedia">libraries</a> that are required to complete the task.</li>
	<li>Any input that is required for the task can be received without a contention with any of the other workers.</li>
	<li>There is a master process that sends tasks to each worker and collects the results.</li>
</ul>
There are a few more, but we will limit ourselves to these key ones.
<h3><span class="Apple-style-span" style="font-size:15px;font-weight:bold;">Choice of technologies to build the grid</span></h3>
<div>Our aim is to show that we can build a simple grid - not to showcase the various grid frameworks out there. So in order to keep this very simple, we will build this in <a  title="Python (programming language)" href="http://www.python.org/" rel="homepage">Python</a>. The choice of Python is because it provides the necessary tools to build the server components without much effort using just the standard library that comes in the default installation and also provides good libraries for the other functionality that we need.</div>
<h3><span class="Apple-style-span" style="font-size:15px;font-weight:bold;">The problem</span></h3>
<div>We need a problem that is suitable to be run in parallel. Whatever the problem maybe, it has to be easily divisible into tasks. For our grid, we will use the following method as the task. This method calculates the sum of the first 'n' <a  title="Fibonacci number" href="http://en.wikipedia.org/wiki/Fibonacci_number" rel="wikipedia">Fibonacci numbers</a>. The implementation here is not the best approach - this is chosen because it takes a lot of time. This was taken from an example on the internet.</div>
<pre style="padding-left:30px;">def fibonacci(n):
    if n&lt;=1:
        return 1
    else:
        return fibonacci(n-1)+fibonacci(n-2)</pre>
This code works fast up to n=30, after that it takes a very long time to execute and trying to calculate the sum for anything above 30 is something we can do in parallel. You can try running this code in your Python editor and you can see how long it takes. If you do two calculations for n=40 one after the other then it will take a very long time to execute.

We will build a grid that can take multiple <a  title="Capital punishment" href="http://en.wikipedia.org/wiki/Capital_punishment" rel="wikipedia">executions</a> of this code and run them in parallel.
<h3><span class="Apple-style-span" style="font-size:15px;font-weight:bold;">The solution architecture</span></h3>
<div>We will build a server which will function as the worker. The job of the server will be to receive the task and execute it, and return the result. Simple. We will build this server using <a  title="XML-RPC" href="http://en.wikipedia.org/wiki/XML-RPC" rel="wikipedia">XML RPC</a> module which is part of the Python standard library. This server will:</div>
<div>
<ul>
	<li>Start on a given port and register a method which can be invoked remotely</li>
	<li>This method will accept the task and the value of n, and execute the code</li>
	<li>Once the task is completed, it will return the value.</li>
</ul>
<div>The <a  title="Client (computing)" href="http://en.wikipedia.org/wiki/Client_%28computing%29" rel="wikipedia">client</a> will be a simple XML RPC client that will connect to the server and submit the task. When we have more than one server, the client will submit the task to one of the many servers. The client will</div>
<div>
<ul>
	<li>Connect to a server using a proxy</li>
	<li>Create the task and send it to the server using the proxy</li>
	<li>Get results back from the server</li>
	<li>Submit each task in a thread so that it need not wait for completion before submitting the next task</li>
</ul>
<h3>How do we share the task from client to server?</h3>
We said we will create a task and send it to the server - but how can we do this? The immediate problems that we can think of are:
<ul>
	<li>The server should be able to identify and execute the code</li>
	<li>The server should have all the libraries that are required in the task</li>
</ul>
One way we can do this is that we ensure all the servers have the same libraries imported in their python shell, and we tell them what method to execute and with what parameters. This will work because if all the servers have the same code, then you tell which method to call and give the parameters and the runtime will be able to find the method. In Python this is simple to achieve because you can have global methods and you can easily call them.

</div>
However, we will take a different approach.

</div>
In python, functions are instances of the FunctionType - and they can be passed around like variables, just like function pointers. So, it is possible to define a function and pass it on to another piece of code which can then execute this function. That is very easy to do when all the code is executing in the same Python shell.

In our case we want to send the code over the network to a server and have the code execute there. So this will require special handling before we can send the code. In order to achieve this, we will use the marshal module in Python. This provides a means to serialize the function into a byte stream. This can then be sent over the network and again reconstructed into a function. As long as the function is self contained and does not have any references to code that is not commonly imported in both the client and server or recursive calls to itself, it can be executed without issues.

If you wanted to write a function to a file, read it back and then execute it - that's possible too. The following example shows how this can be done.
<div>
<pre style="padding-left:30px;">import marshal,types,base64

def fibonacci(n):
    if n&lt;=1:
        return 1
    else:
        return fibonacci(n-1)+fibonacci(n-2)

def persist():
    code_string=marshal.dumps(fibonacci.func_code)
    f=open('/tmp/fibonacci','w')
    f.write(base64.b64encode(code_string))
    f.close()

def extract_and_execute(n):
    f=open('/tmp/fibonaaci','r')
    code_string=base64.b64decode(f.read())
    f.close()
    code=marshal.loads(code_string)
    func=types.FunctionType(code,globals(),'fibonacci')
    return func(n)

if __name__=='__main__':
    persist()
    print extract_and_execute(40)</pre>
</div>
One additional thing that you might notice in the above code is that we are encoding the result of marshal.dumps using base64 encoding. This is required so that we can write the data to the file and retrieve it back safely without running into encoding issues. We will use the same encoding when sending on the XML RPC request so that we can safely send the code over XML.
<h3><span class="Apple-style-span" style="font-size:15px;font-weight:bold;">Building the server</span></h3>
Lets build the server now. Like I said, we will build this using the XML RPC module in Python. I am listing the code below and will then explain the key components in the code.
<pre style="padding-left:30px;">import sys,xmlrpclib,base64,types,marshal
from SimpleXMLRPCServer import SimpleXMLRPCServer
from fibonacci import *

def execute(code_string,args):
    code=marshal.loads(base64.b64decode(code_string))
    func=types.FunctionType(code,globals(),'fibonacci')
    return func(args)

server = SimpleXMLRPCServer(("localhost", int(sys.argv[1])))
print "Started server"
server.register_function(execute, "execute")
server.serve_forever()</pre>
What I have done here is put the code for the Fibonacci calculation in a separate module so that it can be imported into the server and the client. The server has one function which takes a string and a parameter. The function unmarshals the string into a function and calls the function using the parameter. The result is returned back.

How we will use this is by sending a serialized version of the fibonaaci method and the value of n as parameters. Because the code is available in the module which is imported on the server, we can make recursive calls without failing.

In order to expose the method, we will register it in the XML RPC server and start the server on a user specified port.

If you start this code in a terminal, you will see something like this


<h3>Building the client</h3>
The client will be a simple XML RPC client. A very simple client can be built as follows - the code serializes the fibonacci method, and invokes the method over the XML RPC proxy.
<pre style="padding-left:30px;">import xmlrpclib,base64,types,marshal
from fibonaaci import *

proxy = xmlrpclib.ServerProxy("http://localhost:8000/")

code_string=base64.b64encode(marshal.dumps(fibonaaci.func_code))
print proxy.execute(code_string,20)</pre>
The code doesn't do much - if you start the server in one terminal and run this in another terminal, you will see a log in the server window when the request is received and processed, and you will see the result printed in the client window.





Now, this was no grid - one server received task from one client. To make this more interesting,lets start another server and modify the client to send multiple requests to the server in threads. This is how the client code looks.
<pre style="padding-left:30px;">import xmlrpclib,base64,types,marshal
from fibonaaci import *
from threading import Thread

class Runner(Thread):
    def __init__(self,code,proxy,param):
        Thread.__init__(self)
        self.code=code
        self.proxy=proxy
        self.param=param

    def run(self):
        print self.proxy.execute(self.code,self.param)

proxy = xmlrpclib.ServerProxy("http://localhost:8000/")
proxy1 = xmlrpclib.ServerProxy("http://localhost:8001/")

code_string=base64.b64encode(marshal.dumps(fibonaaci.func_code))

r=Runner(code_string,proxy,30)
r1=Runner(code_string,proxy1,40)

r.start()
r1.start()</pre>
<pre style="padding-left:30px;">r.join()</pre>
<pre style="padding-left:30px;">r1.join()</pre>
If you start two servers and run this client code then you will see a log in one of the servers as soon as the calculation for 30 is completed. The code for 40 will take a little longer and you will see the logs.

You can try starting many servers and modifying the client code to send the request to all of them one by one to see how long it takes to execute.
<h3>Making the client code more effective</h3>
The usual case for a grid computing scenario is that you have a set of grid servers and you submit the tasks to them. Each server is busy for the duration that it is executing the code and then becomes available for more execution. The following code snippet shows how we can modify the client to loop over a set of proxies and continuously submit jobs.
<pre style="padding-left:30px;">import xmlrpclib,base64,types,marshal
from fibonaaci import *
from threading import Thread
from collections import deque

proxy = xmlrpclib.ServerProxy("http://localhost:8000/")
proxy1 = xmlrpclib.ServerProxy("http://localhost:8001/")

code_string=base64.b64encode(marshal.dumps(fibonaaci.func_code))

servers=deque([proxy,proxy1])

class Runner(Thread):
    def __init__(self,code,param):
        Thread.__init__(self)
        self.code=code
        self.param=param

    def run(self):
        while True:
            try:
                proxy=servers.popleft()
            except:
                proxy=None
            while proxy is None:
                try:
                    proxy=servers.popleft()
                except:
                    proxy=None
            print proxy.execute(self.code,self.param)
            servers.append(proxy)

r=Runner(code_string,35)
r1=Runner(code_string,35)

r.start()
r1.start()

r.join()
r1.join()</pre>
<h3> Conclusion</h3>
What I have shown here is a very simple example. In a larger use case you will have to ensure that all the libraries are deployed and available on all the grid servers. You will want to have an optimum number of servers, and some way to monitor the status of each job. Also, the client code will have to deal with taking a large input set and splitting it across all the tasks.

All that can be built on top of this simple framework and you can have a working grid ready in a matter of days!
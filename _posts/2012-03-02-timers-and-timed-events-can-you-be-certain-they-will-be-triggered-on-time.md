---
layout: post
title: "Timers and Timed events - can you be certain they will be triggered on time?"
date: 2012-03-02 00:15
comments: false
categories:
---

We all need to measure time, whether we are late for an appointment or if we want to decide how much more we can laze before we need to get going. In our daily life, thankfully, we only deal with minutes and hours, rarely with seconds. This makes it easy. But it is a different story when we deal with computers and software - we need to measure in terms of milliseconds and microseconds. Some applications like low latency market data for financial institutions demand nano second precision in this fast paced world of electronic trading.

<a href="http://commons.wikipedia.org/wiki/File:CpuTimeonSingleCpuMultiTaskingSystem.png" target="_blank"><img  title="English: CPU Time on Single CPU Multi Tasking ..." src="http://upload.wikimedia.org/wikipedia/commons/thumb/3/33/CpuTimeonSingleCpuMultiTaskingSystem.png/300px-CpuTimeonSingleCpuMultiTaskingSystem.png" alt="English: CPU Time on Single CPU Multi Tasking ..." width="300" height="202" /></a>

The servers and computers that we use and the operating systems that we use in today's world provide us the means to measure time accurately. And they work in 99.99% of all the cases - but there are cases where they don't work with the precision that we want or would like to demand. Out of these .01% cases, 99% cases are never noticed - only a small 1% of these ever gets noticed - that was when you had that serious production issue with something that happened too late, to early or never happened at all.

Makes you wonder - do these things really work? This post aims to clarify some of the aspects around the various <a  title="Time" href="http://en.wikipedia.org/wiki/Time" rel="wikipedia" target="_blank">time measurement</a> requirements for timers and timed events and why they work or fail.


<h2>Some reading material</h2>
If you work with Java, then this article from Oracle is a very good explanation of how the clocks in the VM work, the different types of time measurement - the free running nano second counter that counts the cycles between operations and the interrupt based clock that calculates milliseconds - and the conversions between the two etc.

<a href="https://blogs.oracle.com/dholmes/entry/inside_the_hotspot_vm_clocks">https://blogs.oracle.com/dholmes/entry/inside_the_hotspot_vm_clocks</a>

This article from Citihub explains the importance of precision <a  title="Synchronization" href="http://en.wikipedia.org/wiki/Synchronization" rel="wikipedia" target="_blank">time synchronization</a> - if we need two events to occur one after the other, then we need to know when the first one is completed without ambiguity, even when they are running on separate platforms and far apart.

<a href="http://www.citihub.com/precision-time-synchronisation/">http://www.citihub.com/precision-time-synchronisation/</a>
<h2>Beating the speed of light in time measurements</h2>
This is one of the most interesting comments that I heard in a presentation when a vendor was claiming the low latency of  their product and claimed to have measured the time taken between two points in the data flow with single digit nano second precision. Light can travel 0.3 meters in one nano second. So if you are measuring the time between two points more than 0.3 meters apart with the precision of one nano second then your data must be travelling faster than the speed of light :).

I do feel that this does not apply if you are measuring the time taken within the processor or in memory. Modern <a  title="Central processing unit" href="http://en.wikipedia.org/wiki/Central_processing_unit" rel="wikipedia" target="_blank">CPU</a>'s with &gt; 1GHz frequency have <a  title="Clock signal" href="http://en.wikipedia.org/wiki/Clock_signal" rel="wikipedia" target="_blank">clock cycles</a> slightly under 1 nano second, so you could copy a value in that time.

But for anything between two computers or anything that is separated by a foot or more of wiring or cable you cannot measure with such a precision. This is fundamental to why we have errors in measurement in some very high speed cases of data processing.
<h2>So how does a timer trigger and why does it get delayed?</h2>
A timer is nothing more than a piece of code that runs on the CPU to determine if it is time to do something and do the task. In that sense it is not much different from any other piece of code that it has to wait for the <a  title="Operating system" href="http://en.wikipedia.org/wiki/Operating_system" rel="wikipedia" target="_blank">OS</a> to schedule it on the CPU to run, get all the resources that are needed for it to determine that the time has indeed come to do the task and then execute the task. The task execution itself is a different story because the task has to get CPU, resources etc on its own and get executed.

Since a timer is given <a  title="CPU time" href="http://en.wikipedia.org/wiki/CPU_time" rel="wikipedia" target="_blank">CPU time</a> once in few cycles, and then it has has to wait for its next chance, there is an amount of time spent waiting. Assuming a timer has to fire after an hour, this one hour includes the time waiting for CPU and the time spent each time it gets the CPU to determine if one hour has actually passed.

Applying common sense,we know that if we divide a certain X into chunks of Y, then unless X is a multiple of Y, the chunks will not be equal. Also applying common sense from daily life, we know that when we are queued up for something, how fast we get to it depends not on how long we decided to wait but on how many people are ahead of us on the queue and how much time each one of them takes.

Applying these analogies, the timer can get the CPU either exactly when the hour has passed and determine that the time has come, or it can fall ahead or behind the schedule. This largely depends on the CPU load and number of competing processes.

Once the timer gets the CPU and determines that it has to do a task, it will instantiate the task and invoke it - this takes some time of its own. So, if the timer got the CPU c1 cycles late, and took c2 cycles to instantiate and invoke the task, then the task actually occurred c1+c2 cycles later than expected. As the number of cycles goes into the higher millions and billions, the delay becomes significant enough to get noticed. The larger the number, the more easily it is noticed.
<h2>Does this delay really matter?</h2>
It probably does not matter for most cases, but use cases like financial markets, precision manufacturing, airlines etc, this matters. Take for example the financial industry. Almost every major player has electronic trading systems that go out to the market and buy or sell securities without human intervention and at blazing high speeds. These systems are further optimized by hosting them closer to the exchanges and markets so that orders can be placed and filled in nano seconds.

You need to understand how this works - an event comes in, a news or a price update, which the application processes and determines that it is of interest. The computer then has to a)identify the security, b)pick a price and size, c)place an order, d)process the acknowledgement, e)get the execution and process it or f)cancel the order if it cannot be filled or g)take another action to counter this one and flatten risk.

That is 6 steps, at least, taken very quickly at high speed. If any one of them falls over and fails, there is an open position. If any one of them takes too long to happen, then it is an opportunity lost. Worst case, it a case where someone takes advantage of you. High speed trading was introduced because we as humans take too long to react to fast paced events and computers do better. If computers take too long to react then we are not making much progress.

Not just finance,lets say <a  title="NASA" href="http://maps.google.com/maps?ll=38.8830555556,-77.0163888889&amp;spn=0.01,0.01&amp;q=38.8830555556,-77.0163888889 (NASA)&amp;t=h" rel="geolocation" target="_blank">NASA</a> or <a  title="European Space Agency" href="http://maps.google.com/maps?ll=48.8482,2.3042&amp;spn=1.0,1.0&amp;q=48.8482,2.3042 (European%20Space%20Agency)&amp;t=h" rel="geolocation" target="_blank">ESA</a> or <a  title="Indian Space Research Organisation" href="http://maps.google.com/maps?ll=12.9666666667,77.5666666667&amp;spn=0.01,0.01&amp;q=12.9666666667,77.5666666667 (Indian%20Space%20Research%20Organisation)&amp;t=h" rel="geolocation" target="_blank">ISRO</a> launch a rocket to the moon and that rocket has to detach a stage at a certain altitude. The rocket is moving bloody fast and gets past that altitude in fraction of a second.  The computers that determine that the stage needs to be detached need to be able to react fast.

This lack of tolerance of delay is what is demanded in <a  title="Real-time computing" href="http://en.wikipedia.org/wiki/Real-time_computing" rel="wikipedia" target="_blank">real time computing</a>.
<h2>Its not just about the delay, its about knowing the right time</h2>
If our clocks are running fast or slow, we can can check them against a reference and correct them. Without that we have no way of knowing whether the time we measured is correct. The same is required for computers as well. They need to know if the time they measure is correct or not. At any instant, time is unique across all systems measuring it. If a server measures the time as 3 PM and another measures it as 1 second past 3 PM, then it is not acceptable.

This is further complicated by the fact that two servers might be synchronizing against different reference clocks. And also the delays introduced in the transmission of data between two points in the network. If the network is heavily loaded then it adds non-deterministic delays to each packet of data transmitted.

There are ways to get around all these issues using precision time servers, GPS signals etc as described in the posts that I mentioned.
<h2>All this adds up to having timers and timed events not occur on time</h2>
When we use timers to schedule or trigger a task, all these synchronizations, calculations and scheduling is happening in the background. On a system that does not operate under heavy loads, there is less contention for resources and things go fast. So events always trigger on time.

As the load increases, delays increase and at some point they become noticeable enough to be classified as issues. In some cases, they can be remedied by adding more memory or reducing the load so that each process gets more CPU time and waits less.

Also, from the time an event is triggered to the time when it is ready with all resources and can execute, the CPU can get loaded. If the execution depends on components on different servers then it will also include network delays. For example, an event to send a alert message to your server might have triggered on time, but because of network latency, it might come to you a bit late.
<h2>OS and system level tasks interfering with execution</h2>
In Java, there are cases when the memory allocated to the JVM is not enough and objects fill up the space. This generally happens when garbage collection is not able to free enough space. When this happens, the garbage collector gets into a thrashing mode and continuously tries to free space instead of getting any work done. In fact it is impossible for any work to get done because all resources are used up and that is why the garbage collector is thrashing.

This can happen with non Java applications, or even with the OS when then system runs out of resources and cannot proceed further. It gets into a desperate rush to free resources and be able to breathe and avoid a crash.

These kind of interference also adds a delay to timed events happening correctly.
<h2>Using hardware to remedy the issues</h2>
We can use hardware to remedy all these delays and issues. High speed optical cabling, more memory, high speed memory, solid state disk drives etc can be used to speed up IO and network operations. Also CPU's can be run at optimal cooling and clock cycles to make them perform faster and better.

We can add more cores to the servers or distribute the load across a large number of servers to reduce individual loads. This helps a lot.
<h2>Optimizing software</h2>
Since at the heart of it all it is software that runs on the CPU, optimizing your code to run faster and not lock resources from other threads and processes and contribute to overall delay is very important. If you hear anyone say that issues not found at a certain load happen at twice that load or five times that load, then it is because these small delays add up at such large scales.

Writing optimized code is not a trivial task, but can be achieved with a clean design and with the correct approach from the the start.
<h2>Conclusion</h2>
Timers and timed events do occur on time as expected in most cases. In the cases when they do fail it is not because they were lazy or were not coded correctly but because of the loads on the system and the contention for resources. These issues are common, but not too common that they happen everyday to you. With careful planning they can be avoided.

&nbsp;
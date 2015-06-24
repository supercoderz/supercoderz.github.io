---
layout: post
title: "IPython kernel for Redis"
date: 2014-11-02 00:39
comments: false
categories:
---

I have been working on understanding how IPython works, the kernels, the client etc. I have managed to figure out how the zmq client and server mechanism works and makes it so simple to add so many types of clients. Its really awesome.

Not content with that I wanted to see if I would write a kernel for IPython. I had 2 plans - one was to write a simple kernel and one was to write a Groovy kernel for IPython, mixing a JVM language into the notebook.

The Groovy kernel isn't making much progress, but I found that I could use the IPython dev version from Github and the kernel machinery exposed in that to write wrapper kernels. So I figured why not try that for starters. I picked up Redis which allows us to send commands over simple sockets, and integrated it into the IPython kernel machinery to build a simple kernel which can be used a a Redis CLI.

Take a look at it and let me know what you think - <a href="https://github.com/supercoderz/redis_kernel">https://github.com/supercoderz/redis_kernel</a>
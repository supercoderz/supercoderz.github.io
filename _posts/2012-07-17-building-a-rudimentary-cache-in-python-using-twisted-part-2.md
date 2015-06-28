---
layout: post
title: "Building a rudimentary cache in Python using Twisted - part 2"
date: 2012-07-17 23:37
comments: false
categories:
---

I decided to go ahead and try using the cache that I built for some scenario that I might expect to see in real life. Part of the work I do daily demands that I start one thread to create some data that goes into a cache or a messaging layer and then another thread in the same application consumes it - pretty basic multithreading in Java  and nothing really special.

What I wanted to see was if it is possible to use my cache in a similar way in Python , obviously with fewer lines of code. See the below screen shots for the updated code and the execution results.



<a href="http://supercoderz.files.wordpress.com/2012/07/enhanced_cache1.png"><img class="aligncenter size-large wp-image-379" title="enhanced_cache" src="http://supercoderz.files.wordpress.com/2012/07/enhanced_cache1.png?w=1024" alt="" width="1024" height="577" /></a>

<a href="http://supercoderz.files.wordpress.com/2012/07/fib-ticker.png"><img class="aligncenter size-large wp-image-380" title="fib ticker" src="http://supercoderz.files.wordpress.com/2012/07/fib-ticker.png?w=1024" alt="" width="1024" height="577" /></a>

<a href="http://supercoderz.files.wordpress.com/2012/07/running-ticker.png"><img class="aligncenter size-large wp-image-381" title="running ticker" src="http://supercoderz.files.wordpress.com/2012/07/running-ticker.png?w=1024" alt="" width="1024" height="545" /></a>
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://supercoderz.in/2012/07/16/building-a-rudimentary-cache-in-python-using-twisted/" target="_blank">Building a rudimentary cache in Python using Twisted</a> (supercoderz.in)</li>
</ul>
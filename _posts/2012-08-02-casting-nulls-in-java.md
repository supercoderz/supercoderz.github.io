---
layout: post
title: "Casting nulls in Java"
date: 2012-08-02 20:25
comments: false
categories:
---

I admit this was something that I never noticed, even though I have used it day in and out. And it was a surprise when I realized it.

I have been working extensively on messaging systems and applications that interface to the messaging layer in banks and other large enterprises. In most cases data is exchanged as maps, and extracting any part of the data is just a map look up that looks like this -

Object value = map.get(key)

You could check if the key is present before you try to extract its value, but in most cases I find that my code does not have to deal with the value itself, but has to pass it on - and the downstream can handle if it is null. So I just map fields. The cases where I do deal with the value, I do something this this -

String value = String.valueOf(map.get(key))

if(value.equals("null"==false){

...code...

}

This works and I get my results fine. Now, I got so used to this that I did not realize that when I do a map.get(key) on a non existent key, the null value is cast as a reference of the type of the map - in my case Object in 99% cases. And String.valueOf, which is overloaded always resolves to the Object variant and there is no null pointer exception.

If you actually did this, it will fail

String value = String.valueOf(null)

This is because there are many overloaded implementations and this resolves to String.valueOf(char[]) - that method internally tries to get the length of the array, and that gives a null pointer.

Instead if I did something like this, it will work. That is because the null is now cast as a String reference and will resolve to the Object variant of the valueOf method.

String value = String.valueOf((Object)null)

It is of course possible to not convert to String at all and check for null - that works for almost any case you might have. In my day to day work since I am used to expecting fields as strings, I use the approach of converting to String.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://www.javacodegeeks.com/2012/06/avoid-null-pointer-exception-in-java.html" target="_blank">Avoid Null Pointer Exception in Java</a> (javacodegeeks.com)</li>
</ul>
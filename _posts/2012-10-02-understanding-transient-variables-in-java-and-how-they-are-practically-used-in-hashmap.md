---
layout: post
title: "Understanding transient variables in Java and how they are practically used in HashMap"
date: 2012-10-02 23:48
comments: false
categories:
---

What is the significance of the transient keyword in <a  title="Java (programming language)" href="http://www.oracle.com/technetwork/java/" rel="homepage" target="_blank">Java</a>? If you know the answer, good! you are a person who uses this a lot or a person who has read this very recently. If this seems like a word from a half remembered dream, well don't worry you have company. I was and am will be confused if you asked me about this in an hour. It is one of those things that I learnt but never had to use it - mainly because I never worked on code that required me to worry about how my objects were serialized. I could delegate that to the libraries.

This post is an attempt to explain what transient is (which most people should know) and an example of how it is used, using the most abused class of my daily work - the java.util.<a  title="Hash table" href="http://en.wikipedia.org/wiki/Hash_table" rel="wikipedia" target="_blank">HashMap</a> class.

<!--more-->
<h2>What is transient?</h2>
The transient keyword in Java is used on <a  title="Class variable" href="http://en.wikipedia.org/wiki/Class_variable" rel="wikipedia" target="_blank">class variables</a> and is used to indicate to the runtime that this variable should not be considered while serializing the object is being written to a <a  title="Byte stream" href="http://en.wikipedia.org/wiki/Byte_stream" rel="wikipedia" target="_blank">byte stream</a>. To put it in simpler words, if an object is being written to a byte stream then all transient variables will be ignored.

Lets think about this for a moment. Lets say you have a class that looked like this

public class Bean{

private int a=5;

private transient int b=6;

}

If you were creating an instance of this class and then writing it to a byte stream - a file or a socket, then when serializing this class, the variable 'a' will be sent but 'b' will be ignored. When you reconstruct this class on the other end, then 'b' will be reverted back to the initial default value. So lets say you had some code that updated the value of b to 10 before serializing the object, then when you reconstruct it, the value of b will be 6.
<h2>Why would you not want fields to be preserved?</h2>
Why would you not want some of the fields in an object to be preserved between storing and retrieving the object? If a field represented a value like a persons name or a amount or some such parameter which has some significance and changing it will impact the application state, then you would definitely want to preserve it. But do you have parameters that you can afford to lose?

Lets say you have a flag that indicates whether the object was modified or not - this is typical in <a  title="Object-relational mapping" href="http://en.wikipedia.org/wiki/Object-relational_mapping" rel="wikipedia" target="_blank">ORM</a> libraries that want to track whether a bean was modified after you retrieved it from the database. The value of the flag that indicates whether the object was modified or not is significant only until the change to the object is saved somewhere and the object is now again clean. So such a flag, lets call it the dirty indicator, is a field that gets reset once it is saved.

If this object was being written to a database to save it, like in an ORM, or written to a file or sent over a socket, then once that is done, then the flag is not needed. Also any code that retrieves the object next time, will expect the flag to be set to the default value which is false for a new instance. So this is a good case to use  transient variable. Set the flag to true while the object is in memory and while writing it to a file, ignore the field. When you read the file again, the field is read a false, which is true because you haven't yet changed the field.
<h2>Do we ever want to use transient for fields that are not reset and need to be preserved?</h2>
The answer is yes. And now we will proceed to understand why.

Lets say you have a bean class, like the one I showed above. When this is sent over a socket for example, Java serializes this to a stream of bytes in such a way that it can reconstruct the object on the other end. In order to do this it sends information like -
<ul>
	<li>The class name</li>
	<li>The field names</li>
	<li>The field values and the lengths</li>
</ul>
Based on the class name, the <a  title="Java Virtual Machine" href="http://en.wikipedia.org/wiki/Java_Virtual_Machine" rel="wikipedia" target="_blank">JVM</a> on the other end instantiates an object and sets all the values on the respective fields. Since you are sending all the field names and values, it will take a good amount of memory to hold this. If the size of the serialized byte array is larger then it will take more time to send this over the socket and it will take more time to reconstruct it. It will also consume more bandwidth.

There are applications where spending time sending large blocks of data is expensive - so you want to minimize the amount of space occupied by serialized objects and also the time taken to <a  title="Serialization" href="http://en.wikipedia.org/wiki/Serialization" rel="wikipedia" target="_blank">serialize</a> and de-serialize objects. You will write custom code to serialize your objects by writing writeObject(ObjectOutputStream) and readObject(ObjectInputStream) methods.

When you do this, you should mark all your fields as transient and then write them on your own to the object output stream. You can read them back in the same order and reconstruct the object.

Another case is for collections where you store data in an indexed fashion and the index will change when you de-serialize the object - in this case too you will want to mark things as transient and reconstruct them on your own.
<h2>So how does a hash map use transients?</h2>
There is no good reason why I chose hash map as an example other than the fact that I am most familiar with this and have flogged this to death by using it a lot.

If you look at the source code of java.util.HashMap (<a href="http://www.docjar.com/html/api/java/util/HashMap.java.html">http://www.docjar.com/html/api/java/util/HashMap.java.html</a>), you will see that the data that you put in is stored in a array of Entry&lt;K,V&gt; objects. This array is a transient variable called table. There is a table size which is transient too, and there are a bunch of other non-transient and transient variables.

Why is the table a transient? If you look at the code, you will see that for every key that you put in , the class creates a hash code, and using the length of the array (default 16 at start up) it determines the index of the key in the array as

index = hash &amp; (length -1)

Where hash is the <a  title="Return statement" href="http://en.wikipedia.org/wiki/Return_statement" rel="wikipedia" target="_blank">return value</a> of Object.<a  title="Hash function" href="http://en.wikipedia.org/wiki/Hash_function" rel="wikipedia" target="_blank">hashCode</a>().

There are two important things here - hashCode() does not return the same value for two instances of the same class. And, if a key is not stored in the correct index as per the hash, then it is more expensive to retrieve it. In fact, if you thin about it, if you created hash code on one computer and put the key at a particular index, and then on a different PC calculated the hash code and index, it will return a different value. If you serialized the table as is and reconstructed it, then you will be referring to the wrong index!

So, when a hash map is serialized, it means that the hash index, and hence the ordering of the table is no longer valid and should not be preserved.

For this reason, HashMap implements the writeObject and readObject methods. These still let the non transient fields to get serialized as they would normally do, but they wrote the <a  title="Associative array" href="http://en.wikipedia.org/wiki/Associative_array" rel="wikipedia" target="_blank">key value pairs</a> at the end of the byte array one after the other. When reading back, they let the non transient variables to be handled by Java default de-serialization logic and then read the key value pairs one by one.

For each key the hash and the index is calculated again and is inserted to the correct position in the table so that it can be retrieved again without any error.
<h2>Conclusion</h2>
Although we have not touched upon all uses of transient, we have seen a very good example usage of this in a core Java class.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://stackoverflow.com/questions/12556539/hashmap-internal-implementation" target="_blank">HashMap internal implementation</a> (stackoverflow.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://java.dzone.com/articles/java-7-hashmap-vs" target="_blank">Java 7: HashMap vs ConcurrentHashMap</a> (java.dzone.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://pinku12.wordpress.com/2012/09/14/java-language-keywords/" target="_blank">Java Language Keywords</a> (pinku12.wordpress.com)</li>
</ul>
---
layout: post
title: "Building your own Mock objects with Proxy pattern in Java"
date: 2012-06-18 22:22
comments: false
categories:
---



Most of us, if not all, have written unit tests for our code using <a  title="JUnit" href="http://junit.sourceforge.net" rel="homepage" target="_blank">JUnit</a> or comparable tools. In most cases we are in control of what is being tested and we can provide all the inputs that are needed to test the scenario. But then there are cases where there are external factors or classes that cannot be instantiated for tests and we need to find ways to simulate them - the suggested approach is to use <a  title="Mock object" href="http://en.wikipedia.org/wiki/Mock_object" rel="wikipedia" target="_blank">mock objects</a>.

There are many frameworks like <a  title="EasyMock" href="http://www.easymock.org/" rel="homepage" target="_blank">EasyMock</a> which help create mock objects for classes and interfaces.

How do they mock objects? Turns out it is not magic.


<h2>Proxy Pattern</h2>
The Proxy pattern is the core of any mock framework. As per Wikipedia, Proxy Pattern is defined as
<blockquote>A proxy, in its most general form, is a class functioning as an interface to something else. The proxy could interface to anything: a network connection, a large object in memory, a file, or some other resource that is expensive or impossible to duplicate.</blockquote>
So when we say that we are interacting with a proxy object, it means that we are working with something that behaves exactly the same way as the object would behave if it was instantiated using the class.

Proxies can be created using many libraries, but we will not get into that - instead we will see how easy it is to create a proxy of any class in simple Java.
<h2>Proxy and InvocationHandler</h2>
The <a  title="Proxy pattern" href="http://en.wikipedia.org/wiki/Proxy_pattern" rel="wikipedia" target="_blank">Proxy class</a> in <a  title="Java Development Kit" href="https://jdk6.dev.java.net/" rel="homepage" target="_blank">Java SDK</a> provides the mechanism to create a proxy instance of any interface - it has a newProxyInstance method which takes a <a  title="Java Classloader" href="http://en.wikipedia.org/wiki/Java_Classloader" rel="wikipedia" target="_blank">class loader</a>, a set of interfaces and a invocation handler to create a proxy. The invocation handler is an object to which all the method calls on the proxy are delegated to.

The InvocationHandler interface has a single method called invoke which gets called with the proxy object, the method to be called and the arguments. This method is supposed to return a value compatible with the method being invoked - if it returns anything else then an exception will be thrown.

The following code examples show how you can create an invocation handler for an interface.

package com.supercoderz.proxy;
public interface Test {
    public int getInt();
    public int getIntXXX();
}

An implementation of this interface

package com.supercoderz.proxy;
public class TestImpl implements Test {
    <a  title="Annotation" href="http://en.wikipedia.org/wiki/Annotation" rel="wikipedia" target="_blank">@Override</a>
    public int getInt() {
        // TODO Auto-generated <a  title="Method stub" href="http://en.wikipedia.org/wiki/Method_stub" rel="wikipedia" target="_blank">method stub</a>
         return 0;
    }
    @Override
    public int getIntXXX() {
        // TODO Auto-generated method stub
        return 0;
    }
}

Now if we want to write a simple invocation handler that delegates the calls to an instance of the TestImpl class then this is what we can do

package com.supercoderz.proxy;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
public class ProxyHandler implements InvocationHandler {
    Test test;
    public ProxyHandler(Test test) {
        this.test = test;
    }
    @Override
    public Object invoke(Object object, Method method, Object[] args)
    throws Throwable {
        System.out.println("Test");
        System.out.println(method.getReturnType());
        return 1;
    }
}

So how do we use this? Below example show a simple factory method to create this and a sample code on how you can use this transparently in your code. (The <a  title="Factory method pattern" href="http://en.wikipedia.org/wiki/Factory_method_pattern" rel="wikipedia" target="_blank">factory class</a> code is not shown in full here).
public static Test getInstance() {
    TestImpl test = new TestImpl();
    Test proxy = (Test) Proxy.newProxyInstance(Test.class.getClassLoader(),
    new Class&lt;?&gt;[] { Test.class }, new ProxyHandler(test));
    return proxy;
}
Main code to use this
    Test proxy = getInstance();
    System.out.println("" + proxy.getInt());
Simple!
<h2>From proxies to mock</h2>
The transition from a proxy to a mock is pretty simple - in one approach you could create a invocation handler that knows that it is in test mode and returns test values for each method call. This will mean that you return values for all methods, or you throw exceptions for what you do not want to support in mocking. This is a static approach and you cannot add more methods or change results easily.

The other approach which is provided by EasyMock for example, where you specify methods that you expect to call as expect(some method).andReturn(some value) can be easily built using an invocation handler.

What we will do is create an invocation handler that understands that we are creating a mock and can return results that we expect instead of delegating to an instance of the class. As you can see below the code is very generic, not specific to the Test interface and al it does is track methods that we set on it as expectations and the values to return. If a method is not set as expectation then it throws an exception.

package com.supercoderz.proxy;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.util.<a  title="Hash table" href="http://en.wikipedia.org/wiki/Hash_table" rel="wikipedia" target="_blank">HashMap</a>;
import java.util.Map;
public class MockProxyHandler implements InvocationHandler {
    Map&lt;String, Object&gt; expectations = new HashMap&lt;String, Object&gt;();
    @Override
    public Object invoke(Object object, Method method, Object[] args)
    throws Throwable {
        if (expectations.containsKey(method.getName())) {
            return expectations.get(method.getName());
        } else {
            throw new Exception("No mock assigned to method");
        }
    }
    public void addExpectation(String method, Object result) {
        expectations.put(method, result);
    }
}

This invocation handler can be used to create a factory method just like before - in fact you can pass in any class you want and this handler can be used with that. You can also add a helper method to set expectations.

public static Test createMock() {
    TestImpl test = new TestImpl();
    Test proxy = (Test) Proxy.newProxyInstance(Test.class.getClassLoader(),
        new Class&lt;?&gt;[] { Test.class }, mockProxyHandler);
        return proxy;
}
public static void mockResult(String method, Object result) {
    mockProxyHandler.addExpectation(method, result);
 }

And then you can use this in your tests without much hassle and check for yourself that the method returns the values that you set when setting the expectation.

    Test mock = createMock();
    mockResult("getInt", new Integer(45));
    System.out.println(mock.getInt());
    System.out.println(mock.getIntXXX());

<h2>Conclusion</h2>
Mocking frameworks abstract a lot from us, but the underlying logic is very simple.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://www.nofluffjuststuff.com/blog/howard_lewis_ship/2012/04/yet_more_spock_magic_mocks" target="_blank">Yet More Spock Magic: Mocks</a> (nofluffjuststuff.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://nedbatchelder.com/blog/201206/tldw_stop_mocking_start_testing.html" target="_blank">tl;dw: Stop mocking, start testing</a> (nedbatchelder.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.javacodegeeks.com/2012/05/mocks-and-stubs-understanding-test.html" target="_blank">Mocks And Stubs - Understanding Test Doubles With Mockito</a> (javacodegeeks.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://premaseem.wordpress.com/2012/05/28/mockito-examples/" target="_blank">Mockito examples</a> (premaseem.wordpress.com)</li>
</ul>
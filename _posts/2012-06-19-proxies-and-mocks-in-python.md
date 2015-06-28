---
layout: post
title: "Proxies and Mocks in Python"
date: 2012-06-19 23:45
comments: false
categories:
---

Yesterday I wrote a post about using Proxy class in <a  title="java development tools" href="http://download.cnet.com/windows/java-software/" rel="downloadcom" target="_blank">Java</a> to create a trivial mock framework - creating the same thing in <a  title="Python (programming language)" href="http://www.python.org/" rel="homepage" target="_blank">Python</a> takes even fewer <a  title="Source lines of code" href="http://en.wikipedia.org/wiki/Source_lines_of_code" rel="wikipedia" target="_blank">lines of code</a>. The generic proxy can be implemented very easily as shown below.
<pre>class Proxy(object):
    def __init__(self,subject):
        self.subject=subject
        self.expectations={}
    def __getattr__(self,name):
        if self.expectations.__contains__(name):
            def wrap():
                return self.expectations[name]
            return wrap
        else:
            raise <a  title="Exception handling" href="http://en.wikipedia.org/wiki/Exception_handling" rel="wikipedia" target="_blank">Exception</a>("No expectations set on mock for this attribute")

    def expect(self,name,value): 
        if hasattr(self.subject,name): 
            self.expectations[name]=value 
        else: 
            raise Exception("No attribute with given name")

class <a  title="Test cricket" href="http://en.wikipedia.org/wiki/Test_cricket" rel="wikipedia" target="_blank">Test</a>(object): 
    def get_int(self): 
        return 12 
t=Test() 
print t.get_int() 
p=Proxy(t) 
print p 
try: 
    p.expect("unknown","value") 
except: 
    print "caught exception adding <a  title="Expected value" href="http://en.wikipedia.org/wiki/Expected_value" rel="wikipedia" target="_blank">expectation</a> on unknown method" 

p.expect("get_int",45) 
print p.get_int() 
try: 
    p.get_intxxx() 
except: 
    print "caught exception trying to call unknown attribute with no expectation"</pre>
And this produces the following output
<pre>Python 2.7.2 (default, Jun 12 2011, 15:08:59) [MSC v.1500 32 bit (<a  title="Intel" href="http://maps.google.com/maps?ll=37.3879277778,-121.963538889&amp;spn=0.01,0.01&amp;q=37.3879277778,-121.963538889 (Intel)&amp;t=h" rel="geolocation" target="_blank">Intel</a>)] on win32
Type "copyright", "credits" or "license()" for more information.
&gt;&gt;&gt; =============================<a  title="Relational operator" href="http://en.wikipedia.org/wiki/Relational_operator" rel="wikipedia" target="_blank">===</a> RESTART ================================
&gt;&gt;&gt; 
12caugt exception adding expectation on unknown method
45
caught exception trying to call unknown attribute with no expectation
&gt;&gt;&gt;</pre>
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://supercoderz.in/2012/06/18/building-your-own-mock-objects-with-proxy-pattern-in-java/" target="_blank">Building your own Mock objects with Proxy pattern in Java</a> (supercoderz.in)</li>
</ul>
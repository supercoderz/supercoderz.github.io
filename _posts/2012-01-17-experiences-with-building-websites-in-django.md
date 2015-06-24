---
layout: post
title: "Experiences with building websites in Django"
date: 2012-01-17 00:21
comments: false
categories:
---

The first time that I heard about <a class="zem_slink" title="Django (web framework)" href="http://www.djangoproject.com" rel="homepage">Django</a> was actually when I heard about another equally good and well known web development framework - <a class="zem_slink" title="Google App Engine" href="http://code.google.com/appengine/" rel="homepage">Google App Engine</a>. I had read about App Engine somewhere and was itching to try out a test site over the weekend. I am basically a middleware and messaging guy and feel most comfortable with command line - so I was not fully sure I would be able to build the web pages for my experiment with App Engine - but somewhere in the tutorial while showing the easy to use templates, the guy mentioned that App Engine supports Django templates - that's how I got to know Django and fell in love with it mostly for the ease with which you can build things and even more for the Admin website.

I have been envangelising Django for a while to my friends and have recently started using it for a serious side project and I must say I am amazed by my own velocity of things.

This post is a collection of few things that I like - its not an exhaustive list, and you can find out more by visiting <a href="https://www.djangoproject.com/">https://www.djangoproject.com/</a> and trying it for yourself.

<!--more-->
<h2><img title="More..." src="https://supercoderz.wordpress.com/wp-includes/js/tinymce/plugins/wordpress/img/trans.gif" alt="" />What is Django?</h2>
Taken from their website, this is what is Django
<blockquote>Django is a high-level Python Web framework that encourages rapid development and clean, pragmatic design.</blockquote>
It is true to every word of it. Django makes it very easy to build websites by providing all the necessary functionality that you would expect in a website as easy to use components. All you need to know is what you want and what components in Django provide that functionality - after that its always just a few lines of code.

Lets go through the things that I love the most.
<h2>Defining Models</h2>
Every application starts with a basic model - what are the entities that are applicable to the problem being solved and how they should be represented. For web applications, model is even more important because models drive the views or pages that need to be created and also control how data is represented. A well designed model can make your application easy to enhance and maintain in the long run and with enough care shown upfront, it can accommodate all the changes that might happen to the business domain. We cannot factor in earth shattering changes - but that is a different story.

The problem I had with models and the one that gets easily solved in Django is how you define them. I hate <a class="zem_slink" title="ER" href="http://www.nbc.com/ER/" rel="hulu">ER</a> diagrams - although that is how I learnt database. I hate all the things about ER diagrams and normalization etc because they do not easily translate into code in my head - its a personal thing. This was alleviated to an extent in Java world by Hibernate which would let me create classes and then turn them into tables. So instead of worrying about tables and then coding for them, I could worry about how best to represent in code and then let the <a class="zem_slink" title="Object-relational mapping" href="http://en.wikipedia.org/wiki/Object-relational_mapping" rel="wikipedia">ORM</a> decide how to store it. But, Hibernate required lots of work to get it working.

In Django, I define the class by extending the base model - and that's it. No <a class="zem_slink" title="XML" href="http://en.wikipedia.org/wiki/XML" rel="wikipedia">XML</a>, no bean to column mapping, nothing. I can sync DB in come command, start a shell and start playing with my ORM - I can test and know right from that shell if the model will satisfy all the data access requirements that I need without writing any view or procedure. This is more to do with Python being a scripting language but I think Django makes it a charm to use.
<h2>The Admin Site</h2>
In a Java web application, it can take from days to weeks to define views that will let you do basic <a class="zem_slink" title="Create, read, update and delete" href="http://en.wikipedia.org/wiki/Create%2C_read%2C_update_and_delete" rel="wikipedia">CRUD</a> with your models - it might be possible to get this done faster, but it still needs work. Django provides a really rich and easy to use Admin site which will let you create data with just a few lines of code after you write your models.

This is important to me for two reasons -
<ul>
	<li>I can have the data being input to the application almost on the same day that I write the models. This lets me test my views etc with real data and also gives the users a feel of how they will enter the data. If they see any issues with the way they need to enter the fields, I can easily change the model as I create the views.</li>
	<li>Because of the Admin site, I do not need to build CRUD screens for most applications that will not have the user enter any data - if I am not building Facebook, or Twitter or a blogging engine, I can get away in many cases without the user entering anything at all. All I need in that case are good views.</li>
</ul>
<h2>Internationalization</h2>
I once worked on building a <a class="zem_slink" title="Java (software platform)" href="http://www.java.com" rel="homepage">Java application</a> GUI that needed to be translated to different languages - the answer was to use resource bundles. And it was a lot of work and testing. When I saw that Django settings has a language code, I was tempted to change it. I changed it to something I expected to fail. I changed it to my mother tongue <a class="zem_slink" title="Telugu language" href="http://en.wikipedia.org/wiki/Telugu_language" rel="wikipedia">Telugu</a> and reloaded the admin page - no restart required, only reload - and there it was all translated to Telugu and in correct grammar!

My models were in English, but a few minutes spent on <a class="zem_slink" title="Google Translate" href="http://translate.google.com" rel="homepage">Google Translate</a> and I had all my models with verbose names in Telugu. I could have given it to anyone who did not know English and with a suitable keyboard he could type out documents in Telugu and I could show them on the views! This for me is killer!
<h2>Permission groups</h2>
I once built a banking web application that was not the most well thought out architecture in the world. Among other things, one thing which we found hard to implement in the technologies that we used was permissions - there were so many roles and hierarchies that it became a mess to check who had access to what. We built our own permission checking system based on component ID's on the pages and some mapping XML in the server side - it was coded very well by a friend of mine - but it was pathetic to use.

Django provides a simple permission check system in the templates and a easy to add decorator based permission checking in the views. With this approach you can build the entire application and then secure just the parts that you need with very few changes to anything.
<h2>Model Forms</h2>
I don't like repeating myself or my code - so if someone tells me that after I define my models, I need to redefine all those fields in another class for a form or HTML - then fuck it!

Django model forms solve this problem for me - I just need to tell the form which model to use and I can even get a <a class="zem_slink" title="Form (web)" href="http://en.wikipedia.org/wiki/Form_%28web%29" rel="wikipedia">HTML form</a> from that class without writing any code.

This does not serve or suit all purposes - but as far as I have seen this serves 95% of the cases where I wanted to use Django. I might have to later go and enhance the forms and the look and feel or add some additional checks or build a custom form - but it gets the thing up and running very fast.
<h2>Pagination is easy</h2>
In the web application that I mentioned previously, we had to put in a fair bit of effort to get pagination working - in Django, all I need to do is create a generic view and tell it how many items per page. In most cases I feel there is no utility in letting users choose how many items they want to see without slowing the damn thing down - so it always suits me that I can tell it to paginate by 25 or 50 and I never have to do anything big to get pagination working.
<h2>Markdown and other filters</h2>
As much as I hate writing HTML, I hate the fact that a user can enter arbitrary HTML formatting that can ruin my page appearance. So I would rather prefer someone to write in Markdown. In Django, rendering Markdown to HTML is just a matter of adding '|markdown' filter in the template - cannot get more easier.

There are many other filters to escape HTML tags and so many other common operations that life becomes easy.
<h2>RSS Feeds, JSON API etc</h2>
I once did a Django POC where I needed to provide a JSON view of all the objects in a certain model - I was able to use the JSON serializer and get the view ready in 10 minutes. I can easily get an RSS or Atom feed in similar timelines.

When you are building a website, it is very helpful to have these things out of the box working for you.
<h2>Various applications in django.contrib and on the web</h2>
There are so many applications for Django that can be easily plugged in to get desired results. In the last few weeks since I started the Django project on the side, I have been able to make excellent progress using applications like django-search, django-registration,django.contrib.comments,django-mongodb -  I would be still a long way from where I am if I had to build any of these on my own or even if any of these required more than few lines of code to get things up and running.
<h2>Conclusion</h2>
This post is just a collection of things that I have found interesting in Django and which drive me to use it more - you will have your won experiences. Do share them in the comments.
---
layout: post
title: "Python and easy web scraping with scrapy - building some useful data sets of Hyderabad bus routes"
date: 2014-03-02 21:16
comments: false
categories:
---

Web scraping has been around for a long time, and there are a lot many libraries to get started on this. The whole process is very standardised. Scrapy(<a href="http://scrapy.org/">http://scrapy.org/</a>), one of the easy to use Python web scraping tools is even available on the cloud - <a href="http://scrapinghub.com/">http://scrapinghub.com/</a>.

I will not go into what scraping is - I will instead go into something I did with this in a couple of hours to get some useful data.

<!--more-->
<h2>The problem</h2>
I come form Hyderabad in south India. Hyderabad along with Bangalore were the first cities where most of the IT majors started out in India. And yet most of the information about the city is hard to find online. Given that most people are not fully connected to the internet and/or are not mature enough to have a fully online integrated way of life, this doesn't create major life threatening hassles. Maybe some minor inconveniences with the lack of systems or many non-interconnected systems.

One of the major lifelines of Hyderabad is the city bus system - this has been around for many years and has been modernised too - however there are no such thing as live updates on bus timings or arrivals, or even an easy to use online route information. What is there is spread over many places, in bad formats and cannot be easily found even with Google. They do sell a booklet for around 10 bucks, but paper has its own issues.

Since I cannot solve the issue of live arrival times, I decided to get some data on the bus route information.
<h2>The current state of information</h2>
There is a website called <a href="http://www.hyderabadbusroutes.com/">http://www.hyderabadbusroutes.com/</a> which provides this information, in a very cluttered website full of ads, which is definitely a nice step. This is way better than having nothing. The information provided here is similar to the information on a lot of other sites on the internet - but is in a format that is easy to scrape.

So, I decided that I want to at least collate all this information from this website and keep it handy - you never know when or how you will get an idea to solve this problem.

One things I found that some of the information was wrong - the bus route 100H which I used for many years is totally wrong. And other routes have some mistakes. But even the website itself notes that the data might be incorrect. Hell I even sent an email to APSRTC ( the company which runs the buses) a few years ago asking for data and they had no reply. So I will take what I get.
<h2>The solution</h2>
The data on the website was organised into routes, and it listed all the information about the route like first and last buses and more interestingly, it listed all the stops. This is very important information which I saw in only one more place, in PDF format.

So I wrote a web scraper using scrapy.

The scraper is pretty simple - there are 29 pages on this site with tables showing the route and basic information. The main issue here was that the table did not have any ID or specific class attached to it. But luckily, there was only one table on the page. So, the crawler went through all the 29 pages and for each row, created a route information with number, start, stop and the timing details.

The stops for each route are listed on another page - I could have just used the same scraper to visit that link and get the information. But I needed the basic information too - so I made the first scraper write the data to a json file. Then reading this file, I wrote a scraper to visit each detail page and get the list of stops.

For both these cases, the pages were dynamically created when the scraper ran.
<h2>The code and the next plan</h2>
The code and the data that I created are both available at our Github page - <a href="https://github.com/supercoderz/hydbusroutes">https://github.com/supercoderz/hydbusroutes</a>

I now need to figure out some way to clean this data, remove bad data and use it in some form. One of the ideas in my head is to create a site where people and view the data, and edit it collaboratively to create a proper data set. Hopefully will have more updates on that soon.
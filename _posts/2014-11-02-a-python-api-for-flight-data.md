---
layout: post
title: "A Python API for flight data!"
date: 2014-11-02 00:49
comments: false
categories:
---

Of late I have become a fanatic of learning about aircraft, following aircraft on flightradar24, keeping track of tail numbers I flew on, asking questions and all that. I even track the on time performance of any flight I book or take before I plan my journey!

For all this I search on the browser and look up the data. Works for me. But I wanted to see if there was a way I could use this data in a program. Most sites don't offer a free API or even a paid API. And scraping data is forbidden mostly. So I end up looking up in browser and hard coding values in the code.

But then I thought, why can't I make a HTTP call from my code and get the data that I want? I wont store the data or use it for commercial purposes and all that, just use that data for any analysis in IPython notebook for example.

So I sat about creating one such API, its available here now - <a href="https://github.com/supercoderz/pyflightdata">https://github.com/supercoderz/pyflightdata</a>

At the moment I get all the data from flightradar24, but I am planning to add interfaces to get information from more sources to get more details on the airports, runways , weather maybe and even pictures.

Take a look and let me know what you think
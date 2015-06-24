---
layout: post
title: "Building a simple journey planner"
date: 2014-03-04 23:35
comments: false
categories:
---

In my last blog I mentioned about using a Scrapy crawler to build information about the bus routes in Hyderabad. Once I had the data in place, what to do with it?

Since I had all the routes and the stops on each route, I wanted to see if I could build a simple rudimentary journey planner. I have a basic idea about journey planners and wanted to see how it could be done.

The first problem was to decide what features I would have. Since I had no reasonable way to determine the travel times in Hyderabad, I decided to assume that all the stops take a unit time between them - this wasn't a bad start since I would know the route to take. The route might take a bit longer based on the traffic etc.

Since the final aim was to find the shortest path, I needed to build a graph, and run something like A* or Djikstra algorithm to get the shortest path. I am pretty weak in algorithms and cannot build one of these on my own - so I looked for a library that I can use.

I found a Python library called networkx - <a href="http://networkx.github.io/">http://networkx.github.io/</a> - which could help me.

This is a pretty good library which lets us add a complete path to the graph at once - either as connections between adjacent nodes or as a star mapping between the first node and all the nodes in the path.

I want to find the shortest route between two places with the interchanges - I don't care how many stops are there in between. So I decided to go with the star setup. Once the graph was built, it was easy to use the networkx algorithms to get the shortest path.

Once I had the path, I needed to know the bus routes on each path, for this I did 2 things - I used the multi graph in networkx which allows multiple edges between nodes, and on each edge I set the route number as an attribute. Using this information when I got the shortest path, I could list out the routes that we can take between the stops.

Simple.

The code for this can be found at - <a href="https://github.com/supercoderz/hydbusroutes">https://github.com/supercoderz/hydbusroutes</a>
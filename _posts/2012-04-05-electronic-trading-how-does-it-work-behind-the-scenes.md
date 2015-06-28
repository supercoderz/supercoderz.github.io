---
layout: post
title: "Electronic Trading - How does it work behind the scenes?"
date: 2012-04-05 00:36
comments: false
categories:
---

[caption id="" align="alignright" width="300" caption="The floor of the Chicago Board of Trade, a major commodities exchange in the United States. (Photo credit: Wikipedia)"]<a href="http://commons.wikipedia.org/wiki/File:Chicago_bot.jpg" target="_blank"><img  title="The floor of the Chicago Board of Trade, a maj..." src="http://upload.wikimedia.org/wikipedia/commons/thumb/0/0e/Chicago_bot.jpg/300px-Chicago_bot.jpg" alt="The floor of the Chicago Board of Trade, a maj..." width="300" height="220" /></a>[/caption]

<a  title="Electronic trading" href="http://en.wikipedia.org/wiki/Electronic_trading" rel="wikipedia" target="_blank">Electronic Trading</a> or Computer <a  title="Trade" href="http://en.wikipedia.org/wiki/Trade" rel="wikipedia" target="_blank">Trading</a> - whichever name you use for this - is how most financial markets work these days.Not just stocks or cash products,  even commodities are traded electronically. All this is driven by huge <a  title="Computer" href="http://en.wikipedia.org/wiki/Computer" rel="wikipedia" target="_blank">computer systems</a> that work very fast and very efficiently and handle huge volumes at large scales.

Its the best thing - until it fails.

This article today on Bloomberg only highlights some of the complications involved in these - <a href="http://www.bloomberg.com/news/2012-04-04/sec-puts-exchanges-on-notice-over-computer-driven-trades.html">http://www.bloomberg.com/news/2012-04-04/sec-puts-exchanges-on-notice-over-computer-driven-trades.html</a>.

Going back to the old way is not an option - these days when the competition is immense and everyone needs to give the <a  title="NYSE: WMT" href="http://www.google.com/finance?q=NYSE:WMT" rel="googlefinance" target="_blank">best price</a> to win over the others, large volumes are the only way to remain profitable and large volumes mean that you need to be quick and efficient. And that can only be achieved by using computers.

While I am no expert in these and definitely have no idea how each of the many different electronic platforms are built, I have a basic idea of the components that need to go in to these. In this post I will try to briefly describe some of these and give you an idea of the complexity that is involved in these applications. What this will serve to do is help you understand how these systems work next time you hear about a flash crash or exchange failure.

As a note - if you already work in the financial domain or build some of these systems for a living - I might oversimplify or may be outright wrong in some cases - please point out any flaws in the comments and I will definitely make corrections.

<!--more-->
<h2>How was it before electronic trading?</h2>
So what are exchanges? What is trading?

An exchange is an <a  title="Exchange (organized market)" href="http://en.wikipedia.org/wiki/Exchange_%28organized_market%29" rel="wikipedia" target="_blank">organized market</a> where the buyers and sellers come together to trade a set of agreed products under certain market guarantees provided by the market. You can read more about exchanges on this <a  title="Wikipedia" href="http://www.wikipedia.org" rel="homepage" target="_blank">Wikipedia article</a> or on the internet.

<a href="http://en.wikipedia.org/wiki/Exchange_(organized_market)">http://en.wikipedia.org/wiki/Exchange_(organized_market)</a>

Before electronic trading become common, the buyers and sellers would gather and provide the prices where they were willing to <a  title="List of The Price Is Right pricing games" href="http://en.wikipedia.org/wiki/List_of_The_Price_Is_Right_pricing_games" rel="wikipedia" target="_blank">buy or sell</a> the product that is being traded. According to the market guidelines, these bids would be matched with one another and deals would be agreed. The exchange would then provide means to clear the trades and settle the deal.

If you have seen old movies about the stock markets, and if you had seen people gathering around in a circle and shouting prices and all - that was how it was.

All this was slow, took time and could handle only so much of volume. Hence the need for speed.
<h2>The different things needed for trading</h2>
What do we need if we want to trade a product?
<ol>
	<li>We need a clearly defined product, which we understand along with all the terms attached to it</li>
	<li>We need participants in the market willing to buy and sell the product</li>
	<li>We need a price to be set on the product, or a price range to be defined for the product</li>
	<li>We need a mechanism to match the buyers and sellers and create deals</li>
	<li>Once a deal is agreed, the records and accounts of the deal need to be maintained</li>
</ol>
All these are different components that need to work together and efficiently in order for the whole trading platform to work. If any one of these fails then the whole system will fail.

Lets discuss each of these in details.
<h2>The product</h2>
The product is the core of all this. The exchange defines a product. This product has a face value, sometimes a minimum size, conditions for delivery or settlements. Every participant who wants to trade in this product must clearly understand the product and associated terms. They must also understand the cost of owning or selling the product, the commissions if any and the means to settle or deliver the product.

Since the trading is electronic in nature, all the data related to the product needs to be maintained and distributed to the participants electronically. All this meta data, also called static data, is very essential for any <a  title="Market participant" href="http://en.wikipedia.org/wiki/Market_participant" rel="wikipedia" target="_blank">market participant</a>. Without this they will not be able to make an informed decision and trade on the product. Each participant maintains all the data they receive from the exchange and there are means for the exchange to communicate any changes and updates to all the participants.
<h2>The participants</h2>
The participants are any one who wants to buy or sell the product. The participant needs a means to get on to the market, they need the product static data and they need a means to place and execute orders. When a participant wants to get on to an exchange, they work with the exchange to provide necessary details and go through background checks so that they can be setup to trade on the exchange.

For electronic trading, the participants will not physically come to the market - they will connect to the computer systems on the market. They connect using different means - s0me co-locate in the exchange alongside the computer systems of the exchange so that they get the quickest access to the market. Others connect to the market using dedicated high speed lines. The end goal for everyone is to be able to get on to the market quickly and faster than everyone else.

The participants trade on the market as per the terms of the market - they are required to follow the rules set for the market and any violation is not taken lightly. In addition they are also governed by the laws of the regulators of the country.
<h2>The price and the order</h2>
Trading happens on a price - when the buyers specify that they want to buy by paying up to a certain price, called the <a  title="Bid price" href="http://en.wikipedia.org/wiki/Bid_price" rel="wikipedia" target="_blank">bid price</a>, and the sellers specify that they are willing to sell for not less than a certain price, called the offer price, a market is created on the exchange.

This market has two kinds of aspects - the price and the order. The data about these two are called the <a  title="Market data" href="http://en.wikipedia.org/wiki/Market_data" rel="wikipedia" target="_blank">market data</a>.

As more participants place orders on the market the price moves up or down and these movements of price are called price ticks. These price ticks are available to all the participants and also to others using various means like dedicated feeds or using public websites. The information about the price movements is very important for the participants because based on this they can make an informed decision on whether they want to buy or sell the product and at what price.

Just like the price is important, the participants also need to know about orders placed by others on the market. Unless you know how much of the product is being sold at a given price, how can you decide the size of your order? You need both the price and the size available on the market, your decision is not correct.

In electronic trading, the exchange provides a specified format in which orders can be placed on the market - all participants know this format and place orders using this format.
<h2>The matching engine</h2>
Once the participants have placed orders on the market, there has to be a means of matching all these to create deals. Before electronic trading, the orders would be manually matched using the rules of the market. With the advent of computer based trading, all these rules for matching orders have been automated and the matching is performed very quickly. There are systems that can perform order matching in matter of microseconds.

The matching engine is the core of the exchange and determines the performance of the market. This is the most critical piece of the entire platform and must perform under the heavy volume without failing or missing any single order. These are built with various fail safe mechanisms and resiliency in order to guarantee that it is always available and can facilitate trading.
<h2>The book keeping and settlement process</h2>
Once a deal is reached, accounts need to be kept and the deal information needs to be sent to both the parties involved in the deal. There are dedicated systems on exchanges that facilitate sending these details to the participants. These could be real time feeds or end of day snapshots. These need to be capable of handling the volumes generated on the market.
<h2>Its not as simple as it sounds</h2>
All these systems that I described might sound simple - but in reality there are various levels of complexity built in to these like security, validation, resiliency and many many rules and regulations that will govern the market. In addition, to achieve the high speed and large transaction volumes that are needed, these will be built with architectures that can withstand and perform a peaks loads.
<h2>Putting it all together</h2>
Once we have a trading platform comprising of all these, it starts working as as soon as the participants start placing orders. As more and more orders are placed and executed, the market grows and performs. Since there is no way to artificially create a market, everything must come from the participants. If there are no participants then the market will fail. Even in a market where there are participants, they might be interested only in certain products.

The best markets out there perform and survive by providing those products which everyone is interested in.

The huge electronic trading market is created when a large number of participants connect to these markets and place orders based on up to date prices and in large numbers. The participants use their own systems and calculations to determine which price is good.

Crashes happen when a wrong price, a too low price or a price that can be taken advantage of is put on the market. Crashes also happen when there is no balance between the number of buyers and sellers and one side is larger than the other. Panic reactions from participants can also cause a crash. And of course there is the ever persistent threat of a computer system breaking down.

A good market will have means to identify all these issues before they happen and take corrective steps.
<h2>Conclusion</h2>
All these systems are not some poorly done job that fails one day. They are large complex computer systems that work well and fail under very few circumstances. But more often they fail because of the conditions created by the participants rather then the hardware itself.
<h6 class="zemanta-related-title" style="font-size:1em;">Related articles</h6>
<ul class="zemanta-article-ul">
	<li class="zemanta-article-ul-li"><a href="http://blogs.cisco.com/financialservices/with-high-frequency-trading-financial-firms-face-new-challenges/" target="_blank">With High-Frequency Trading, Financial Firms Face New Challenges</a> (blogs.cisco.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://www.prweb.com/releases/prweb2012/1/prweb9112726.htm" target="_blank">SunGard Identifies Ten Trends in Electronic Trading for 2012</a> (prweb.com)</li>
	<li class="zemanta-article-ul-li"><a href="http://dealbook.nytimes.com/2012/03/26/bats-chief-on-fridays-meltdown-my-stomach-sank/" target="_blank">BATS Chief on Friday's Meltdown: 'My Stomach Sank'</a> (dealbook.nytimes.com)</li>
</ul>
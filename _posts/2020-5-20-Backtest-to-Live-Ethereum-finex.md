---
layout: post
title: An experience creating my first live algorithm
---

I got my first experience making a trading algorithm my undergrad dissertation. In my dissertation I tried to include everything - blockchain data, reddit data, google and wiki trends, twitter data and TA and fed the data to a ML model to get predictions. The predictions still appeared good but, I was never satisfied with it and never went live. I learned what was not useful trading signal while working on the model. Most of the data I applied on were not (Unless I was working in a team).
<!--more-->

Since my graduation, I worked full time/part time/on/off for more than a year to finish my first model. Although profitable, I don't use the model rn so lets start with performance in some backtest and live data.

<p>
<iframe frameborder='0' scrolling='no' src='/static/backtest_ETH.html' class="embed-responsive-item" style="border:none; height: 100vh; width: 100vw; margin-left:-33vw;" ></iframe> 
<div style="font-size:12px; margin-left: -100px;">I hate HTML. If the chart does not look good, click <a href="/static/backtest_ETH.html">here</a> for a full page access.</div>
</p>

As you can see its a pretty good algorithm. I created a pretty sweet interface for live visualization too:
<img src="/static/screenshot.png">

Okay it dosen't look that good but I hate web developement and its great for me. There was even <a href="/static/historic_positions.png">this heatmap</a> showing the algo's historic positions. But the most important thing for a trading algorithm is to make money. I did not make money off this algorithm due to lack of capital, patience, risk management, and a pandemic.

# Developing
This algorithm uses Machine Learning. Features calculated off total longs and shorts position data provided by Bitfinex as a primary


# Going Live
Write how I got an bias too.


# Conclusion and Suggestions

In this post i will explain what I can about my algorithm, share my experience developing it and tell you why it didn't work
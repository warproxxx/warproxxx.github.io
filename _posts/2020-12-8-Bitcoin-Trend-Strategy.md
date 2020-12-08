---
layout: post
title: Profitable trading sytem for Bitcoin
---

I start this post by making a bold claim. Bitcoin is a trend based market. It moves in a similar way for a certain amount of time. Then it dosen't. Unless you trade in very short (seconds) or very long (months or years) timeframe or are very good (hedge funds with access to tons of resources/talent), you need to change with the trend or stay with it till it lasts instead fighting it to profit consistently. I provide an approach for dividing the market into medium term trends next. Then i do a series of realistic yet simple backtests to prove my point.

<!--more-->

## Trends
Different trends exist in a given time depending on how you look at the market. A n year cycle theory is popular way people divide the market to see the trend. It is one of the way of looking at long term trend. I establish my own simple method for this post. I focus this post for action since March 2020. Trends have existed in the market since inception (and probably do in all other markets too) but long term trend work differently and they are for another time.

I apply standard deviation on the daily close data of bitcoin to obtain its volatility. Then I apply 30 day moving average to smoothen it out. Then i apply gausian filter with a sigma of 3 to flatten it further. This will have a look ahead bias. To prevent this, i apply gausian filter to every data without giving access to the next days data. Now we have a completly unbiased value. The resulting chart looks like this:

<p>
<iframe frameborder='0' scrolling='no' src='https://interface.parsvirilis.com/static/plot_unbiased.html' class="embed-responsive-item" style="border:none; height: 605px; width: 1120px; margin-left:-200px; margin-bottom:-110px" ></iframe> 
</p>

The chart is available in [this url](https://interface.parsvirilis.com/static/plot_unbiased.html) and it will be updated live every day at UTC 00:03 with the previous day's close data.


The calculated volatility is in bottom. Every time the volatility changes direction, a line is drawn. In this approach, anything that lies between 2 lines is part of that trend. If the trend is smaller than 3 days i discard it. By this approach we have created a trend between following dates for Bitcoin:




## Approach
Then i backtest the system without any bias with the following rules:

1) Create features, and a set of parameters and ways by which which we can trade.

2) Wait 3 days until a trend is established. Then run a grid search in these 5 days of data to find an approach for trading in that trend.

3) Use the best parameter out of these 5 days to trade till the end of the trend. While trading use a worse than realistic fee - 0.075% per trade and worse than realistic slippage - 0.01% in both direction. If leverage of n is used, multiply slippage and fee by the same factor of n to be realistic. Also assume a worse than realistic interest rate (10% multiplied by n) to factor in the funding in the derivative market.

4) Provide result and chart. Apply the best parameter for one trend in another.

## Conclusion
Why is an obvios question people have in mind people reach here. There are different factors for different trends. I think one of the answer for why market works 


It is a very complicated question and i will focus on this in another post sometime in future.

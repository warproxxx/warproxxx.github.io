---
layout: post
title: Trend based trading system for Bitcoin
---

I start this post by making a bold claim. Bitcoin is a trend based market. It moves in a similar way for a certain amount of time. Then it dosen't. Unless you trade in very short (seconds) or very long (months or years) timeframe or are very good (hedge funds with access to tons of resources/talent), you need to change with the trend or stay with it till it lasts instead of fighting it to profit consistently. I provide an approach for dividing the market into medium term trends next. Then i do a series of realistic yet simple backtests to prove my point. In doing this i give ideas of various trading strategies suitable for different market conditions.

<!--more-->
Different trends exist in a given time depending on how you look at the market. A n year cycle theory is popular way people divide the market to see the trend. It is one of the way of looking at long term trend. I establish my own simple method for this post. I focus this post for action since March 2020. Trends have existed in the market since inception (and probably do in all other markets too) but long term trend work differently and they are for another time (In longer timeframe, i will say that we are in a trend in since March 2020).

## Trend Calculation
I apply standard deviation on the daily close data of bitcoin to obtain its volatility. Then I apply 30 day moving average to smoothen it out. Then i apply gausian filter with a sigma of 3 to flatten it further. This will have a look ahead bias. To prevent this, i apply gausian filter to every data without giving access to the next days data. Now we have a completly unbiased value. The resulting chart looks like this:

<p>
<iframe frameborder='0' scrolling='no' src='https://interface.parsvirilis.com/static/plot_unbiased.html' class="embed-responsive-item" style="border:none; height: 605px; width: 1120px; margin-left:-200px; margin-bottom:-110px" ></iframe> 
</p>
<font size="1">The chart is available in <a href="https://interface.parsvirilis.com/static/plot_unbiased.html" target="_blank">this url</a> and it will be updated live every day at UTC 00:03 with the previous day's close data. </font>

The calculated and processed volatility is in bottom. Every time the volatility changes direction, a line is drawn. In this approach, anything that lies between 2 lines is part of that trend. If the trend is smaller than 3 days i discard it. By this approach we have created a trend between following dates for Bitcoin:
<center>
<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-fymr{border-color:inherit;font-weight:bold;text-align:left;vertical-align:top}
.tg .tg-0pky{border-color:inherit;text-align:left;vertical-align:top}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-fymr">start</th>
    <th class="tg-fymr">end</th>
    <th class="tg-fymr">name</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0pky">02-28-2020</td>
    <td class="tg-0pky">03-23-2020</td>
    <td class="tg-0pky">corona_dump</td>
  </tr>
  <tr>
    <td class="tg-0pky">03-23-2020</td>
    <td class="tg-0pky">04-24-2020</td>
    <td class="tg-0pky">corona_recovery</td>
  </tr>
  <tr>
    <td class="tg-0pky">04-29-2020</td>
    <td class="tg-0pky">05-15-2020</td>
    <td class="tg-0pky">further_recovery</td>
  </tr>
  <tr>
    <td class="tg-0pky">05-15-2020</td>
    <td class="tg-0pky">06-04-2020</td>
    <td class="tg-0pky">9k_accumulation</td>
  </tr>
  <tr>
    <td class="tg-0pky">06-06-2020</td>
    <td class="tg-0pky">06-28-2020</td>
    <td class="tg-0pky">further_9k_accumulation</td>
  </tr>
  <tr>
    <td class="tg-0pky">07-01-2020</td>
    <td class="tg-0pky">07-24-2020</td>
    <td class="tg-0pky">final_9k_accumulation</td>
  </tr>
  <tr>
    <td class="tg-0pky">07-24-2020</td>
    <td class="tg-0pky">08-14-2020</td>
    <td class="tg-0pky">initial_takeoff</td>
  </tr>
  <tr>
    <td class="tg-0pky">08-14-2020</td>
    <td class="tg-0pky">09-03-2020</td>
    <td class="tg-0pky">12k_dump</td>
  </tr>
  <tr>
    <td class="tg-0pky">09-03-2020</td>
    <td class="tg-0pky">09-16-2020</td>
    <td class="tg-0pky">10k_accumulation</td>
  </tr>
  <tr>
    <td class="tg-0pky">09-16-2020</td>
    <td class="tg-0pky">10-11-2020</td>
    <td class="tg-0pky">10k_accumulation_2</td>
  </tr>
  <tr>
    <td class="tg-0pky">10-11-2020</td>
    <td class="tg-0pky">11-27-2020</td>
    <td class="tg-0pky">lift_off</td>
  </tr>
</tbody>
</table>
</center>

I gave each column names manually for clarity. All this data is without any lookahead bias. The same table would had been constructed day to day live as it happened using this mechanism (And i actually did it too).

## Strategy 

Then I created price features. We will use 10 Min OHLCV data (I calculated that using trade data), buy percentage (calculated from bitmex trade data), price change, big buy percentage (the % of buying higher than 10 BTC in Bitmex) and RSI (I usually hate TA and the mentality behind it but I have found that many other big boy algos use RSI too). Actually this is the entire data. Then I define a rough outline of how the system can trade. This is the pseudocode for the trading logic:





Here, n,x ,s are hyperparmateres. I wait 3 days until a trend is established. Then I run a grid search in these 3 days of data using the rough outline to find the parameters to use in that trend. Then i use the parameters created from these 3 days until the end of this trend.

2) Use the best parameter out of these 3 days of test to trade till the end of the trend. While trading use a worse than realistic fee - 0.075% per trade and worse than realistic slippage - 0.01% in both direction. If leverage of n is used, multiply slippage and fee by the same factor of n to be realistic. Also assume a worse than realistic interest rate (10% multiplied by n) to factor in the funding in the derivative market.


## Result

Provide result and chart. 


Total Return:
Total Sharpe Ratio:

On comparision:

Bitcoin Return:
Bitcoin Sharpe Ratio:

## Cross Comparision
Apply the best parameter for one trend in another.

## Conclusion
Mathematically and computationally speaking this is a fairly simple system. The hard part here is to see the markets as trend based parts.  The system itself is still trivial and can be improved a lot. But as the returns show, it works. And there is zero lookahead cheating, comission hacking, and p-hacking involved here like >99% of academic papers in this topic. The only place one can claim bias is the creation of pseduocode. I made that based on data from 2018/2019. Although you will have to take my word for it there, you can check by seeing that a system like this works then too. I am fairly certain plenty of crypto hedge funds use a similar but more advanced strategy.


## Further words
This is just one way of looking at trends. I have different system with same parameters since March 2020. So looking different, we are in a trend since then.

Why this kind of system works is an obvious question people may have in mind. I think there are different reason why something like this worked in 2013-18 and different reason for now. Now, one of the reason it may be working is human factor. Everyone has access to the same trade data. Everyone sees similary, create a similar system (by everyone i mean funds who matter. People lost doing dumb TA don't have enough influence to matter). But its just a rough guess. It is a very complicated question and i will focus on this in another post sometime in future.
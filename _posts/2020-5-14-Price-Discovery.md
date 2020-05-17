---
layout: post
title: Price Discovery in Bitcoin exchange
---

Twenty days ago on reddit I promised to explain in detail a Bitcoin price discovery algorithm which explains the role played by different spot, derivates and futures market towards determining the Bitcoin Price. And although a bit late, I have now applied Price Discovery and Spillover Analysis on BTC/USD data from Bitfinex, Bitmex, Bitstamp, Coinbase, HitBTC, Kraken, Poloniex, Binance, OkEx and CME futures to Bitcoin Price data from 2016. 

<!--more-->

# TL;DR

- As of May 15, 2020, Bitmex and CME Contracts and have played the strongest role in determining the Bitcoin price in the current run.

- Today derivative markets play a big role in determining the Bitcoin price. The fall in price starting 2018 conincides with an increase in price discovery role by CME contracts.

- During 2017/18, USDT based Bitfinex had the extremly dominant role in starting the pump. But as the price increased, trades on US based exchanges, Coinbase, Bitstamp, Kraken played the more dominat role in leading the price up.

- The Bitcoin market today is very different than the market in 2017. Traditional financial asset class like Futures contract are having more say in where the market moves. Today, against the early desires of anarchy, Bitcoin is more integrated and mainstream to traditional financial ideas. 


# Introduction

According to Investopedia, Price discovery is the "overall process, whether explicit or inferred, of setting the spot price or the proper price of an asset, security, commodity, or currency." And, Spillover analysis measures the extent to which unexpected deviations in one exchange influences other. So in summary, Price discovery identifies the leader exchanges whose traders have defined where the price will move to while Spillover analysis measures the effect extreme events (like high price rise falls) in one exchange effects on others. In cryptocurrencies as different exchanges operated very differently, we can know a lot about who is setting the price based on this information. 

Several academics have done price discovery and spillover analysis on Bitcoin Markets before me in the past and although they have done a fantastic job as most of my analysis is built up on their work, they do not include as many exchanges as I have. Consequently, they ignoring exchanges with big impacts like Bitfinex, OkEx and Huobi. Here i define all the algorithms i have used and the sources I have got the data from. This transparency will ensure that someone else can replicate this analysis. If you have good ideas, <a href="mailto:daniel@waterbot.xyz">contact me</a> and we can discuss further or I can send you the python code I used. If there is enough interest in this measurement I can setup a live interface that provides the live value.

## Price Discovery
In 1995 Hasbrouck and Gonzalo and Granger proposed a measure for information shares (IS) and component shares (CS). Their work has been used to study Gold, Bitcoin, futures derviatives and all. Some changes has been 


## Spillover Effect
Explain about what they are and algos. Redirect them to the original paper for details.

For calcualting I used an R package <a href="https://github.com/tomaskrehlik/frequencyConnectedness">frequencyConnectedness </a>


# Data Sources
I obtained historic trades data for Bitstamp and Kraken from <a href="http://api.bitcoincharts.com/v1/csv/">Bitcoincharts</a> and that of Bitmex from the <a href="https://public.bitmex.com/">exchange</a>. I used this data to create 5 Minute and 1 Minute OHLCV data. After verifying that there isn't much difference in calculated result using 5 Minute and 1 Minute data, I got 5 Minute OHLCV data for the exchanges from the following sources:

- **Poloniex, Bitfinex, Binance and HitBTC:** Exchange's API through <a href="https://github.com/ccxt/ccxt">CCXT</a>.
- **Huobi:** <a href="https://huobiapi.github.io/docs/spot/v1/en/#websocket-market-data">Official Websocket Market API</a>
- **Coinbase:** Official API using <a href="https://pypi.org/project/cbpro/">cbpro</a>
- **Kraken, Bitstamp:** I created candles from the trade data obtained from <a href="http://api.bitcoincharts.com/v1/csv/">Bitcoincharts</a>
- **OkEx:** <a href="https://bitdataset.com/api/">Bitdataset API</a>
- **CME:** Okay, this was was supposed to be tricky and expensive. I broke a TOS and scraped the data for free removing the expensive part from the equation. I will not go into detail about where I scraped this data from.
- **Bitmex:** I got Bitmex data from the <a href="https://www.bitmex.com/app/restAPI">API</a>. For futures contract, I chose the closest contract until it was 3 days till expiration. Then I chose the next month. This approach was advocated by __ and has been used by __ with success.

# Analysis

<p>
<iframe frameborder='0' scrolling='no' src='/static/price_discovery.html' class="embed-responsive-item" style="border:none; height: 605px; width: 1120px; margin-left:-200px" ></iframe> 
</p>

A full page version of this graph is available <a href="/static/price_discovery.html">here</a>

I also studied spill over effect

<p>
<iframe frameborder='0' scrolling='no' src='/static/spillover.html' class="embed-responsive-item" style="border:none; height: 605px; width: 1120px; margin-left:-200px" ></iframe> 
</p>

A full page version of this graph is available <a href="/static/spillover.html">here</a>
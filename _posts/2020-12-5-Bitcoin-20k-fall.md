---
layout: post
title: Dynamics behind bitcoins fall from 19.9k
---


I used trade data and orderbook snapshots from 15 spot and 6 derivative exchanges in their BTC/USD and/or BTC/USDT pair to attempt to find an explanation for Bitcoin's sharp price fall from 19.9k on December 1, 2020. <!--more-->In my analysis, I observed dynamics that told a story behind price movement in the Bitcoin Market.

# Summary
- At 11:10 UTC BTC price was at 19.9k. At this time orderbook in both the bid and the ask side decreased substantially. This stopped the roadblock from a potential breakout in either direction
- For the next few minutes selling increased while buying didn't. As the orderbook had grown fickle in both directions at this point, selling pushed it to the lower side.
- Afterwards, Liquidation was responsible for most of the sharp movement


## Data
I used data from the following exchanges and pairs:

- **BTC-USD Spot:** Binance, BinanceUS, Bitfinex, Bitstamp, Coinbase, FTX, FTXUS, Gemini, HitBTC, Kraken
- **BTC-USDT Spot:** Binance, Bitfinex, Bitmax, Bittrex, FTX, FTXUS, Huobi, Kraken, OKEx, Poloniex
- **BTC-USD Derivative:** Bitmex, Bybit, Bittrex, FTX, Huobi, OKEx
- **BTC-USDT Derivative:** Binance

I did not use TUSD, USDC, BUSD, and EUR markets here as I found them to be too small.

# Orderbook
I took a snapshot of the orderbook a few times every minute in all pairs depending on how often the orderbook changes. This way, I took more snapshots for orderbooks that change frequently and less for those who changeless. Then I calculated the average of the snapshots to get the average amount needed for a 100$ move in both directions in that minute. I grouped the exchanges into spot and derivative and summed the total depth to obtain this chart:

<p>
<iframe frameborder='0' scrolling='no' src='/static/100_depth.html' class="embed-responsive-item" style="border:none; height: 605px; width: 1120px; margin-left:-200px; margin-bottom:-110px" ></iframe> 
</p>
<br/><br/>
There is plenty of information to digest in this chart. As bitcoin reached 19.9k heavy buy and sell orders popped up at 11:06. But at 11:10 they were removed, and the amount required for the price to move fell substantially. I manually plotted the random snapshot of orderbooks of all the exchange and found that most of this action took place at <a href="/static/coinbase.html" target="_blank">Coinbase</a> in spot and <a href="/static/bitmex.html" target="_blank">Bitmex</a> in derivative (<a href="/static/kraken.html" target="_blank">Kraken</a>, <a href="/static/bybit.html" target="_blank">Bybit</a>, <a href="/static/binancefutures.html" target="_blank">BinanceFutures</a> and <a href="/static/huobi_swap.html" target="_blank">Huobi</a>'s depth for comparision). This fall in orderbook depth made a movement in either direction more likely.

# Trades
I summed the amount of buying and selling that took place in every 30-second interval. Then I divided each interval's amount by the last 100 intervals' moving average to get a normalized amount. Then I plotted the 10 intervals (5 mins) moving average of the 30-second data to obtain this (all the charts below are log scaled in y-axis):

<iframe frameborder='0' scrolling='no' src='/static/combined_buy_sell.html' class="embed-responsive-item" style="border:none; height: 605px; width: 1120px; margin-left:-200px; margin-bottom:-110px" ></iframe> 
<font size=1>Chart that divides spots and derivatives <a href="/static/bitmex.html" target="_blank">here</a>. I find the combined to be more clear.</font>

After 11:10, both buying and selling fell. But buying fell much faster than selling. I created a cumulative sum of the amount sold/brought since 11:00 UTC below to get the below chart:

<iframe frameborder='0' scrolling='no' src='/static/cum_sum.html' class="embed-responsive-item" style="border:none; height: 605px; width: 1120px; margin-left:-200px; margin-bottom:-110px" ></iframe> 
<br/>
The increase in selling even before the fall is more evident here. This increase in selling was responsible price going low.

<br/><br/>

# Liquidations
I only have access to BitMEX's liquidation data. So my analysis of this section is based on that. I don't think it's a stretch to assume that BitMEX data will represent other derivative markets. And I have <a href="/Price-Discovery/">shown in the past</a> how derivative frequently lead spot markets.

From 11:20 UTC to 11:26 UTC, the bitcoin price fell from ~19800 to ~19100. 150 Million $ was traded in Bitmex at this time. Of them, 6.8$ was liquidation. ~5% of all the trades during this time were liquidations. There were a total of 12 liquidations.

Zooming in: 
- Bitcoin price fell ~250$ from 19808$ to 19568$ from 11:20:34 to 11:20:51 in a 17-second interval. 8% of the volume was liquidations. 5 of the 12 liquidations took place at this time.
- Bitcoin price fell ~200$ from 19540$ to 19333$ from 11:25:20 to 11:25:33 in a 13-second interval. 36% of the volume were liquidations. 5 of the 12 liquidations took place at this time.



All this information tells a story on the dynamics on play in the bitcoin market. In this particular case, change in orderbook depth allowed a movement to happening. Selling pushed the price low. Liquidations pushed the price lower. 

Not all price movements today are like this. But we have seen how bitcoin sharply in short intervals nowadays. And this dynamic provides a small story in the trade level behind the cause of movements happening fast.


# Final Words
I wonder how backtesting an automatic trading system based on the orderbook change and trades keeping in mind liquidation would perform. I am sure it would beat buy and hold if appropriately done (as I have been beating it with a more straightforward system). But unfortunately, I didn't collect this data in the past, and i can't afford paying over 10k€ (Kaiko charges 2k€ per exchange for the trade data, and who knows how much for the obook) for these data. Fortunately, I am collecting it now so I will have data for future movements, and with enough data, I will have better analysis and better systems in the future.
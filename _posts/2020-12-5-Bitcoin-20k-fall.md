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

I did not use TUSD, USDC, BUSD, and EUR markets here as I found them to be too small

# Orderbook
I took a snapshot of the orderbook a few times every minute in all pairs depending on how often the orderbook changes. This way, I took more snapshots for orderbooks that change frequently and less for those who changeless. Then I calculated the orderbook depth - the amount needed for a 100$ move in both directions. Then i averaged the snapshots of the amount required for a 100$ movement in the given minute to get the average amount required for that minute for the given pair in the given exchange. Then I grouped the data into spot and derivative by adding in such pair to obtain this chart

<p>
<iframe frameborder='0' scrolling='no' src='/static/100_depth.html' class="embed-responsive-item" style="border:none; height: 605px; width: 1120px; margin-left:-200px; margin-bottom:-110px" ></iframe> 
</p>
<br/><br/>
The Red line is the sell-side, and the green line the buy-side. When the red line is higher, it means the sell-side is significant, and it is hard for the price to rise. Similarly, when the green line is significant, the buy-side is bigger. Its axis is on the left. The light blue line is close price and golden line the average price of BTC. Its axis is on the right. The depth in spot exchanges have been scaled up a bit in the chart as I think comparing spots to derivative depth 1:1 is not fair.

There is plenty of information to digest in this chart. As bitcoin reached 19.9k heavy buy and sell orders popped up at 11:06. But at 11:10, they were removed, and the amount required for the price to move fell substantially. I manually plotted the random snapshot of orderbooks of all the exchange and found that most of this action took place at <a href="/static/coinbase.html" target="_blank">Coinbase</a> in spot and <a href="/static/bitmex.html" target="_blank">Bitmex</a> in derivative (<a href="/static/kraken.html" target="_blank">Kraken</a>, <a href="/static/bybit.html" target="_blank">Bybit</a>, <a href="/static/binancefutures.html" target="_blank">BinanceFutures</a> and <a href="/static/huobi_swap.html" target="_blank">Huobi</a>'s depth for comparision). This fall in orderbook depth made a movement in either direction more likely.

# Trades
I summed the amount of buying and selling that took place in every 30-second interval. Then I divided each interval's amount by the last 100 intervals' moving average to get a normalized amount. Then I plotted the 10 intervals (5 mins) moving average of the 30-second data to obtain this:

<iframe frameborder='0' scrolling='no' src='/static/combined_buy_sell.html' class="embed-responsive-item" style="border:none; height: 605px; width: 1120px; margin-left:-200px; margin-bottom:-110px" ></iframe> 
<font size=1>Chart that divides spots and derivatives <a href="/static/bitmex.html" target="_blank">here</a>. I find the combined to be more clear.</font>

The green and red line show the intensity of buying/selling. Higher green line means that buying is much higher than the average buying in the last 50 minutes. If the line is rising faster, it means buying/selling is increasing heavily. If it is falling fast, the average buying/selling is decreasing. For buying and selling, a log axis is used on the right side. For value, the orange line's axis is on the right.

After 11:10, both buying and selling fell on average. But buying fell much faster than selling. I created a cumulative sum of the amount sold/brought since 11:00 UTC below to get the below chart:

<iframe frameborder='0' scrolling='no' src='/static/cum_sum.html' class="embed-responsive-item" style="border:none; height: 605px; width: 1120px; margin-left:-200px; margin-bottom:-110px" ></iframe> 
<br/>
Here the green line shows the total amount of bitcoin brought in all these exchanges, and the red shows the amount sold. Its log axis is on the left. The orange line is the price. Here it is visible that buying starts bigger than selling. But after the slight dip at 11:10, selling keeps increasing, eventually leading to a price crumble.

<br/><br/>

# Liquidations
This section's analysis is only based on BitMEX's liquidation data. While concluding, I assume that Bitmex's liquidation represents the amount in other derivative exchanges. I think it's a fair assumption given that <a href="/Price-Discovery/">I have shown</a> how the bitmex derivative market frequently leads spot markets.

From 11:20 UTC to 11:26 UTC, the bitcoin price fell from ~19800 to ~19100. 150 Million $ was traded in Bitmex at this time. Of them, 6.8 Million $ was liquidation. ~5% of all the trades during this time were liquidations. There were a total of 12 liquidations.

Zooming in the massive role of liquidation gets more clear:
- Bitcoin price fell ~250$ from 19808$ to 19568$ from 11:20:34 to 11:20:51 in a 17-second interval. 8% of the volume was liquidations. 5 of the 12 liquidations took place at this time.
- Bitcoin price fell ~200$ from 19540$ to 19333$ from 11:25:20 to 11:25:33 in a 13-second interval. 36% of the volume was liquidations. 5 of the 12 liquidations took place at this time.

This considerable proportion and liquidation occurrence in sharp movements shows how liquidations enhance trends in the bitcoin market.

# Conclusion
All this information tells a story on the dynamics of play in the bitcoin market. In this particular case, a change in orderbook depth allowed a movement to happen. Selling pushed the price low. Liquidations pushed the price lower. Fundamental factors define market dynamic in the long run, and this zoomed-in analysis in the market dynamic might give some people biased conclusions. But, Market dynamics are still influential in the short/medium-term price movement. We have seen how bitcoin moves sharply in short intervals nowadays. And this case provides a good insight into how/why bitcoin moves sharply in such a way and is thus good knowledge for traders.

# Final Words
I wonder how backtesting an automatic trading system based on the orderbook change and trades keeping in mind liquidation would perform. I am sure it would beat buy and hold if appropriately done (as I have been beating it with a more straightforward system). But unfortunately, I didn't collect this data in the past, and i can't afford paying over 10k€ (Kaiko charges 2k€ per exchange for the trade data, and who knows how much for the obook) for these data. Fortunately, I am collecting it now, so I will have better analysis and insights to share with enough data in the future. And hopefully better trading systems for me personally!
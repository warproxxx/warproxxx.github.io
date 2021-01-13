---
layout: post
title: Price Discovery in Bitcoin exchange
---

I applied price discovery algorithms to 5 Min OHLCV data from Bitmex and CME contracts and Bitstamp, Coinbase, HitBTC, Kraken, Poloniex, Binance, and OkEx BTCUSD/BTCUSDT markets from March 2016 to May 2020 to analyze the leading bitcoin exchanges. <!--more--> Some exciting observations I made are:

- Before the 2017/18 bull run, Bitfinex dominated the price discovery process. They started the run. But as the price increased, trades on other exchanges, Binance and Bitstamp played a more dominant role in leading the price up. 

- Since then, CME Contracts and Bitmex contracts have had an increasing role in price discovery. Today Bitmex and CME Contracts play the most substantial role in determining the direction of Bitcoin price. 

- In 2020, market dominance by Bitmex has been negatively correlated with price. Dominance by Bitfinex, Huobi and OkCoin has had high positive correlation with price.


# Introduction
<a href="#analysis">(Skip to the analysis section if you want to avoid the theory)</a>

Price discovery is the overall process of setting the price of an asset. Price discovery algorithms identify the leader exchanges whose traders define the price. Several academics have already done various price discovery analysis on Bitcoin Markets. And although they used novel approaches, they ignore exchanges with huge real volumes like Bitfinex, OkEx, and Huobi.

There are different approaches to measure price discovery. The most popular ones are <a href="https://sci-hub.tw/https://www.jstor.org/stable/2329348?seq=1">Hasbrouck (1995)</a> and <a href="https://sci-hub.tw/https://www.jstor.org/stable/1392518?seq=1">Gonzalo and Granger (1995)</a>. <a href="https://sci-hub.tw/https://www.jstor.org/stable/2329348?seq=1">Hasbrouck (1995)</a> measures price discovery by calculating something called an efficient price. They define efficient price as the actual true price and measure discovery by measuring each exchange's variance from the efficient price. <a href="https://sci-hub.tw/https://www.jstor.org/stable/1392518?seq=1">Gonzalo and Granger (1995)</a> do it a bit differently by decomposing the price series into components that represent the deviation from the efficient price. Both of these work have a different level of noise and in an attempt to decrease the noise <a href="https://sci-hub.tw/https://www.sciencedirect.com/science/article/abs/pii/S0927539813000340">Putniņš (2013)</a> combined both of them to create a new measure. 

All of the algorithms mentioned above assume cointegration, random walk, and a common efficient price. And although cointegration is a fair assumption, I do not feel comfortable assuming random walk and common efficient price in Bitcoin Markets. This is a speculation based market where significant trades can cause change. So although the measures in previous paragraphs are better studied, I use a new and little know method by <a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3297869">De Blasis (2019)</a>. De Blasis assumes that "the fastest price to reflect new information releases a price signal to the other slower price series." This assumption seems fair in Bitcoin Markets. <a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3297869">De Blasis (2019)</a> tested their work in different futures and ETF contracts and found that the index's lead discovery in line with accepted literature adding credibility to their work.

<a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3297869">De Blasis (2019)</a> uses Markov Chains to measure Price Discovery. Markov Chain is a way of modeling random processes. Without going into the mathematical details their work calculates Price Discovery using the following steps:

- Data is first grouped into a daily interval. Then inside each daily interval's 5-minute candles, the change in prices between the current time t and previous time t-1 is calculated. The difference across the same time t across all exchanges in a given day is juxtaposed to create an initial matrix.
- The initial matrix is used to create a Transition Matrix, which measures the probability of price changing to something else at time t+1 for its state at t.
- Then Markov Chain based algorithms are used to measure the influence an exchange at time t had over all other exchanges' price movement at time t+1 individually.
- Reduction and normalization is done to this data. In the end, each exchange receives a single number that sums to 1 for a given day. 

<a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3297869">De Blasis (2019)</a> names this number Price Leadership Share (PLS). High PLS indicates a large role in price discovery. As the sum of the numbers is 1, they can be looked at as a percentage contribution. I recommend reading the <a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3297869">original paper</a> if you are interested to know more about the mathematical detail.


# Data
<a href="https://sci-hub.tw/https://amstat.tandfonline.com/doi/abs/10.1080/07350015.2000.10524857?journalCode=ubes20">Andersen (2000)</a> argues that 5 Minute window provides the best trade-off between getting enough data and avoiding noise. In one of the first work on Bitcoin's Price Discovery, <a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=2691456">Brandvold et al. 2015</a> had used 5M window. So I obtained 5M OHLCV data using the following sources:

- **Poloniex, Bitfinex, Binance and HitBTC:** Exchange's API through <a href="https://github.com/ccxt/ccxt">CCXT</a>.
- **Huobi:** <a href="https://huobiapi.github.io/docs/spot/v1/en/#websocket-market-data">Official Websocket Market API</a>
- **Coinbase:** Official API using <a href="https://pypi.org/project/cbpro/">cbpro</a>
- **Kraken, Bitstamp, OkCoin:** I created candles from the trade data obtained from <a href="http://api.bitcoincharts.com/v1/csv/">Bitcoincharts</a>
- **OkEx:** <a href="https://bitdataset.com/api/">Bitdataset API</a>
- **CME:** Okay, this was was supposed to be tricky and expensive. I broke a TOS and scraped the data for free, removing the expensive part from the equation. I will not go into detail about where I scraped this data.
- **Bitmex:** I got Bitmex data from the <a href="https://www.bitmex.com/app/restAPI">API</a>. 

Futures data are different from other data because multiple futures contract trades at the same time. I formed a single data from the multiple time series by selecting the nearest contract until it was three days from expiration. I used the next contract when the contract was three days from expiration. This approach was advocated by <a href="https://onlinelibrary.wiley.com/doi/abs/10.1002/%28SICI%291096-9934%28199909%2919%3A6%3C619%3A%3AAID-FUT1%3E3.0.CO%3B2-M">Booth et al ( 1999)</a> and and was also used by <a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3353583">Alexander et al (2019)</a> in the cryptocurrency market.

# Analysis

<p>
<iframe frameborder='0' scrolling='no' src='/static/price_discovery.html' class="embed-responsive-item" style="border:none; height: 605px; width: 1120px;" ></iframe> 
</p>

<div style="font-size:12px; margin-left: -100px;">A full-page version of this graph is available <a href="/static/price_discovery.html">here</a></div>

<br/>
In the figure above, each colored line shows the total influence the exchange had towards the discovery of Bitcoin Price on that day. Its axis is on the left. The black line shows a moving average of the bitcoin price at the close in Bitfinex for comparison. The chart was created by plotting the EMA of price and dominance with a smoothing factor of 0.1. This was done to eliminate the noise. Let's start looking from the beginning. We start with a slight Bitfinex dominance at the start. When the price starts going up, Bitfinex's influence does too. This was the time large Tether printing was attributed to the rise of price by many individuals. But Bitfinex's influence wanes down as the price starts rising (remember that the chart is an exponential moving average. Its a lagging indicator). Afterward, exchanges like Binance and Bitstamp increase their role, and there isn't any single leader in the run. So although Bitfinex may have been responsible for the initial pump trades on other exchanges were responsible for the later rally.

CME contracts were added to our analysis in February 2018. Initially, they don't have much influence. On a similar work <a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3383147">Alexandar and Heck (2019)</a> noted that initially CBOE contracts had more influence. CBOE later delisted Bitcoin futures so I couldn't get that data. Overall, Bitmex and CME contracts have been averaging around 50% of the role in price discovery. To make the dominance clear, look at <a href="/static/bitmex_dom.html" target="_blank">this chart</a> where I add Bitmex Futures and Perp contract's dominance figure to create a single dominance index. There bitmex leads 936 of the total 1334 days (Bitfinex leads 298 days and coinbase and binance get 64 and 6 days). That is a lot. One possible reason for this might be Bitmex's low trading fee. Bitmex has a very generous -0.025% maker fee and price discovery tend to occur primarily in the market with smaller trading costs (<a href="https://sci-hub.tw/10.1002/(sici)1096-9934(199909)19:6%3C619::aid-fut1%3E3.0.co;2-m">Booth et al, 1999</a>; <a href="https://sci-hub.tw/10.1002/fut.20302">Hsieh et al., 2008</a>). It may also be because our market is mature. In mature markets, futures lead the price discovery.


<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-fymr{border-color:inherit;font-weight:bold;text-align:left;vertical-align:top}
.tg .tg-0pky{border-color:inherit;text-align:left;vertical-align:top}
</style>
<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-7btt{border-color:inherit;font-weight:bold;text-align:center;vertical-align:top}
.tg .tg-0pky{border-color:inherit;text-align:left;vertical-align:top}
</style>
<table class="tg">
<caption style="caption-side:bottom; font-size:12px">Table 1: Days Lead</caption>


<thead>
  <tr>
    <th class="tg-7btt">Exchange</th>
    <th class="tg-0pky">bitmex_futures</th>
    <th class="tg-0pky">bitfinex</th>
    <th class="tg-0pky">coinbase</th>
    <th class="tg-0pky">bitmex</th>
    <th class="tg-0pky">okex</th>
    <th class="tg-0pky">binance</th>
    <th class="tg-0pky">cme</th>
    <th class="tg-0pky">bitstamp</th>
    <th class="tg-0pky">okcoin</th>
    <th class="tg-0pky">kraken</th>
    <th class="tg-0pky">poloniex</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-7btt"> Days</td>
    <td class="tg-0pky">571</td>
    <td class="tg-0pky">501</td>
    <td class="tg-0pky">102</td>
    <td class="tg-0pky">88</td>
    <td class="tg-0pky">34</td>
    <td class="tg-0pky">12</td>
    <td class="tg-0pky">8</td>
    <td class="tg-0pky">7</td>
    <td class="tg-0pky">6</td>
    <td class="tg-0pky">4</td>
    <td class="tg-0pky">1</td>
  </tr>
</tbody>
</table>

Out of 1334 days in the analysis, Bitmex futures leads the discovery in 571 days or nearly 43% of the duration. Bitfinex leads for 501 days. Bitfinex's high number is due to its extreme dominance in the early days. 

<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-lboi{border-color:inherit;text-align:left;vertical-align:middle}
.tg .tg-9j3s{border-color:inherit;font-weight:bold;text-align:right;vertical-align:middle}
.tg .tg-uzvj{border-color:inherit;font-weight:bold;text-align:center;vertical-align:middle}
</style>
<table class="tg">
<caption style="caption-side:bottom; font-size:12px">Table 2: Correlation between the close price and Exchange's dominance index</caption>
<thead>
  <tr>
    <th class="tg-9j3s">Exchange</th>
    <th class="tg-uzvj">binance</th>
    <th class="tg-uzvj">huobi</th>
    <th class="tg-uzvj">cme</th>
    <th class="tg-uzvj">okcoin</th>
    <th class="tg-uzvj">bitmex_futures</th>
    <th class="tg-uzvj">okex</th>
    <th class="tg-uzvj">hitbtc</th>
    <th class="tg-uzvj">kraken</th>
    <th class="tg-uzvj">poloniex</th>
    <th class="tg-uzvj">bitstamp</th>
    <th class="tg-uzvj">bitfinex</th>
    <th class="tg-uzvj">coinbase</th>
    <th class="tg-uzvj">bitmex</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-9j3s">Correlation</td>
    <td class="tg-lboi">0.809190</td>
    <td class="tg-lboi">0.715667</td>
    <td class="tg-lboi">0.648058</td>
    <td class="tg-lboi">0.644432</td>
    <td class="tg-lboi">0.577147</td>
    <td class="tg-lboi">0.444821</td>
    <td class="tg-lboi">0.032649</td>
    <td class="tg-lboi">-0.187348</td>
    <td class="tg-lboi">-0.365175</td>
    <td class="tg-lboi">-0.564073</td>
    <td class="tg-lboi">-0.665008</td>
    <td class="tg-lboi">-0.695115</td>
    <td class="tg-lboi">-0.752103</td>
  </tr>
</tbody>
</table>

Binance, Huobi, CME, and OkCoin had the most significant correlation with the close price. Bitmex, Coinbase, Bitfinex, and Bitstamp's dominance were negatively correlated. This was very interesting. To know more, I captured a yearwise correlation.

<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-cly1{text-align:left;vertical-align:middle}
.tg .tg-1wig{font-weight:bold;text-align:left;vertical-align:top}
.tg .tg-wa1i{font-weight:bold;text-align:center;vertical-align:middle}
.tg .tg-zt7h{font-weight:bold;text-align:right;vertical-align:middle}
</style>
<table class="tg">
<caption style="caption-side:bottom; font-size:12px">Table 3: Yearwise Correlation between the close price and Exchange's dominance index</caption>
<thead>
  <tr>
    <th class="tg-zt7h"></th>
    <th class="tg-zt7h">index</th>
    <th class="tg-zt7h">2016</th>
    <th class="tg-zt7h">2017</th>
    <th class="tg-zt7h">2018</th>
    <th class="tg-zt7h">2019</th>
    <th class="tg-1wig">2020</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-wa1i">0</td>
    <td class="tg-cly1">bitfinex</td>
    <td class="tg-cly1">0.028264</td>
    <td class="tg-cly1">-0.519791</td>
    <td class="tg-cly1">0.829700</td>
    <td class="tg-cly1">-0.242631</td>
    <td class="tg-cly1">0.626386</td>
  </tr>
  <tr>
    <td class="tg-wa1i">1</td>
    <td class="tg-cly1">bitmex</td>
    <td class="tg-cly1">0.090758</td>
    <td class="tg-cly1">-0.752297</td>
    <td class="tg-cly1">-0.654742</td>
    <td class="tg-cly1">0.052242</td>
    <td class="tg-cly1">-0.584956</td>
  </tr>
  <tr>
    <td class="tg-wa1i">2</td>
    <td class="tg-cly1">bitmex_futures</td>
    <td class="tg-cly1">-0.011323</td>
    <td class="tg-cly1">-0.149281</td>
    <td class="tg-cly1">-0.458857</td>
    <td class="tg-cly1">0.660135</td>
    <td class="tg-cly1">0.095305</td>
  </tr>
  <tr>
    <td class="tg-wa1i">3</td>
    <td class="tg-cly1">bitstamp</td>
    <td class="tg-cly1">0.316291</td>
    <td class="tg-cly1">-0.373688</td>
    <td class="tg-cly1">0.600240</td>
    <td class="tg-cly1">-0.255408</td>
    <td class="tg-cly1">-0.407608</td>
  </tr>
  <tr>
    <td class="tg-wa1i">4</td>
    <td class="tg-cly1">coinbase</td>
    <td class="tg-cly1">-0.505492</td>
    <td class="tg-cly1">-0.128336</td>
    <td class="tg-cly1">-0.351794</td>
    <td class="tg-cly1">-0.410874</td>
    <td class="tg-cly1">-0.262036</td>
  </tr>
  <tr>
    <td class="tg-wa1i">5</td>
    <td class="tg-cly1">hitbtc</td>
    <td class="tg-cly1">0.024425</td>
    <td class="tg-cly1">0.486229</td>
    <td class="tg-cly1">0.104912</td>
    <td class="tg-cly1">-0.200203</td>
    <td class="tg-cly1">0.308862</td>
  </tr>
  <tr>
    <td class="tg-wa1i">6</td>
    <td class="tg-cly1">kraken</td>
    <td class="tg-cly1">0.275797</td>
    <td class="tg-cly1">0.422656</td>
    <td class="tg-cly1">0.294762</td>
    <td class="tg-cly1">-0.064594</td>
    <td class="tg-cly1">-0.192290</td>
  </tr>
  <tr>
    <td class="tg-wa1i">7</td>
    <td class="tg-cly1">poloniex</td>
    <td class="tg-cly1">0.177616</td>
    <td class="tg-cly1">-0.087090</td>
    <td class="tg-cly1">0.230987</td>
    <td class="tg-cly1">-0.135046</td>
    <td class="tg-cly1">-0.154726</td>
  </tr>
  <tr>
    <td class="tg-wa1i">8</td>
    <td class="tg-cly1">binance</td>
    <td class="tg-cly1">NaN</td>
    <td class="tg-cly1">0.865295</td>
    <td class="tg-cly1">0.706725</td>
    <td class="tg-cly1">-0.484130</td>
    <td class="tg-cly1">0.265086</td>
  </tr>
  <tr>
    <td class="tg-wa1i">9</td>
    <td class="tg-cly1">okcoin</td>
    <td class="tg-cly1">NaN</td>
    <td class="tg-cly1">0.797682</td>
    <td class="tg-cly1">0.463455</td>
    <td class="tg-cly1">-0.010186</td>
    <td class="tg-cly1">-0.160217</td>
  </tr>
  <tr>
    <td class="tg-wa1i">10</td>
    <td class="tg-cly1">huobi</td>
    <td class="tg-cly1">NaN</td>
    <td class="tg-cly1">0.748489</td>
    <td class="tg-cly1">0.351514</td>
    <td class="tg-cly1">-0.298418</td>
    <td class="tg-cly1">0.434164</td>
  </tr>
  <tr>
    <td class="tg-wa1i">11</td>
    <td class="tg-cly1">cme</td>
    <td class="tg-cly1">NaN</td>
    <td class="tg-cly1">NaN</td>
    <td class="tg-cly1">-0.616407</td>
    <td class="tg-cly1">0.694494</td>
    <td class="tg-cly1">-0.012962</td>
  </tr>
  <tr>
    <td class="tg-wa1i">12</td>
    <td class="tg-cly1">okex</td>
    <td class="tg-cly1">NaN</td>
    <td class="tg-cly1">NaN</td>
    <td class="tg-cly1">-0.618888</td>
    <td class="tg-cly1">-0.399567</td>
    <td class="tg-cly1">0.432474</td>
  </tr>
</tbody>
</table>

Price movement is pretty complicated. If one factor, like a dominant exchange, could explain it, everyone would be making money trading. With this disclaimer out of the way, let us try to make some conclusions. This year Bitfinex, Huobi, and OkEx, Tether based exchanges, discovery power have shown a high correlation with the close price. This means that when the traders there become successful, price rises. When the traders there are failing, the price is falling. When Bitmex traders are getting a say, price is falling. I leave the interpretation of other past years to the viewer.




# Limitations
My analysis does not include market data for other derivative exchanges like Huobi, OkEx, Binance, and Deribit. So, all future market's influence may be going to Bitmex. I did not add their data because they started having an impact recently. A more fair assessment may be to conclude this as the new power of derivative markets instead of attributing it as the power of Bitmex. But Bitmex has dominated futures volume most of the time (until recently). And they brought the concept of perpetual swaps.

# Conclusion
There is a lot in this data. I think there is some edge here. Someday I will backtest some trading logic based on this data. Then I will have more info and might write more. But, this analysis was enough for to shift my focus from a Bitfinex based trading algorithm to a <a href="/Backtest-to-Live-Bitcoin-mex/">Bitmex based one</a>. It has been giving me good results.

<br/><br/><br/>



If you have any good ideas that you want me to write about or discuss further <a href="mailto:n00bdan13@gmail.com">contact me</a>. If you are interested in this analysis and want to discuss further leave a comment or <a href="mailto:n00bdan13@gmail.com">contact me</a>.
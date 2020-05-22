---
layout: post
title: Price Discovery in Bitcoin exchange
---

Twenty days ago, on Reddit, I promised to explain in detail a Bitcoin price discovery algorithm. And although a bit late, I now do so.  
<!--more-->

# TL;DR
I applied price discovery algorithms to 5 Min OHLCV data from Bitmex and CME contracts and Bitstamp, Coinbase, HitBTC, Kraken, Poloniex, Binance and OkEx BTCUSD/BTCUSDT markets from March 2016 to May 2020. Some interesting results I got was:

- Before the 2017/18 bull run, Bitfinex dominated the price discovery process. They started the run. But as the price increased, trades on other exchanges, Binance and Bitstamp playing a more dominant role in leading the price up. 

- Since then CME Contracts and Bitmex contracts have had a increasing role in price discovery. Today Bitmex and CME Contracts play the most substantial role in determining the direction of Bitcoin price. 


# Introduction
Price discovery is the overall process of setting the price of an asset. Price discovery algorithms identify the leader exchanges whose traders define the price. Several academics have already done various price discovery analysis on Bitcoin Markets. And although they used nobel approaches they ignore exchanges with huge real volumes like Bitfinex, OkEx, and Huobi.

There are different approaches to measure price discovery. The most popular ones are <a href="https://sci-hub.tw/https://www.jstor.org/stable/2329348?seq=1">Hasbrouck (1995)</a> and <a href="https://sci-hub.tw/https://www.jstor.org/stable/1392518?seq=1">Gonzalo and Granger (1995)</a>. <a href="https://sci-hub.tw/https://www.jstor.org/stable/2329348?seq=1">Hasbrouck (1995)</a> measures price discovery by calculating something called an efficient price. They define efficient price as the actual true price and measure discovery by measuring the variance of each exchange from the efficient price. <a href="https://sci-hub.tw/https://www.jstor.org/stable/1392518?seq=1">Gonzalo and Granger (1995)</a> does it a bit differently by decomposing the price series into components that represent the deviation from the efficient price. Both of this work have different level of noise and in an attempt to decrease the noise <a href="https://sci-hub.tw/https://www.sciencedirect.com/science/article/abs/pii/S0927539813000340">Putniņš (2013)</a> combined both of them to create a new measure. 

All of the algorithms mentioned above assume cointegration, random walk, and a common efficient price. And although cointegration is a fair assumption, I do not feel comfortable assuming random walk and common efficient price in Bitcoin Markets. This is a speculation based market where significant trades can cause change. So although the measures in previous paragraphs are better studied, I use a new and little know method by <a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3297869">De Blasis (2019)</a>. De Blasis assumes that "the fastest price to reflect new information releases a price signal to the other slower price series." This assumption seems fair in Bitcoin Markets. <a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3297869">De Blasis (2019)</a> tested their work in different futures and ETF contracts and found that the index's lead discovery in line with accepted literature adding credibility to their work.

<a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3297869">De Blasis (2019)</a> uses Markov Chains to measure Price Discovery. Markov Chain is a way of modeling random processes. Without going into the mathematical details their work calculates Price Discovery using the following steps:

- Data is first grouped into a daily interval. Then inside each daily interval's 5-minute candles, the change in prices between the current time t and previous time t-1 is calculated. The difference across the same time t across all exchange in a given day is juxtaposed to create an initial matrix.
- The initial matrix is used to create a Transition Matrix, which measures the probability of price changing to something else at time t+1 for its state at t.
- Then Markov Chain based algorithms are used to measure and influence the change at time t on an exchange had over all other exchanges' price movement at time t+1.
- Reduction and normalization is done to this data on each exchange to create a single number that sums to 1 for each exchange for a given day. 

<a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3297869">De Blasis (2019)</a> names this number Price Leadership Share (PLS). High PLS indicate a large role in price discovery. As the numbers sum to 1, they can be looked at as a percentage contribution. I recommend reading the <a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3297869">original paper</a> if you are interested to know more about the mathetical detail.


# Data
<a href="https://sci-hub.tw/https://amstat.tandfonline.com/doi/abs/10.1080/07350015.2000.10524857?journalCode=ubes20">Andersen (2000)</a> argues that 5 Minute window provides the best trade-off between getting enough data and avoiding noise. In one of the first work on Bitcoin's Price Discovery, <a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=2691456">Brandvold et al. 2015</a> had used 5M window. So I obtained 5M OHLCV data using the following sources:

- **Poloniex, Bitfinex, Binance and HitBTC:** Exchange's API through <a href="https://github.com/ccxt/ccxt">CCXT</a>.
- **Huobi:** <a href="https://huobiapi.github.io/docs/spot/v1/en/#websocket-market-data">Official Websocket Market API</a>
- **Coinbase:** Official API using <a href="https://pypi.org/project/cbpro/">cbpro</a>
- **Kraken, Bitstamp:** I created candles from the trade data obtained from <a href="http://api.bitcoincharts.com/v1/csv/">Bitcoincharts</a>
- **OkEx:** <a href="https://bitdataset.com/api/">Bitdataset API</a>
- **CME:** Okay, this was was supposed to be tricky and expensive. I broke a TOS and scraped the data for free, removing the expensive part from the equation. I will not go into detail about where I scraped this data.
- **Bitmex:** I got Bitmex data from the <a href="https://www.bitmex.com/app/restAPI">API</a>. 

Futures data are different from other data because multiple futures contract traders at the same time. I formed a single data from the multiple time series by selecting the nearest contract until it was three days from expiration. I used the next contract when the contract was three days from expiration. This approach was advocated by <a href="https://onlinelibrary.wiley.com/doi/abs/10.1002/%28SICI%291096-9934%28199909%2919%3A6%3C619%3A%3AAID-FUT1%3E3.0.CO%3B2-M">Booth et al ( 1999)</a> and and was also used by <a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3353583">Alexander et al (2019)</a> in the cryptocurrency market.

# Analysis

<p>
<iframe frameborder='0' scrolling='no' src='/static/price_discovery.html' class="embed-responsive-item" style="border:none; height: 605px; width: 1120px; margin-left:-200px; margin-bottom:-110px" ></iframe> 
</p>

<div style="font-size:12px; margin-left: -100px;">A full-page version of this graph is available <a href="/static/price_discovery.html">here</a></div>

<br/>
In the figure above, each colored line shows the total influence the exchange had towards the discovery of Bitcoin Price in that day. Its axis is on the left. The black line shows a moving average of the bitcoin price at the close in Bitfinex for comparison. The chart was created by plotting the EMA of price and dominance with an smoothing factor of 0.1. This was done to eliminate the noise. Let's start looking from the beginning. We start with a slight Bitfinex dominance at the start. But as the price start going up, Bitfinex's influence does too. This was the time large Tether printing was attributed to the rise of price by many individuals. With or without real money backing it, USDT defined the price movement at this time. But Bitfinex's influence wanes down as the price starts rising (remember that the chart is a exponential moving average. Its a lagging indicator). Afterwards exchanges like Binance and Bitstamp increase there role and there isn't any single leader in the run. So although Bitfinex may have been responsible for the initial pump trades on other exchanges were responsible for the later rally.

CME contracts was added to our analysis in February 2018. Initially, they don't have much influence. On a similar work <a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3383147">Alexandar and Heck (2019)</a> wrote that initially CBOE contracts had more influence. CBOE later delisted Bitcoin futures so I couldn't get that data. Going back to the analysis, OkEx has popped up at the top during some moves. However, overall Bitmex Perpetual contracts and CBOE contracts have been averaging around 50% of the role in price discovery. Although CME contracts have had quiet an influence, Bitmex contracts have dominanted more. To make the dominance clear, look at <a href="/static/bitmex_dom.html">this chart</a> where I add Bitmex Futures and Perp contract's dominance figure to create a single dominance index. There bitmex leads 936 of the total 1334 days (Bitfinex leads 298 days and coinbase and binance get 64 and 6 days). That is a lot. One possible reason for this might be Bitmex's low trading fee. Bitmex has a very generous -0.025% maker fee and price discovery tend to occur primarily in the market with smaller trading costs (<a href="https://sci-hub.tw/10.1002/(sici)1096-9934(199909)19:6%3C619::aid-fut1%3E3.0.co;2-m">Booth et al, 1999</a>; on exchanges with low trading fee <a href="https://sci-hub.tw/10.1002/fut.20302">Hsieh et al., 2008</a>). Or maybe we are becoming mature as futures lead the price discovery in other mature financial assets too. Both may be the case too.
 

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
    <th class="tg-fymr">Exchange</th>
    <th class="tg-fymr">bitfinex</th>
    <th class="tg-fymr">cme</th>
    <th class="tg-fymr">bitmex</th>
    <th class="tg-fymr">binance</th>
    <th class="tg-fymr">okex</th>
    <th class="tg-fymr">coinbase</th>
    <th class="tg-fymr">bitstamp</th>
    <th class="tg-fymr">poloniex</th>
    <th class="tg-fymr">kraken</th>
    <th class="tg-fymr">hitbtc</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-fymr">Days Lead<br></td>
    <td class="tg-0pky">624</td>
    <td class="tg-0pky">229</td>
    <td class="tg-0pky">128</td>
    <td class="tg-0pky">68</td>
    <td class="tg-0pky">67</td>
    <td class="tg-0pky">57</td>
    <td class="tg-0pky">37</td>
    <td class="tg-0pky">34</td>
    <td class="tg-0pky">29</td>
    <td class="tg-0pky">4</td>
  </tr>
</tbody>
</table>

Out of 1277 days in the analysis Bitfinex leads the discovery in 624 days or nearly 50% of the entire duration. Although it loses control later, its dominance can be citated to its extreme dominance in the early days. 

Then an overall correlation with the close price was measured. Bitfinex's influece was correlated with Price at a whopping 0.5. As expected, CME and Bitmex had a negative correlation.
<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-fn5d{border-color:inherit;color:rgb(0, 0, 0);text-align:left;vertical-align:middle}
.tg .tg-ae8w{border-color:inherit;color:rgb(0, 0, 0);font-weight:bold;text-align:left;vertical-align:middle}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-ae8w">maximum</th>
    <th class="tg-ae8w">binance</th>
    <th class="tg-ae8w">bitfinex</th>
    <th class="tg-ae8w">bitmex</th>
    <th class="tg-ae8w">bitstamp</th>
    <th class="tg-ae8w">cme</th>
    <th class="tg-ae8w">coinbase</th>
    <th class="tg-ae8w">hitbtc</th>
    <th class="tg-ae8w">kraken</th>
    <th class="tg-ae8w">okex</th>
    <th class="tg-ae8w">poloniex</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-ae8w">change</td>
    <td class="tg-fn5d">-0.266103</td>
    <td class="tg-fn5d">0.518077</td>
    <td class="tg-fn5d">-0.110729</td>
    <td class="tg-fn5d">0.290081</td>
    <td class="tg-fn5d">-0.056347</td>
    <td class="tg-fn5d">0.363762</td>
    <td class="tg-fn5d">-0.388755</td>
    <td class="tg-fn5d">-0.012326</td>
    <td class="tg-fn5d">-0.291696</td>
    <td class="tg-fn5d">0.112925</td>
  </tr>
</tbody>
</table>

But it dosen't always acts the same. A yearwise correlation would be more useful.

<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-fn5d{border-color:inherit;color:rgb(0, 0, 0);text-align:left;vertical-align:middle}
.tg .tg-ae8w{border-color:inherit;color:rgb(0, 0, 0);font-weight:bold;text-align:left;vertical-align:middle}
.tg .tg-exjq{color:rgb(0, 0, 0);text-align:left;vertical-align:middle}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-ae8w"><br>Exchage</th>
    <th class="tg-ae8w">2016</th>
    <th class="tg-ae8w">2017</th>
    <th class="tg-ae8w">2018</th>
    <th class="tg-ae8w">2019</th>
    <th class="tg-ae8w">2020</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-fn5d">bitfinex</td>
    <td class="tg-fn5d">-0.029098</td>
    <td class="tg-fn5d">-0.613785</td>
    <td class="tg-fn5d">0.612723</td>
    <td class="tg-fn5d">-0.236784</td>
    <td class="tg-fn5d">0.454181</td>
  </tr>
  <tr>
    <td class="tg-fn5d">bitmex</td>
    <td class="tg-fn5d">-0.190769</td>
    <td class="tg-fn5d">-0.496867</td>
    <td class="tg-fn5d">-0.438801</td>
    <td class="tg-fn5d">0.610174</td>
    <td class="tg-fn5d">-0.562105</td>
  </tr>
  <tr>
    <td class="tg-fn5d">bitstamp</td>
    <td class="tg-fn5d">0.261178</td>
    <td class="tg-fn5d">-0.433370</td>
    <td class="tg-fn5d">0.545216</td>
    <td class="tg-fn5d">-0.271758</td>
    <td class="tg-fn5d">-0.197735</td>
  </tr>
  <tr>
    <td class="tg-fn5d">coinbase</td>
    <td class="tg-fn5d">-0.296110</td>
    <td class="tg-fn5d">-0.177246</td>
    <td class="tg-fn5d">-0.171468</td>
    <td class="tg-fn5d">-0.336559</td>
    <td class="tg-fn5d">-0.476033</td>
  </tr>
  <tr>
    <td class="tg-fn5d">hitbtc</td>
    <td class="tg-fn5d">0.058207</td>
    <td class="tg-fn5d">0.273143</td>
    <td class="tg-fn5d">0.040815</td>
    <td class="tg-fn5d">-0.026220</td>
    <td class="tg-fn5d">0.238886</td>
  </tr>
  <tr>
    <td class="tg-fn5d">kraken</td>
    <td class="tg-fn5d">0.198303</td>
    <td class="tg-fn5d">0.503060</td>
    <td class="tg-fn5d">0.494483</td>
    <td class="tg-fn5d">0.228132</td>
    <td class="tg-fn5d">-0.089156</td>
  </tr>
  <tr>
    <td class="tg-fn5d">poloniex</td>
    <td class="tg-fn5d">0.162664</td>
    <td class="tg-fn5d">-0.131068</td>
    <td class="tg-fn5d">0.042419</td>
    <td class="tg-fn5d">-0.127213</td>
    <td class="tg-fn5d">0.228508</td>
  </tr>
  <tr>
    <td class="tg-fn5d">binance</td>
    <td class="tg-fn5d">NaN</td>
    <td class="tg-fn5d">0.824916</td>
    <td class="tg-fn5d">0.605818</td>
    <td class="tg-fn5d">-0.446734</td>
    <td class="tg-fn5d">0.101668</td>
  </tr>
  <tr>
    <td class="tg-fn5d">huobi</td>
    <td class="tg-fn5d">NaN</td>
    <td class="tg-fn5d">0.826675</td>
    <td class="tg-fn5d">0.282910</td>
    <td class="tg-fn5d">-0.211446</td>
    <td class="tg-fn5d">0.716309</td>
  </tr>
  <tr>
    <td class="tg-fn5d">cme</td>
    <td class="tg-fn5d">NaN</td>
    <td class="tg-fn5d">NaN</td>
    <td class="tg-fn5d">-0.539686</td>
    <td class="tg-fn5d">0.691447</td>
    <td class="tg-fn5d">-0.082495</td>
  </tr>
  <tr>
    <td class="tg-exjq">okex</td>
    <td class="tg-exjq">NaN</td>
    <td class="tg-exjq">NaN</td>
    <td class="tg-exjq">-0.599661</td>
    <td class="tg-exjq">-0.474166</td>
    <td class="tg-exjq">0.324347</td>
  </tr>
  <tr>
    <td class="tg-exjq">bitmex_futures</td>
    <td class="tg-exjq">NaN</td>
    <td class="tg-exjq">NaN</td>
    <td class="tg-exjq">NaN</td>
    <td class="tg-exjq">NaN</td>
    <td class="tg-exjq">NaN</td>
  </tr>
</tbody>
</table>

This data is more explanatory than the previous one. This year Bitfinex, Huobi and OkEx, Tether based exchanges have high correlation with price increases. Bitmex has with decreases. This goes in with the theories I have been reading on reddit about how Tether based exchanges are trying to push the price up and Bitmex is pushing the price down. But concluding this would be cherry picking ignoring other years. Going back to the chart, CME dosen't seem to be influencing price in any direction. It has had high correlation with whatever direction the price went in. <a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3353583">Alexander et al, 2019</a> had that Bitmex was responsible for price falls more often than an increase. But in our data if we look at the <a href="/static/bitmex_dom.html">this chart</a>, Bitmex seems to be leading movement in every direction. 

# Conclusion and Limitations
My analysis does not include market data for other derivative exchanges like Huobi, OkEx, Binance and Deribit. So, all future market's influence may be going to Bitmex. I did not added their data because they started having an influence recently. A more fair assesment of this can be interpreting the futures market as powerful instead of Bitmex itself. But Bitmex's contribution cannot be underestimented for it dominated volume for most of time (until recently) and even brough the concept of perpetual swaps.

This conclusion was enough for to shift my focus from a <a hre="/Backtest-to-Live-Ethereum-finex/">Bitfinex based trading algorithm</a> to a <a href="/Backtest-to-Live-Bitcoin-mex/">Bitmex based one</a>. Since it has been giving me good results adding my confidence in this analysis. I plan to move to other liquidity exchanges too once I need more liquidity. I that time, I can do an analysis including more exchanges. 





If you have any good ideas that you want me to write about discuss further <a href="mailto:daniel@waterbot.xyz">contact me</a>. If you are interested in this analysis <a href="mailto:daniel@waterbot.xyz">contact me</a> and I can send the python notebooks used in this analysis. If there is enough interest in this measurement I can setup a live interface that provides the live value.
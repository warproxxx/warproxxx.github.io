---
layout: post
title:  Slippage and Fee analysis in BitMex
---

I have completed 541 trades in BitMex since April 23 in the XBTUSD perp swap through the BitMex API. I send my order as the best limit order. If better orders come, I cancel and send it as the best order again. If I can't enter through limit orders in 1-2 minutes this way, I enter using Market Orders. The slippage and the average fee I got in my trades this way can be used by other algo traders for a rough estimate when they are backtesting their strategy. <!--more-->

# TL;DR:
Overall I got a positive slippage of 0.0115%. But this was because of 1 anomalous trade in which I got lucky. Considering trades whose slippage was within two standard deviations of the mean, I got a slippage of -0.035%. I paid a fee of 0.0115% per trade on average. Overall I overpaid 0.0465% in fees and slippage (multiplied the margin percentage) compared to the candle open.


### Disclamers:
- This data is from a volatile time in the Bitcoin Market. The 20 day Bollinger Band width averaged to 41. In 2019, it was 32.
- I use a mild trend following algorithm. Here mild means that the overall trend is going toward the direction but in the last few minutes when I am buying or selling, there hasn't been a big change. A trend reversal algorithm might get a better fee.
- The size of my trades was fairly small. If you are trading 6 digits or more, you might get more slippage.


# Analysis

I got all the trades I did from April 23 through the Bitmex API. Most of the times, I had used multiple trades to enter into a position. So I merged the trades to create, totalAmount, averagePrice and averageFee for each entry and exit. My algorithm had 1 minute or two minutes to trade. I compared the average price I got with the price I started entering in and the time I had entered. So this means if I submit my first limit order in 5:59, I compared the price I got with the candle at 5:59 and 6:00. If I hadn't been filled by 6:00 I used to enter in market order. There wasn't any considerable difference in slippage in either. So I use the 1 minute after price in this analysis.

On first look, I got a 0.012 positive slip. This was too good to be true. So I plotted all the slips I got.

<figure>
    <img src="/images/buy_orders_slippage.png">
    <figcaption>Fig 1: Slippage Percentage in each buy orders</figcaption>
</figure>

<figure>
    <img src="/images/sell_orders_slippage.png">
    <figcaption>Fig 2: Slippage Percentage in each sell orders</figcaption>
</figure>

As you can see, there were one or two trades where I got very very lucky in. It is unlikely to happen again. Other details of the trades are:

<center>
<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-1wig{font-weight:bold;text-align:left;vertical-align:top}
.tg .tg-amwm{font-weight:bold;text-align:center;vertical-align:top}
.tg .tg-0lax{text-align:left;vertical-align:top}
</style>
<table class="tg" style="float: left; margin-left:100px">
<thead>
  <tr>
    <th class="tg-amwm" colspan="2">BUY ORDERS</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-1wig">Slippage Mean</td>
    <td class="tg-0lax">-0.023</td>
  </tr>
  <tr>
    <td class="tg-1wig">Slippage Std</td>
    <td class="tg-0lax">0.166</td>
  </tr>
  <tr>
    <td class="tg-1wig">M + 2SD</td>
    <td class="tg-0lax">0.309</td>
  </tr>
  <tr>
    <td class="tg-1wig">M - 2SD</td>
    <td class="tg-0lax">-0.356</td>
  </tr>
</tbody>
</table>


<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-1wig{font-weight:bold;text-align:left;vertical-align:top}
.tg .tg-amwm{font-weight:bold;text-align:center;vertical-align:top}
.tg .tg-0lax{text-align:left;vertical-align:top}
</style>
<table class="tg" style="display: inline-block;">
<thead>
  <tr>
    <th class="tg-amwm" colspan="2">SELL ORDERS</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-1wig">Slippage Mean</td>
    <td class="tg-0lax">0.519</td>
  </tr>
  <tr>
    <td class="tg-1wig">Slippage Std</td>
    <td class="tg-0lax">0.774</td>
  </tr>
  <tr>
    <td class="tg-1wig">M + 2SD</td>
    <td class="tg-0lax">1.6</td>
  </tr>
  <tr>
    <td class="tg-1wig">M - 2SD</td>
    <td class="tg-0lax">-1.49</td>
  </tr>
</tbody>
</table>
</center>

So I removed data that were 2SD above and below the mean. This removed 2 trades in buy and sell each. Afterwards, I compared the slips. I got a slippage of -0.033% on buy orders on average and -0.0389% on sell orders. Overall that made a slippage of -0.0358

## Integrating this slippage in Backtests through backtrader
I used backtrader to perform my backtests. Finding the correct way to integrate them took a bit of time. If you find this page through google, this is the quick way to do it:

<pre><code>class CommInfoFractional(bt.CommissionInfo):
    
    def getsize(self, price, cash):
        '''Returns fractional size for cash operation @price'''
        return self.p.leverage * (cash / price)</code></pre>

<br/>

<code>cerebro.broker = bt.brokers.BackBroker(cash=initial_cash, slip_perc=0.04/100, commission = CommInfoFractional(commission=(0.012*mult)/100, mult=mult), slip_open=True, slip_out=True)</code>

Do this just before invoking cerebro.run(). Here mult is my leverage. CommInfoFractional is only required while trading cryptocurrencies because you can buy a fractional amount. I used 0.04% in backtest to stay in the safe side. 

## Going Forward
I had initially expected much better results. My options were to increase my edge by focusing on the algorithm and decreasing the number of trades or work on a better execution strategy. To get a better slip, I will have to fight against makers. They are probably some wall street firms equipped with colocated servers and a team of quants. They say fight with people your size. It will be tough to reduce the slip by even a bps competing against them. I decided battling them is not worth the results. So I worked on the algorithm and made some changes to enter very conservatively. With these changes, backtests is giving me similar results to what I had previously expected. Lets see how live trading will go.
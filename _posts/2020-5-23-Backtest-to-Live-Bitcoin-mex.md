---
layout: post
title:  Experience creating a XBTUSD Trading Algorithm on Bitmex
---

Two months ago, I decided to create an XBTUSD perpetual contract trading algorithm on Bitmex, a cryptocurrency derivatives market. <!--more-->The reasons for choosing Bitmex were:

EDIT (2020 December): A lot of changed in this time. My system is completly different than what i wrote here. And when i wrote this bitmex dominated the derivative market volume. So bitmex meant derivative market.

- I didn't have a lot of capital. Bitmex provided plenty of leverage.
- <a href="https://public.bitmex.com/">Bitmex provides access to all historical trades and orderbook data</a>.
- Bitmex has high liquidity.
- <a href="/Price-Discovery/">Bitmex traders are well informed and are market leaders in the Bitcoin Market. </a> That meant a higher possibility to find an edge in the data.

As I continued developing, two critical features of Bitmex ensured that this was the right choice.

- Trading fees kill most algorithms. Bitmex has a very generous -0.025% maker fees. By mostly entering as a maker, I had planned to pay a net-zero fee. I was able to achieve that.(EDIT: This is no longer the case either)
- Bitmex has fantastic support. They reply instantly. And they understand what you mean immediately. The second point does not seem much, but it is. In some other exchanges, I had to explain what I said 2 or 3 times before they finally got it. In Bitmex, I have asked somewhat complicated questions about the trading mechanisms and received immediate answers. 

I had learned from my previous experiences to ditch conventions and "best practices" for efficiency. This mindset caused me to make the following changes:

- I stored trade data and orderbook data in csv files instead of some advanced engines. This made the development much faster. Later I switched orderbook data to Redis after I had some issues.
- I had used Machine Learning in my previous algorithm. I had a hypothesis and knew what to find. Still, I chose to let an algorithm find it. It acted black box at times, didn't actually do what I desired, was too slow to adapt, and retraining was a pain. I did not use ML this time. If you have a hypothesis, you don't need ML or something like that to find that hypothesis for you again. There are other simpler mathematical algorithms. If you don't have a hypothesis, good luck having a Machine figure it out for you.
- I did not develop any interfaces. I SSH'd to find bugs. This saved a lot of time and added a mental benefit as refreshing the interface every few hours was taking away the mental benefits of using an algorithm.
- In the previous algorithm, I got my main idea from an academic paper. This time I used an original idea. The market gave me all the feedback I needed.

Generally, I recommend any individual algorithm maker to code using whatever you know. Learn something only if there comes a need. And make changes only when things break.

All the mentioned decisions turned out to be an excellent choice. I was able to complete the first live version of the algorithm in less than two weeks. Last time, it had taken me months. Over the complete development, I used very few advanced mathematical techniques. My algorithm trades XBTUSD perpetual swaps in a single direction. I chose to stay with perpetual contracts in spite the fundings because of the high liquidity. Perpetual contracts are futures contracts that do not expire. To ensure that price doesn't deviate from the index, Bitmex has a mechanism called funding where depending on the deviation from the index, longs pay short or shorts pay longs a comission every 8 hours. Historically longs pay shorts an average of around 10% in a year (multiplied the leverage). Despite paying this fee at times, my algorithm has a sharpe ratio of over 2 in backtests and forward tests. 

Some suggestions about algorithm development that would apply to any markets for individual traders:

- One algorithm cannot profit from every movement. Focus on a few moves.
- Your algorithm does not need to be perfect. It can make mistakes often. My algorithm has a win-lose ratio of 1:4. It loses much more than it wins. But when it wins, it wins big. (EDIT: Although this logic is fine, the new iteration of my algo has more wins than losses.)
- Risk Management is the most critical part of a good algorithm.
- Once you go live, do not change the core logic. Looking at a 30% drawdown live feels different than a 50% drawdown in backtest.
- Don't be afraid to use leverage. My algorithm uses high leverage. Leverage is an asset, and just like any other and how you use it matters. Unless you hold at least 6 figure USD, leverage with a consistent risk management strategy might give you better returns. If you are using leverage, it is very important that you test using leverage, including the fees during your backtest.
- There will be slippage. In backtest assume high slippage. As I entered through limits, I assumed that I would get the one-minute later price and some additional percentage slippage. This turned out to be a fair estimate. In the last one month, I have been getting a slightly better price on average.

After forward testing and verifying that there wasn't any bias, it was time to go live. Going from test to live was harder than I expected. Issues I ran into:

- Bitmex wss API shuts down during times of high load. And these are often times where there is extreme volatility, and you had expected to trade. I had to make many changes in the algorithm to handle this.
- Bitmex wss API sends the candle data 20-30 seconds late. That is unacceptable for any good algorithm. Use trade data to calculate OHLCV data yourself if you should
- Sometimes the exchange will go down. Have some logic ready to handle it.
- Look at the top issues on <a href="https://github.com/BitMEX/api-connectors/issues?q=is%3Aissue+is%3Aopen+sort%3Acomments-desc">github</a> to know what issues you might run into. If you use python, familiarize yourself with this <a href="https://github.com/BitMEX/api-connectors/issues/139">bug</a>


# Final Words

Overall, deciding to create the algorithm the way I chose turned out to be the right choice. It has been trading as expected. Some final recommendation from my experience:

- No matter how good your algorithm looks on backtest, start small. You will run into issues when you go live. 

- If you can, don't use stop losses while trading cryptocurrencies. Use some logic as an alternative to stop losses.

- I started with a desire to trade other smaller coins (cryptocurrencies alternative of penny stock), assuming they would be easier. But it turned out there is still a lot of edge in the BTC market. And the percentage returns can be multiplied with leverage. Small coins aren't worth trading if you don't already have a hypothesis with strong certainty.

- Don't be afraid to try your original ideas. Initially, I thought because academics have already done a lot of research, I can get an edge by combining their idea in a different market. And although I did get some edge by following their approach, I got a better edge through a hypothesis I did not read in any paper. The market is your report card. Backtest and other form of tests will show all that you need.

- Algorithm Trading is not "make an algo once, live all your life without working." Market changes, your total capital changes; allocation strategies will need to change; hyper parameters need to be changed. You will have to make changes continuously. But if your goal is fewer working hours, you can reach there once something stable is made.

- It would be great if your backtest framework supports live trading. Mine did not, and it caused some issues initially.
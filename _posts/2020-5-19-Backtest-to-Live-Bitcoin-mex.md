---
layout: post
title: From Backtest to Live - An experience creating a XBTUSD Trading Algorithm on Bitmex
---

Two months ago, I decided to create an XBTUSD perpetual contract trading algorithm on Bitmex. <!--more-->The reasons for choosing Bitmex were:

- I didn't have a lot of capital. Bitmex provided plenty of leverage.
- Bitmex provides access to all historical trades and orderbook data. Most other exchanges did not provide historical access to this data, and I had not done a live collection.
- Bitmex has high liquidity
- <a href="/Price-Discovery/">Bitmex traders define the market price today.</a>

As I continued developing, two critical features of Bitmex ensured that this was the right choice.

- Trading fees kill most algorithms. Moreover, if you are using margin, fees need to be paid for the entire amount. Bitmex has a -0.025% maker fees.  By mostly entering as a maker, I had hypothesized that I would spend a zero fee over the long term. It was proved correct.
- Bitmex has fantastic support. They reply instantly. And they understand what you mean immediately. The second point does not seem much, but it is. In some other exchanges, I had to explain what I said 2 or 3 times before they finally got it. In Bitmex, I have asked somewhat complicated questions about the trading mechanisms and received immediate answers. 

<a href="/Backtest-to-Live-Ethereum-finex/">When I developed my last algorithm, a Bitfinex based ETH trading algorithm called waterbot</a>, I had learned to ditch conventions and best practices for efficiency. Use the easiest way to do something and switch to more advanced technology only when required. This time I was keeping the efficiency part but was determined to other things differently. On waterbot, I had created a fancy web interface detailing everything that is happening. It took a lot of time, and it did not turn out to be a wise decision. I started refreshing the page regularly. Waterbot traded on longer timeframes. It would hold positions for at an average of 2.3 days. Refreshing the page every few hours was not good for the mind. One of the biggest reasons I went to algorithmic trading was mental benefits.  And, refreshing every few hours was taking it away. So I would not make an interface and trade on shorter timeframes. 

In waterbot I got my main idea from an academic paper. I  learned many concepts that I thought were needed but weren't. The success I was having in some other parts of my life had increased my confidence. So, I would not follow any academic's fancy models and drop fancy database engine and Machine Learning unless there was specific demand (Later, I made some changes to use storage engines like Redis and Arctic, but I only did it when it was really needed). Not following a peer-reviewed idea and engines gave me freedom. I was going to use a hypothesis that I created, which I hadn't seen discussed anywhere else except in a Reddit comment years ago. The only feedback I would need would be provided by backtests. I did not need anything more. I downloaded the data, stored it as required, and start developing a backtest strategy starting with some conditions and basic statistics immediately. All this turned out to be an excellent decision. Over the complete development only used very few advanced mathematical techniques. In 2 weeks, I was able to create an algorithm with a very good sharpe ratio (over 2) in backtests. 

Some suggestions about algorithm development that would apply to any markets for individual traders:

- One algorithm cannot profit from every movement. Focus on a few movements.
- Your algorithm does not need to be perfect. It can make mistakes often. My algorithm has a win-lose ratio of 1:4. It loses much more than it wins. But when it wins, it wins big.
- Risk Management is the most critical part of a good algorithm.
- Once you get going, do not change the core logic. Looking at a 50% downfall in live feels different than a 50% downfall in backtest.
- Don't be afraid to use leverage. My algorithm uses high leverage. It is an asset and just like any other asset how you use it. Unless you hold at least 6 figure USD, leverage with consistent risk management strategy might give you better returns. Test it by including the fees, leverage and liquidity in your backtest.
- If you have a hypothesis, you don't need ML or something like that to find that hypothesis. If you don't have a hypothesis, good luck having a Machine figure it out for you. Although ML has its place, if you are working for a firm, it doesn't have a place in most individual algos. It is prone to overfitting and is slow.

Let's go back to the exchange. My algorithm trades only perpetual contracts and only trades in one direction. In perpetual contracts, longs have to play shorts most of the time every 8 hours. If you don't know Bitmex, it is because Perpetual contracts are futures contracts that do not expire. To ensure that price doesn't deviate from the index, Bitmex has a mechanism called funding where depending on the deviation from the index, longs pay short or shorts pay longs a comission every 8 hours. Historically longs pay shorts an average of around 30% in a year. If I did not want to pay the funding, I could trade futures. But I chose to stay with perp's because:

- Funding took place four times a day. I didn't often need to pay this.
- The futures market is not as liquid as the perp market. I need to enter a position in a set timeframe. If I enter a minute late, I lose my edge. As I was getting limits order filled more often, I would stay 0 at fees even with funding.

After forward testing and verifying that there wasn't any bias, it was time to go live. Going from test to live was harder than I expected. Issues I ran into:

- Bitmex wss API shuts down during times of high load. And these are often times where there is extreme volatility, and you had expected to trade. I had to make changes in the algorithm to roll out to this.
- Bitmex wss API sends the candle data 20-30 seconds late. That is unacceptable for any good algorithm. Use trade data to calculate OHLCV data yourself if you should
- If you use python, familiarize yourself with this <a href="https://github.com/BitMEX/api-connectors/issues/139">bug</a>
- Unless you use "post-only" orders, limit orders might enter as market orders. Through the API, this execInst is ParticipateDoNotInitiate
- Look at the top issues on <a href="https://github.com/BitMEX/api-connectors/issues?q=is%3Aissue+is%3Aopen+sort%3Acomments-desc">github</a> to know what issues you might run into
- Some day the exchange will go down. Have some logic ready to handle it.

# Conclusion and Recommendation

Overall, deciding to create the algorithm the way I chose turned out to be the right choice, and it's doing as expected in live. Here is some further recommendation from my experience:

- No matter how good your algorithm looks on backtest, start small. You will run into issues when you go live. 

- If you can, don't use stop losses while trading cryptocurrencies. Use some logic as an alternative to stop losses. You would be surprised at how much stop triggering moves takes place in this space.

- I started with a desire to trade other smaller coins (cryptocurrencies alternative of penny stock), assuming they would be easier. But it turned out there is still a lot of alpha in the BTC market itself. These small coins aren't worth trading if you don't already have a hypothesis (which is probably going to be a way of detecting their P&D).

- This one may be a bit cliche but, believe in yourself. Initially, I thought because academics have already done a lot of research, I can get an edge by following their idea in a different market in other markets. And although I did get some edge by following their approach, I got a better edge by testing my hypothesis. The market is your report card. Backtests will immediately show where you are right and wrong. In algorithm trading, originality is rewarded (if it works).

- Algorithm Trading is not "make an algo once, live all your life without working." Market changes, your total capital changes, allocation strategies will need to change; the trader will need modifications. You will have to make changes continuously, although the amount of time you need to work might decrease once something stable is made. But, if you are like me, the time will increase because you will love what you are discovering. 
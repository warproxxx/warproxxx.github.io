---
layout: post
title: From Backtest to Live - An experience creating a XBTUSD Trading Algorithm on Bitmex
---

Two months ago I decided to create a XBTUSD perpetual contract trading algorithm on Bitmex. <!--more-->The reasons for choosing Bitmex were:

- I didn't have a lot of capital. Bitmex provided plenty of leverage.
- Bitmex provides access to all historic trades and orderbook data. There is so little you can do with just OHLCV data. I had not collected historic trades and orderbook data for other exchanges. And no exchange provided this data.
- Bitmex traders define the market price today. They know more.

As I continued developing, two key features of Bitmex ensured that this was the right choice.

- Bitmex has a -0.025% maker fees. This is very attractive for any algo trader because in a short term trading algorithm fees kill you. If you are using margin, fees get multiplied by the margin account. By mostly entering as a maker, I had hypothesized that I would pay a zero fee over long term. It was proved correct.
- Bitmex has an amazing support. They reply instantly. And they understand what you mean immediatly. The second point do not seem much but it is. In some other exchanges, I had to explain what I meant 2 or 3 times before they finally got it. In Bitmex, I have asked fairly complicated questions about the trading mechanisms and received answers immediatly and correctly. And I am not receiving this because I trade a lot of money. Till now I trade fairly small amount.

When I developed my last algorithm, a ETH trading algorithm in finex, I got my idea from an academic paper. I followed standards after standards recommended on academic papers. The end result was good but it took me over 10 months of learning fancy concepts and R&D to start trading stably. <a href="">You can look at the detail about this trading algorithm here. </a> While working on my previous alorithm, which I called waterbot, I created a fancy web interface detailing everything that is happening. It did not turn out to be a very wise decision as I started refreshing the page constantly. Waterbot traded on longer timeframes. It would hold positions for at an average of 2.3 days. This is not good for the mind. One of the biggest reason I went to algorithmic trading was the mental side. Refreshing how the algo was doing did not do me good.

This time I was determined to do things differently. I would not create any fancy interface. I would trade on shorter timeframes. I would finish the work much much faster. I would not follow any academic's fancy models. This meant dropping Machine Learning. And I would not follow any fucking standards. I learned this at the end of coding waterbot. Only when I coded however the fuck I wanted without following standards I got the result. Later I made some changes and used those fancy storage engines like Redis but I only did it when it was needed of me. This time I was taking I will do things however the fuck I want to a new level. I was using a hypothesis that I created and I hadn't seen discussed in fancy academic papers (althought I had seen it discussed in reddit). The only feedback I would need would be provided by backtests. I did not needed anything more. I downloaded the data, stored it as needed and starting developing a backtest stategy with some conditions and statistics immediatly.

All this turned out to be a very good decision. My hypothesis was correct. In 2 weeks I was able to create an algorithm with a very good sharpe ratio (over 2) in backtests. My algorithm traded only perpetual contracts and went long more often than short. In perpetual contracts, longs have to play shorts most of time every 8 hours. If you don't know Bitmex it is because Perpetual contracts are futures contracts that do not expire. To ensure that price dosen't deviate from the index, Bitmex has a mechanism called funding where depending on the deviation from the index longs pay short or shorts pay longs a comission every 8 hour. Historically longs pay shorts an average of around 30% in a year. If i did not wanted to pay the funding, I could trade futures. But I chose to trade Perp's because:

- Funding took place 4 times a day. I didn't often needed to pay this
- The futures market are not as liquid as the perp market. I need to enter a position in set timeframe. If i enter a minute late, I lose my edge. As I was getting limits order filled more often, I would stay 0 at fees even with funding.


# Going Live

After forward testing and verifying that there wasn't any bias, it was time to go live. Going from test to live was harder than I expected. Bitmex wss API shuts down during times of high load. And these are often times where I needed to trade. So I had to make changes in the algorithm to roll out to this. Other issues I encountered were:

- Bitmex is really illiquid. It has no uses except testing the endpoint.
- My algorithm had to be entered on limit. Although showing limit, it was being executed on market. Although I had thought of ideas, support helped me.



# Conclusion and Recommendation

Overall, deciding to create the algorithm the way I chose turned out to be a right choice. I haven't made much but I am certain that I will. Here are some recommendation from my expirience:

- No matter how good your algorithm looks on backtest, go small. You will run into issues when you go live. Don't put all of your capital on beginning. (But I did it and there is no way I was going to put effort to such quick effort to make changes. So whatever works for you tbh.)

- If you have a hypothesis, you don't need ML or something like that to find that hypothesis for you. Don't use it. If you don't have an hypothesis good luck letting a Machine find it. The best models still have issues determining if a picture is a cat. Although ML has its place if you are working for a firm, it really dosen't have a place in most individual algos. It is prone to overfitting and is slow.

- Risk Management is the most important part of an algorithm.

- If you can, don't use stop losses. Use some logic as an alternative for stop losses. You would suprised at how much stop triggering short moves takes place in this space.
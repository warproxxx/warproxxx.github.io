---
layout: post
title: Trend based trading in Bitcoin
---

Bitcoin is a trend based market. It moves similarly for a certain amount of time. Then it doesn't. I establish my own simple and replicable method for dividing trends in this post. Then I do a couple of backtests to add more credence to my initial claim. Here, trend means the way it moves (how it responds to certain buying/selling), its overall direction, its volatility, its reversals and such.


<!--more-->

## Trend Calculation
 Four-year cycle, [smart money chart](https://news.bitcoin.com/wp-content/uploads/2017/12/bitcoin-a-new-paradigm-696x467.jpg) are some ways people divide the market to see trends. I apply standard deviation on the daily close data of bitcoin to obtain its volatility. Then I would had applied 30-day moving average, following it with a gaussian filter with a sigma of 3 to smoothen it out. But this will cause a look-ahead bias. To prevent this, I apply gaussian filter to the 30 day moving average without giving access to the next day's data and obtain a completely unbiased trend chart:

<p>
<iframe frameborder='0' scrolling='no' src='https://algos.parsvirilis.com/static/plot_unbiased.html' class="embed-responsive-item" style="border:none; height: 605px; width: 1120px;" ></iframe> 
</p>
<font size="1">The chart is available in <a href="https://algos.parsvirilis.com/static/plot_unbiased.html" target="_blank">this url</a> and it will be updated live every day at UTC 00:03 with the previous day's close data. </font>

The trend line is at the bottom. Every time the trend line changes direction, a vertical line is drawn in the price chart. Through this approach, anything that lies between 2 lines is part of that trend. If the trend is smaller than 3 days, I discard it. The obtained trend groups are:

|    start   	|     end    	|           name          	|
|:----------:	|:----------:	|:-----------------------:	|
| 02-28-2020 	| 03-23-2020 	| corona_dump             	|
| 03-23-2020 	| 04-24-2020 	| corona_recovery         	|
| 04-29-2020 	| 05-15-2020 	| further_recovery        	|
| 05-15-2020 	| 06-04-2020 	| 9k_accumulation         	|
| 06-06-2020 	| 06-28-2020 	| further_9k_accumulation 	|
| 07-01-2020 	| 07-24-2020 	| final_9k_accumulation   	|
| 07-24-2020 	| 08-14-2020 	| initial_takeoff         	|
| 08-14-2020 	| 09-03-2020 	| 12k_dump                	|
| 09-03-2020 	| 09-16-2020 	| 10k_accumulation        	|
| 09-16-2020 	| 10-11-2020 	| 10k_accumulation_2      	|
| 10-11-2020 	| 11-27-2020 	| lift_off                	|

I gave each column names manually for clarity. Bitcoin moves similarly between these points. Price reversals takes place in a similar manner. Volatility and the direction mostly stays the same.

## Simple Test
A new trend is created when something different happens. So, if the group was created due to the price going up, we go long. If it was created due to price falling, we go short. To do this we calculate the percentage change in price from 4 days to the start of a trend to the start. If it is positive, we go long. If it is negative, we go short.

In the table below, **before** is the date 4 days before the start of a trend and **before_price** the price at that time. **start**, **start_price**, **end** and **end_price** are similar. **bef_star_change** is the % change in price from **before_price** to **start_price** and **hodl_return** is % change between **start_price** and **end_price**. **trade_direction** is the direction to go depending on our strategy. It is LONG if **bef_star_change** is positive and SHORT if **bef_star_change** is negative. Finally **strategy_return** is the return following **trade_direction** would give. It is the same as **hodl_return** if **trade_direction** is LONG. It will be -1 * hodl_return if **trade_direction** is SHORT

| name                    	| 4_days_before_start 	| 4_days_before_start_price 	| start      	| start_price 	| creation_change 	| end        	| end_price 	| hodl_return 	| trade_direction 	| strategy_return 	|
|-------------------------	|---------------------	|---------------------------	|------------	|-------------	|-----------------	|------------	|-----------	|-------------	|-----------------	|-----------------	|
| corona_dump             	| 2020-02-24          	| 9970.09                   	| 2020-02-28 	| 8801.27     	| -11.72          	| 2020-03-23 	| 5868      	| -33.33      	| SHORT           	| 33.33           	|
| corona_recovery         	| 2020-03-19          	| 5449                      	| 2020-03-23 	| 5868        	| 7.69            	| 2020-04-24 	| 7506.94   	| 27.93       	| LONG            	| 27.93           	|
| further_recovery        	| 2020-04-25          	| 7491.2                    	| 2020-04-29 	| 7762.77     	| 3.63            	| 2020-05-15 	| 9773.26   	| 25.9        	| LONG            	| 25.9            	|
| 9k_accumulation         	| 2020-05-11          	| 8756.57                   	| 2020-05-15 	| 9773.26     	| 11.61           	| 2020-06-04 	| 9645.99   	| -1.3        	| LONG            	| -1.3            	|
| further_9k_accumulation 	| 2020-06-02          	| 10250.1                   	| 2020-06-06 	| 9573.96     	| -6.6            	| 2020-06-28 	| 8992.81   	| -6.07       	| SHORT           	| 6.07            	|
| final_9k_accumulation   	| 2020-06-27          	| 9131.59                   	| 2020-07-01 	| 9119.09     	| -0.14           	| 2020-07-24 	| 9608      	| 5.36        	| SHORT           	| -5.36           	|
| initial_takeoff         	| 2020-07-20          	| 9222.54                   	| 2020-07-24 	| 9608        	| 4.18            	| 2020-08-14 	| 11837.12  	| 23.2        	| LONG            	| 23.2            	|
| 12k_dump                	| 2020-08-10          	| 11720.58                  	| 2020-08-14 	| 11837.12    	| 0.99            	| 2020-09-03 	| 11394.71  	| -3.74       	| LONG            	| -3.74           	|
| 10k_accumulation        	| 2020-08-30          	| 11488.97                  	| 2020-09-03 	| 11394.71    	| -0.82           	| 2020-09-16 	| 10745.76  	| -5.7        	| SHORT           	| 5.7             	|
| 10k_accumulation_2      	| 2020-09-12          	| 10380.98                  	| 2020-09-16 	| 10745.76    	| 3.51            	| 2020-10-11 	| 11309.66  	| 5.25        	| LONG            	| 5.25            	|
| lift_off                	| 2020-10-07          	| 10585.37                  	| 2020-10-11 	| 11309.66    	| 6.84            	| 2020-11-27 	| 17135.02  	| 51.51       	| LONG            	| 51.51           	|

The direction of 8/11 trend based trades was right. The wrong ones are slightly wrong while the right ones are bigly right. Strategy returns 325% overall while buy and hold would had returned 87%. This is a very good strategy except during the accumulation trends. And there are reasons to this.

But some can say that a coin toss can land tail 8/11 times too. Against this, I will do further backtest. And others can claim a look-ahead bias. I can claim I formed the method months ago looking at the 2019 data. But, the more powerful argument against the look-ahead bias will be the test of time, which I am confident this will pass until something fundamentally changes.


## Advanced test
In supervised Machine Learning there is a concept of training set and test set. You use the training set to train your system. In test set, you test it. I don't use ML in these test. But I use a similar concepts for the test here. I define a general set of rules. Then I use the duration between 4 days before creating the group to the start of the group (which i refer to as training set) to optimize the rules. Then I use those rules in the trend (which I refer to as test set).

For the test here, I consider a worse than realistic fee of 0.075% per trade and worse than realistic slippage - 0.01% in both directions. If leverage is used, I consider funding of -0.01% every 8 hours. Furthermore, for a leverage n, I multiply slippage, funding, and fee by the same n to be realistic. 

For this strategy I use 10 min OHLCV data constructed from the bitmex trade data along with:

- **percentage_large:** The % of trade volume of trades whose size is bigger than 10 BTC
- **percentage_large_reversal:** The difference between the 30 mins percentage_large mean and 240 mins percentage_large mean
- **buy_percentage_large:** 30 mins moving average of the percentage of buy trades bigger than 10 BTC
- **sell_percentage_large:** 30 mins moving average of the percentage of sell trades bigger than 10 BTC
- **volatility:** Moving 4 day standard deviation divided by 10
- **rsi:** 10 Min RSI on close price

**Rules:**
<div style="text-align: center">
<textarea readonly style="width: 50vw; height: 250px;">
-IF A POSITION IS OPEN
  -IF IT IS LONG
    -IF pnl < loss_cap_par OR rsi > long_rsi_par OR sell_percentage_large > sell_percentage_large_par:
      CLOSE
  -IF IT IS SHORT
    -IF pnl < loss_cap_par OR rsi < short_rsi_par OR buy_percentage_large > buy_percentage_large_par:
      CLOSE

-IF NO POSITION IS OPEN
  -IF volatility > volatility_par:
    -IF percentage_large_reversal > percentage_large_reversal_par:
      -IF buy_percentage_large > buy_percentage_large_par:
        OPEN LONG
      -IF sell_percentage_large > sell_percentage_large_par:
        OPEN SHORT
</textarea>
</div>
Here loss_cap_par, long_rsi_par, sell_percentage_large_par, loss_cap_par, short_rsi_par, buy_percentage_large_par, volatility_par and percentage_large_reversal_par are parameters whose value will be selected from the test set (4 days before the creation of the group) out of the given allowed values:

- percentage_large_reversal_par: [0, 0.001, 0.005, 0.01, 0.02, 0.03, 0.06, 0.12, 0.24, 0.5]
- buy_percentage_large_par: [0.1,0.2,0.3,0.4,0.5,0.6,0.7,0.8,0.9],
- sell_percentage_large_par: [0.1,0.2,0.3,0.4,0.5,0.6,0.7,0.8,0.9],
- loss_cap_par: [-1,-2],
- volatility_par: [1,3,5,10,20,30,40,50,60],
- long_rsi_par: [75,80,85,90],
- short_rsi_par: [15,20,25,30,35]

And, pnl is the profit/loss % for the open position. I tried not to make this too complicated. I created this method because I have found that big trades are sometimes indicative of future movements.  RSI and loss_cap_par are used to focus on bitcoin's similar reversals. percentage_large_reversal, buy_percentage_large and sell_percentage_large is used to focus on movement following similar trading patterens. And, volatility guard is added because percentage is a very simple way of looking at things and in non volatile times they lose their edge detecting what I wish to.

I run 800 random iteration with random parameters to find the best performing parameter in the training set. Then i use that parameter in the test set whose result is below:
 
| group_name              	| return_% 	| hodl_return 	| lev_3_ret 	| sharpe 	| Backtest                          	|
|-------------------------	|----------	|-------------	|-----------	|--------	|--------------------------------	|
| corona_dump             	| 37.39    	| -33.87      	| 81.23     	| 2.94   	| [Click Here](/static/02136f.html) 	|
| corona_recovery         	| 23.64    	| 27.68       	| 69.95     	| 2.9    	| [Click Here](/static/6f0222.html) 	|
| further_recovery        	| 10.02    	| 26.26       	| 29.27     	| 2.86   	| [Click Here](/static/bd5ac0.html) 	|
| 9k_accumulation         	| -2.22    	| -1.07       	| -8.16     	| -0.24  	| [Click Here](/static/ed6d53.html) 	|
| further_9k_accumulation 	| -3.08    	| -6.02       	| -10.46    	| -2.09  	| [Click Here](/static/6a9326.html) 	|
| final_9k_accumulation   	| 0        	| 5.4         	| 0         	|        	| [Click Here](/static/487305.html) 	|
| initial_takeoff         	| 6.14     	| 22.87       	| 18.41     	| 2.07   	| [Click Here](/static/380e2a.html) 	|
| 12k_dump                	| -23.13   	| -3.7        	| -55.47    	| -10.92 	| [Click Here](/static/6a6ae1.html) 	|
| 10k_accumulation        	| -1.03    	| -5.43       	| -4.36     	| -0.52  	| [Click Here](/static/d154b9.html) 	|
| 10k_accumulation_2      	| -5.02    	| 5.13        	| -14.74    	| -2.35  	| [Click Here](/static/f2cdc6.html) 	|
| lift_off                	| 49.99    	| 51.79       	| 149.97    	| 4.74   	| [Click Here](/static/65a302.html) 	|

[And the best parameters for each detected from the training set here](/images/with_params.jpg). Click on the "Click Here" on each backtest rows to see how the trades performed in the test set.

Overall, our strategy returns 137% while buy and hold returns 86%. A leverage of 3 increases the return to 251%.  Different group has different parameters. One groups parameters would had performed very bad on another. And applying random parameters dosen't make the returns close.

## Conclusion
Mathematically and computationally speaking, all these are a fairly simple system. Beating buy and hold is a big deal. But, I did it with a roughly coded trading logic in a couple of hours with only 4 days of training. I didn't even optimize the parameters fully. There are probably better parameters in the test set - and thus better result in the training. And one can use better rules, optimize parameters as they move, and use leverage smartly to shoot the return to thousands of percentage with a better sharpe ratio. 

There are numerous ways to use this knowledge in a completely different way too. A profitable system can be created with a similar approach by trading volatility, tweaking a HFT system, or even by training your TA parameters on the training set to use it on the test (and you can update the parameters on live test as long as the same trends last).There are numerous possibilities. The hard part here is to see the market as it i and fbeing consistent and starting, stopping and changing as required in your trades.
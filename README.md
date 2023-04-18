# Project 1: Banks during the 2008 Financial Crisis

1. [Introduction](#introduction)
2. [Data Collection](#data-collection)
3. [EDA](#eda)  
3.1. [Returns](#returns)  
3.1.1 [Single Day Return](#single-day-return)  
3.1.2 [SD of Returns](#standard-deviation-of-returns)  
3.1.3 [Distribution of Returns](#distribution)  
3.2 []
4. [Discussion](#discussion)
5. [Conclusion](#conclusion)


## Introduction
As part of my data analysis portfolio, I collected historical data on bank stocks during the 2008 financial crisis using the Alpha Vantage API. The crisis had a profound impact on the banking sector, with many major financial institutions facing bankruptcy or insolvency. The collapse of Lehman Brothers in September 2008 triggered a wave of panic across global financial markets, leading to a sharp decline in stock prices and widespread economic disruption.

For this project, I will be focusing on these banks and their performance before and after the financial crisis:

* Bank of America (BAC)
* CitiGroup (C)
* Goldman Sachs (GS)
* JPMorgan Chase (JPM)
* Morgan Stanley (MS)
* Wells Fargo (WFC)

I used a combination of yahoo finance and Alpha Vantage APIs to retrieve historical data.
## Data Collection

```python
import pandas_datareader.data as web
import pandas as pd
import numpy as np
import datetime as dt
import os
%matplotlib inline
```
To retrieve historical data, I will be using `pandas_datareader`. With this library, I can easily extract financial data from various sources, including Yahoo Finance, Google Finance, and the Federal Reserve Bank of St. Louis. 

In order to get the stock data for each of the banks from January 1st, 2006 to January 1st, 2016, I will need to take the following steps:

1. Use `datetime` to create start and end datetime objects with the respective dates.
2. Find the ticker symbol for each bank that I want to analyze.
3. Use `pandas_datareader` to retrieve the stock data for each bank, and store it in a separate dataframe for each bank, using the ticker symbol as the variable name.

By completing these steps, I will have created a set of dataframes containing the stock data for each bank, which can be used for further analysis and visualization.

[`pandas_datareader` Documentation](https://pandas-datareader.readthedocs.io/en/latest/remote_data.html)

**Defining my start and end dates**
```python
start = dt.datetime(2006,1,1)
end = dt.datetime(2016,1,1)
```

**Creating API Key Variable**
```python
ALPHAVANTAGE_API_KEY = 'xxx'
```

**Testing Alpha Vantage API**
```python
APPL = web.DataReader("AAPL", "av-daily-adjusted", start, end, api_key=ALPHAVANTAGE_API_KEY)
APPL.info()
```

**Create a list of the ticker symbols (as strings) in alphabetical order**
```python
# Bank of America
BAC = web.DataReader("BAC", "av-daily-adjusted", start,
                   end, api_key=ALPHAVANTAGE_API_KEY)
# CitiGroup
C = web.DataReader("C", "av-daily-adjusted", start,
                   end, api_key=ALPHAVANTAGE_API_KEY)
# Goldman Sachs
GS = web.DataReader("GS", "av-daily-adjusted", start,
                      end, api_key=ALPHAVANTAGE_API_KEY)
# JPMorgan Chase
JPM = web.DataReader("JPM", "av-daily-adjusted", start,
                   end, api_key=ALPHAVANTAGE_API_KEY)
# Morgan Stanley
MS = web.DataReader("MS", "av-daily-adjusted", start,
                   end, api_key=ALPHAVANTAGE_API_KEY)
# Wells Fargo
WFC = web.DataReader("WFC", "av-daily-adjusted", start,
                      end, api_key=ALPHAVANTAGE_API_KEY)
```
```python
tickers = ['BAC',
           'C',
           'GS',
           'JPM',
           'MS',
           'WFC']
```
**Concatenate bank dataframes together, setting the argument key = tickers, concatenating column wise ie. axis = 1**
```python
bank_stocks = pd.concat([BAC,
                         C,
                         GS,
                         JPM,
                         MS,
                         WFC], axis=1, keys=tickers)

bank_stocks.head()
```
*At this point I realised that I had a limit on the number of calls using Alpha Vantage, hence I downloaded the bank_stocks dataframe as a local csv file*
```python
bank_stocks.to_csv('bank_stocks.csv')
```

```python
bank_stocks = pd.read_csv('bank_stocks.csv')
bank_stocks.columns.names = ['Bank Ticker','Stock Info']
bank_stocks.info()
```

```python
bank_stocks.head()
```


## EDA
**Close price of banks**
```python
bank_stocks.xs(key='Close', axis=1, level='Stock Info')
```
**Highest Close price for each bank's stock from 2006 to 2016**
```python
bank_stocks.xs(key='Close', axis=1, level='Stock Info').max()
```

**Lowest Close price for each bank's stock from 2006 to 2016**
```python
bank_stocks.xs(key='Close', axis=1, level='Stock Info').min()
```
**New dataframe of bank returns**
```python
returns = pd.DataFrame()
```
**Creating a percentage change column in `returns`**
```python
for tick in tickers:
    returns[tick+' Return'] = df[tick]['Close'].pct_change()
returns.head()
```
This portion was a bit tougher for me and I had to breakdown the logic step by step before being able to move on.
1. `for tick in tickers:`: This sets up a loop that will iterate through each bank's stock ticker in the list tickers.

2. `returns[tick+' Return']`: This creates a new column in the returns DataFrame with a column name that includes the bank's ticker symbol and the word "Return". For example, for Bank of America, the new column name will be "BAC Return".

3. `bank_stocks[tick]['close'].pct_change()`: This calculates the percentage change in daily closing price for the bank's stock, using the `.pct_change()` method of the close column of the bank's stock data in the `bank_stocks` DataFrame.

4. The result of this calculation is then assigned to the new column in the returns DataFrame for the corresponding bank.

### Returns
```python
import seaborn as sns
```
**Pairplot of returns dataframe**
```python
sns.pairplot(returns)
```
![Pairplot of returns](https://i.imgur.com/7kXmUwl.png)

The pairplot graph shows that **Citigroup consistently underperforms** compared to the other banks. This suggests that there may be specific factors unique to **Citigroup** negatively affecting its performance. Further investigation is needed to identify these factors and potential solutions.

*It is also important to consider potential interactions between the variables being compared, as the pairplot graph only shows pairwise relationships.*

The **government bailout in 2008** was a response to the financial crisis caused by factors such as subprime mortgage lending, securitization, and regulatory failures. It's possible that some of these same factors could be contributing to **Citigroup** underperformance today.

For instance, **Citigroup** heavy investment in subprime mortgages or other high-risk assets prior to the financial crisis could have impacted its performance and contributed to the need for a bailout. Similarly, slow adaptation to changes in the regulatory landscape or ineffective risk management practices may limit its ability to compete with other banks and achieve strong returns.

#### Single Day Return

**Worst single day return**
```python
returns.idxmin()
```
```python
BAC Return   2009-01-20
C Return     2011-05-06
GS Return    2009-01-20
JPM Return   2009-01-20
MS Return    2008-10-09
WFC Return   2009-01-20
dtype: datetime64[ns]
```
It appears that the worst days for Bank of America (BAC), Goldman Sachs (GS), JPMorgan Chase (JPM), and Wells Fargo (WFC) all fell on the same day. After some investigation, it was discovered that this day was January 20, 2009, which was the day of President Barack Obama's inauguration.

This could potentially be attributed to the uncertainty surrounding the transfer of power from one administration to another, as well as the ongoing financial crisis at the time. It's possible that investors were particularly cautious on this day and reacted negatively to any news or events that could further impact the financial sector.

**Best single day return**
```python
returns.idxmax()
```
```python
BAC Return   2009-04-09
C Return     2011-05-09
GS Return    2008-11-24
JPM Return   2009-01-21
MS Return    2008-10-13
WFC Return   2008-07-16
dtype: datetime64[ns]
```

**JPM**  
It's difficult to say with certainty what caused JPM's best day to be so close to their worst day with respect to the inauguration day, as there could be a number of factors at play.

One potential explanation could be related to market sentiment or investor perception. In the lead-up to the inauguration, there may have been a great deal of uncertainty or concern among investors about how the new administration would impact the financial sector. This could have led to a sharp sell-off in bank stocks on the day of the inauguration.

However, once the new administration was officially in place and began to unveil its policies, investors may have become more optimistic about the outlook for the financial sector. This could have contributed to JPM's strong performance on the following day.

**Citigroup**  
The dates of Citigroup's worst and best days are in close proximity to each other. Upon conducting further research, it was found that a stock split occurred on 2011-05-09, with a 1:10 split ratio.

A stock split has the potential to improve Citigroup's single day return. When a company undergoes a stock split, it increases the number of outstanding shares while decreasing the price of each individual share. This can make the stock more accessible to a broader range of investors, which could increase demand and drive up the price.

#### Standard Deviation of Returns
**10 Year Period (2006 - 2016)**
```python
returns.std()
```

```python
BAC Return    0.036650
C Return      0.179969
GS Return     0.025346
JPM Return    0.027656
MS Return     0.037820
WFC Return    0.030233
dtype: float64
```

Over the 10-year period, Citigroup had the highest daily standard deviation of its returns compared to the other banks in the dataset. 

**1 Year Period (2008)**  
**Choosing the specific columns**
```python
returns.loc['2008-01-01' : '2008-12-31']
```

```python

            BAC Return	C Return	GS Return	JPM Return	MS Return	WFC Return
Date						
2008-01-02	-0.016966	-0.017663	-0.034643	-0.033906	-0.040670	-0.036105
2008-01-03	-0.006410	0.000346	-0.013295	-0.006877	-0.000196	-0.019931
2008-01-04	-0.011166	-0.023851	-0.023970	-0.022684	-0.032195	-0.036115
2008-01-07	0.001255	0.000708	-0.026009	0.010017	-0.020081	0.006912
2008-01-08	-0.037343	-0.039632	-0.026858	-0.039671	-0.039536	-0.042630
...	...	...	...	...	...	...
2008-12-24	0.061176	0.039877	0.016489	0.025421	0.004155	0.018155
2008-12-26	-0.012565	-0.007375	-0.006149	-0.001675	0.010345	0.001092
2008-12-29	-0.031437	-0.023774	0.007766	-0.000671	0.017065	0.011632
2008-12-30	0.023184	0.035008	0.071839	0.041303	0.019463	0.034854
2008-12-31	0.063444	-0.013235	0.028394	0.016769	0.055958	0.023611
```
**SD of 2008**
```python
returns.loc['2008-01-01': '2008-12-31'].std()
```
```python
BAC Return    0.016163
C Return      0.015289
GS Return     0.014046
JPM Return    0.014017
MS Return     0.016249
WFC Return    0.012591
dtype: float64
```
#### Distribution
I will be analyzing the distribution of returns for various banks, including the **BAC Return**. I have replicated the code for each bank to visualize their return distributions.
```python
import matplotlib.pyplot as plt
```

##### 2008

**BAC Return**
```python
sns.displot(returns.loc['2008-01-01': '2008-12-31']['BAC Return'], kde=True)

plt.title('BAC Return 2008')
plt.xlabel('Daily Returns')
plt.ylabel('Frequency')

mean = returns.loc['2008-01-01': '2008-12-31']['BAC Return'].mean()
std = returns.loc['2008-01-01': '2008-12-31']['BAC Return'].std()
plt.axvline(x=mean, color='r', linestyle='--', label=f'Mean = {mean:.3f}')
plt.axvline(x=mean+std, color='g', linestyle='--',
            label=f'Std Dev = {std:.3f}')
plt.axvline(x=mean-std, color='g', linestyle='--')

plt.legend()
plt.tight_layout()
plt.show()

```
![BAC](https://i.imgur.com/QAfjSRB.png)

**C Return**  
![C](https://i.imgur.com/BJCZIkE.png)

**GS Return**  
![GS](https://i.imgur.com/IY26Lgj.png)  

**JPM Return**  
![JPM](https://i.imgur.com/zBRXTRQ.png)  

**MS Return**  
![MS](https://i.imgur.com/y9klYnO.png)  

**WFC Return**  
![WFC](https://i.imgur.com/vfnwMUP.png) 

The distribution plots above show that the mean of returns for all banks is relatively low, around 0.01, as indicated by the dotted red line. However, the standard deviations vary among the banks. **MS** has the highest standard deviation of 0.088, while **GS** has a standard deviation of 0.050, suggesting that **MS** returns may be more volatile than those of **GS**. The distribution plot suggests that the return distribution of **MS** is more skewed to the right, whereas the distribution of **GS** is more centralized.

## Discussion
This is the discussion.

## Conclusion
This is the conclusion.


# Data-Analysis
This repository is a portfolio of projects I have completed for self learning, fun and understanding data better :)

# Project 1: 911 Calls

# Project 2: Banks during the 2008 Financial Crisis

1. [Introduction](#introduction)
2. [Data Collection](#data-collection)
3. [EDA](#eda)  
3.1. [Visualisation](#visualisation)
5. [Results](#results)
6. [Conclusion](#conclusion)


## Introduction
As part of my data analysis portfolio, I collected historical data on bank stocks during the 2008 financial crisis using the Alpha Vantage API. The crisis had a profound impact on the banking sector, with many major financial institutions facing bankruptcy or insolvency. The collapse of Lehman Brothers in September 2008 triggered a wave of panic across global financial markets, leading to a sharp decline in stock prices and widespread economic disruption.

For this project, I will be focusing on these banks and their performance before and after the financial crisis:

* Bank of America
* CitiGroup
* Goldman Sachs
* JPMorgan Chase
* Morgan Stanley
* Wells Fargo

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

### Visualisation
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

**Worst single day return**
```python
returns.idxmin()
print(returns.idxmin())
```

**Best single day return**
```python
returns.idxmax()
```
## Results
This is the introduction.

## Conclusion
This is the introduction.


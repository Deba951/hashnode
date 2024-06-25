---
title: "Understanding CryptoPunks Volume and Prices through Python"
seoTitle: "CryptoPunks Analysis with Python"
seoDescription: "Analyze CryptoPunks NFT trading trends using Python: retrieve, process, and visualize data for insights on trading volume and average prices"
datePublished: Tue Jun 25 2024 14:45:55 GMT+0000 (Coordinated Universal Time)
cuid: clxuirb60000a0al218tgaf8l
slug: understanding-cryptopunks-volume-and-prices-through-python
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1719325323168/ba16525c-8661-4ea1-8a54-83957a67f0a4.webp
tags: data-science, data-analysis, matplotlib, nft-trading-platform-market-report-nft-trading-platform-industry-report-nft-trading-platform-market-size-nft-trading-platform-market-share-nft-trading-platform-market-growth-nft-trading-platform-market-trends-nft-trading-platform-market-forecast-nft-trading-platform-market-estimation, pythondataanalysis, cryptopunks-analysis, moralis-api

---

## Objective

The objective of this report is to analyze the trading volume and average price of CryptoPunks NFTs on the Ethereum blockchain. By fetching and processing data from the Moralis API, we aim to visualize the trends over a specified period.

## Aim

We aim to retrieve NFT transaction data, clean and process this data, and finally create a visualization that showcases the trading volume and average price of CryptoPunks NFTs over the past few days. This will help us understand market trends and investor behaviour.

## Procedure

To achieve our aim, we followed these steps:

1. Set up the environment and load necessary libraries.
    
2. Retrieve CryptoPunks transaction data using the Moralis API.
    
3. Process and clean the data.
    
4. Calculate daily volumes and average prices.
    
5. Visualize the results using Matplotlib.
    

## Steps Followed -

1. ### Setting up the Environment:
    

To begin with, we set up our development environment by installing necessary libraries, setting environment variables, and importing required modules. We store our API key in a `.env` file for security purposes and load it into our script.

```python
from moralis import evm_api
import numpy as np
import pandas as pd
from pandas import json_normalize
import matplotlib.pyplot as plt
import datetime
import time
import os
from dotenv import load_dotenv
```

* **Explanation:**
    
    * `moralis`: Used for accessing the Moralis API.
        
    * `numpy`: Provides support for large multi-dimensional arrays and matrices.
        
    * `pandas`: Used for data manipulation and analysis.
        
    * `matplotlib`: Plotting library for creating visualizations.
        
    * `datetime`: Provides classes for manipulating dates and times.
        
    * `time`: Provides various time-related functions.
        
    * `os`: Provides a way of using operating system-dependent functionality.
        
    * `dotenv`: Loads environment variables from a `.env` file.
        

2. ### **Loading Environment Variables:**
    

We load the environment variables, specifically the Moralis API key, which is stored in a `.env` file:

**API Utilized:** Moralis EVM API

```python
load_dotenv()
moralis_api_key = os.getenv("api_key")
```

3. ### Retrieving Data from the Moralis API:
    

We then use the Moralis API to fetch NFT transaction data. The data is retrieved in chunks, and we concatenate these chunks into a single DataFrame

* **NFT Contract Address:** 0xb47e3cd837dDF8e4c57F05d70Ab865de6e193BBB
    
* **Blockchain:** Ethereum (ETH)
    

```python
page_cursor = ''
data_frame = pd.DataFrame()

for iteration in range(2):
    api_response = evm_api.nft.get_nft_contract_transfers(
        api_key=moralis_api_key,
        params={
            "address": "0xb47e3cd837dDF8e4c57F05d70Ab865de6e193BBB", 
            "chain": "eth",
            "cursor": page_cursor
        }
    )
    page_cursor = api_response["cursor"]
    temp_df = json_normalize(api_response['result'])
    if data_frame.empty:
        data_frame = temp_df
    else:
        data_frame = pd.concat([data_frame, temp_df])
    time.sleep(1.1)
```

* **Explanation:**
    
    * `evm_api.nft.get_nft_contract_transfers`: Fetches NFT transfer data.
        
    * `page_cursor`: Used for pagination.
        
    * `json_normalize`: Converts JSON data into a pandas DataFrame.
        
    * `pd.concat`: Concatenates DataFrames.
        
    * `time.sleep(1.1)`: Ensures we do not exceed the API rate limit.
        

4. ### Processing and Cleaning the Data:
    

* Transactions with a value of '0' were removed to focus on meaningful trades.
    
* The dataset was normalized and processed to extract the date and transaction values in ETH
    

```python
data_frame = data_frame[data_frame['value'] != '0']
data_frame['Date'] = data_frame.apply(lambda row: datetime.datetime.strptime(row.block_timestamp[0:10], '%Y-%m-%d').strftime('%b %d'), axis=1)

unique_dates = data_frame.Date.unique()[0:7]
unique_dates = unique_dates[::-1]
```

**Explanation:**

* `data_frame['value'] != '0'`: Filters out transactions with zero value.
    
* `datetime.datetime.strptime`: Parses date strings into date-time objects.
    
* `strftime('%b %d')`: Formats dates to "Month Day" format.
    
* `data_`[`frame.Date`](http://frame.Date)`.unique()`: Extracts unique dates.
    
* `[::-1]`: Reverses the order of dates.
    

5. ### Calculating Daily Volumes and Average Prices:
    

#### Key Metrics:

* **Dates Analyzed:** {dates}
    
* **Daily Trading Volume (ETH):** {volumes}
    
* **Daily Average Price (ETH):** {avgs}
    

For each unique date, we calculate the total trading volume and the average price of the transactions.

```python
daily_volumes = []
daily_averages = []

for date in unique_dates:
    date_df = data_frame[data_frame.Date == date]
    eth_values = [int(val) / 1e18 for val in date_df['value']]
    daily_volumes.append(np.sum(eth_values))
    daily_averages.append(np.mean(eth_values))
```

**Explanation:**

* `int(val) / 1e18`: Converts Wei (smallest unit of ETH) to ETH.
    
* `np.sum`: Calculates the sum.
    
* `np.mean`: Calculates the average.
    

6. ### Visualizing the Results:
    

We plot the daily volumes and average prices using Matplotlib:

```python
plt.style.use('dark_background')
figure, axis1 = plt.subplots()
figure.set_facecolor('#202225')
axis1.set_facecolor("#202225")
axis1.grid(axis="y", zorder=0, color="#36383F")
axis1.bar(x=unique_dates, height=daily_volumes, width=0.4, zorder=3, color="#E6E8EB")
axis1.yaxis.set_major_locator(plt.MaxNLocator(3))
axis1.set_ylabel("Volume (ETH)", fontweight='heavy', labelpad=20)
axis1.spines['top'].set_visible(False)
axis1.spines['right'].set_visible(False)
axis1.spines['bottom'].set_visible(False)
axis1.spines['left'].set_visible(False)

axis2 = axis1.twinx()
axis2.plot(unique_dates, daily_averages, color='#5B60E0')
axis2.yaxis.set_major_locator(plt.MaxNLocator(3))
axis2.set_ylabel("Average Price (ETH)", fontweight='heavy', rotation=270, labelpad=20)
axis2.spines['top'].set_visible(False)
axis2.spines['right'].set_visible(False)
axis2.spines['bottom'].set_visible(False)
axis2.spines['left'].set_visible(False)

plt.title('CryptoPunks Volume and Price', loc='left', fontsize=16, fontweight="heavy", verticalalignment="top")
plt.show()
```

**Explanation:**

* [`plt.style`](http://plt.style)`.use('dark_background')`: Sets the dark background style.
    
* `figure.set_facecolor('#202225')`: Sets the background colour of the figure.
    
* [`axis1.bar`](http://axis1.bar): Creates a bar chart for daily volumes.
    
* `axis2.plot`: Creates a line plot for average prices.
    
* `plt.title`: Sets the title of the plot.
    

#### Detailed Analysis:

* **Highest Volume Day:** {dates\[np.argmax(volumes)\]} with {max(volumes)} ETH
    
* **Lowest Volume Day:** {dates\[np.argmin(volumes)\]} with {min(volumes)} ETH
    
* **Highest Average Price Day:** {dates\[np.argmax(avgs)\]} with {max(avgs)} ETH
    
* **Lowest Average Price Day:** {dates\[np.argmin(avgs)\]} with {min(avgs)} ETH
    

## Insights

1. **Volume Trends:** The bar chart shows the daily volume of CryptoPunks transactions in ETH. This can help identify the days with the highest trading activity.
    
2. **Price Trends:** The line plot indicates the average price of CryptoPunks transactions in ETH. This helps in understanding the market value and price fluctuations of CryptoPunks over the selected period.
    

## Results

From the visualization, we observe the following insights:

1. The daily trading volume of CryptoPunks NFTs fluctuates significantly.
    
2. There are noticeable peaks in trading volume on certain days which may correspond to market events or external factors influencing CryptoPunks transactions.
    
3. The average price of CryptoPunks NFTs also varies, showing trends that may indicate market demand.
    

## Conclusion

By analyzing the CryptoPunks NFT transaction data, we can gain valuable insights into market behaviour and trends. The visualization clearly shows how trading volumes and average prices change over time, providing a useful tool for investors and analysts.

This report demonstrates the power of Python for data analysis and visualization, showcasing how various libraries can be used together to extract, process, and present data in a meaningful way.

## Visual Data:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1719324442616/cca43e5e-5cec-42fa-9caa-7e10bf44fa3f.png align="center")

## Reference Links:

1. [Moralis API KEY](https://admin.moralis.io/)
    
2. [Moralis Documentation](https://docs.moralis.io/web3-data-api/evm/reference/get-nft-contract-transfers?address=116%2F%20Ram%20Sita%20Ghat%20Street%2C%20May%20Fair%20Dream%20-%20101%2C%20Uttarpara%2C%20Doltala%2C%20Bhadrakali%2C%20Hooghly.&chain=eth&format=decimal&order=DESC)
    
3. [NFT COntract Address](https://etherscan.io/token/0xb47e3cd837ddf8e4c57f05d70ab865de6e193bbb)
    
4. [GitHub](https://github.com/Deba951/cryptopunks-analysis)
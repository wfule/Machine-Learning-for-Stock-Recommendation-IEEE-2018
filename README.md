# Dynamic-Stock-Recommendation-Machine_Learning

## First Author: Published paper on IEEE TrustCom 2018 (http://www.cloud-conf.net/trustcom18/)
Hongyang Yang, Xiao-Yang Liu, Qingwei W. [A Practical Machine Learning Approach for Dynamic Stock Recommendation](https://ssrn.com/abstract=3302088). IEEE TrustCom 2018.

### IEEE Official Link of the paper (https://ieeexplore.ieee.org/abstract/document/8456121)
### SSRN Version: (https://ssrn.com/abstract=3302088)

## Abstract:
Stock recommendation is vital to investment companies and investors. However, no single stock selection strategy will always win while analysts may not have enough time to check all S&P 500 stocks (the Standard & Poor’s 500). In this paper, we propose a practical scheme that recommends stocks from S&P 500 using machine learning. Our basic idea is to buy and hold the top 20% stocks dynamically. First, we select representative stock indicators with good explanatory power. Secondly, we take five frequently used machine learning methods, including linear regression, ridge regression, stepwise regression, random forest and generalized boosted regression, to model stock indicators and quarterly log-return in a rolling window. Thirdly, we choose the model with the lowest Mean Square Error in each period to rank stocks. Finally, we test the selected stocks by conducting portfolio allocation methods such as equally weighted, mean- variance, and minimum-variance. Our empirical results show that the proposed scheme outperforms the long-only strategy on the S&P 500 index in terms of Sharpe ratio and cumulative returns.

## Index Term:
Stock recommendation, fundamental value investing, machine learning, model selection, risk management

## Project summary：
+ We developed a practical approach to using machine-learning methods selecting S&P 500 stocks based on financial ratios (e.g., EPS, ROA, ROE, etc). Outperformed the S&P 500 index on out of sample data, achieved a Sharpe ratio of 0.5 (0.19 on SPX).
+ We performed feature selection by 11 GICS sectors based on a rolling window to choose the lowest MSE model among Linear Regression, Stepwise Regression, Regression with Ridge, Random Forest, and GBM. Applied a model ensemble method.

<img src=figs/chart10_insample.PNG width="500">

<img src=figs/chart11_overallPerformance.PNG width="500">

## Data:
Retrieved from __WRDS (Wharton Research Data Services)__, Compustat Industrial [27 years daily and quarterly Data]

<img src=figs/chart1_datasetPeriod.PNG width="500">


+ __S&P 500 Fundamental Quarterly Data__ ([fundamental_final_table.xlsx](Data/fundamental_final_table.xlsx))
  + Database: Compustat North America (Fundamentals Quarterly) and (Index Constituents)
  + Timeline: 27 years (1990-2017)
  + Tickers: 1193 stock (all historical S&P 500 component stocks)
  + Value: 20 financial ratios calculated from raw accouting report data

+ __S&P 500 Historical Component Stocks Adjusted Daily Price__ ([1-sp500_adj_price.csv.zip](Data/1-sp500_adj_price.csv.zip))
  + Database: Compustat North America (Security Daily)
  + Timeline: 27 years (1990-2017)
  + Tickers: 1193 stock (all historical S&P 500 component stocks)
  + Value: Adjusted Daily Close Price

+ __S&P 500 Index Daily Price__ ([1-spx_price.xlsx](Data/1-spx_price.xlsx))
  + Database: Yahoo Finance
  + Timeline: 27 years (1990-2017)
  + Tickers: SPX
  + Value: Adjusted Daily Close Price

## Code:

### __Focasting Model__:
+ __Input__: 11 Excel files of cleaned data about fundamental financial ratios (sector 10-Energy, sector 15-Materials, sector 20-Industrials, sector 25-Consumer Discretionary, sector 30-Consumer Staples, sector 35-Health Care, sector 40-Financials, sector 45-Information Technology, sector 50-Telecommunication Services, sector 55-Utilities, sector 60-Real Estate)
+ __Python Script__: 2 Scripts
  + [ml_model.py](code/ml_model.py): The forecasting function (cornerstone of this project)
  + [fundamental_run_model.py](fundamental_run_model.py): The main function to run the forecasting model  
```shell

python3 fundamental_run_model.py \
  -sector_name sector10 \
  -fundamental Data/fundamental_final_table.xlsx \
  -sector Data/1-focasting_data/sector10_clean.xlsx 
```


+ __Old R Script__: 3 R Scripts
  + [fundamental_run_model.R](code/fundamental_run_model.R): The main function to run the forecasting model
  + [fundamental_ML_model.R](code/fundamental_ML_model.R): The forecasting function (cornerstone of this project)
  + [fundamental_select_stock.R](code/fundamental_select_stock.R): The function to select top 20% stocks in each sector
+ __Output__: [a CSV file](Data/2-portfolio_data/stocks_selected_total_user8.csv) includes __tic__: the stock name, __predicted_return__: predicted return of next quarter by our model, __trade_date__: the date to execute the trades





### __Portfolio Allocation__:

+ __Input__: 2 files
  + The [CSV file](Data/2-portfolio_data/stocks_selected_total_user8.csv) generated by forecasting model
  + The [adjusted close price data of S&P 500 stocks](Data/1-sp500_adj_price.csv.zip) to calculate covariance matrix

+ __Script__: [fundamental_portfolio.ipynb](fundamental_portfolio.ipynb)

+ __Output__: 3 Excel files each with the following 4 columns
  1. __tic__: the stock name
  2. __predicted_return__: predicted return of next quarter by our model
  3. __weights__: the weights to trade
  4. __trade_date__: the date to execute the trades



### __Back-testing Model__:

+ __Input__: 5 files
  + [equally_weighted](Data/2-portfolio_data/equally_weighted_user8.xlsx): equally-weighted portfolio (Portfolio Benchmark)
  + [mean_weighted](Data/2-portfolio_data/mean_weighted_user8.xlsx): mean-variance portfolio
  + [minimum_weighted](Data/2-portfolio_data/minimum_weighted_user8.xlsx): minimum-variance portfolio (our model)
  + [adjusted daily close price of S&P 500 stocks](Data/1-sp500_adj_price.csv.zip): to calcualte quarterly return
  + [SPX adjusted daily close price](Data/1-spx_price.xlsx): The Market Index (Overall Benchmark)

+ __Script__: 1 Python jupyter notebook Script
  + [fundamental_back_testing.ipynb](code/fundamental_back_testing.ipynb): The back-testing function

+ __Output__:
  1. Quarterly return of our portfolio with transaction cost
  2. Performance Evaluation: total return, annulized return and standard deviation, maximum drawdown, Sharpe ratio

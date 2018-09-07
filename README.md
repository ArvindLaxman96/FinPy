# FinPy
import pandas as pd
import numpy as np
import pandas_datareader
from pandas_datareader import data as wb
import matplotlib.pyplot as plt
%matplotlib inline
tickers= ['UNH','AAPL','MMM','HD','GS','MCD', 'BA'] #creating a array containing the stock tickers
stock_data=pd.DataFrame() # A dataframe created to store the stock data
for t in tickers: # For loop created to call all the data from the iex data provider
    stock_data[t]=wb.DataReader(t,data_source='iex',start='2013-1-1')['close']
    
(stock_data/stock_data.iloc[0]*100).plot(figsize=(15,6)) # The should to normalised in order to ensure smooth analysis
log_returns=np.log(stock_data/stock_data.shift(1)) # Calculating the log returns of each stock
print (log_returns.head())

num_tickers = len(tickers) # num_tickers stores the number of stocks in the portfolio
port_ret= [] # creating a dataframe that storres the portfolio returns  
port_vol=[]

for i in range(1000): # Monte-Carlo running 1000 simulations with differents weights
    wieghts = np.random.random(num_tickers) # randomizing the weights 
    wieghts /= np.sum(wieghts) # making sure that the total of all the weights equals one
    port_ret.append(np.sum(wieghts*log_returns.mean())*250) # calculating and storing expected returns in port_ret dataframe
    port_vol.append(np.sqrt(np.dot(wieghts.T , np.dot(log_returns.cov()*250,wieghts)))) # calculating and storing the expected volatility in port_vol dataframe
port_ret= np.array(port_ret)
port_vol= np.array(port_vol)
print (pd.DataFrame({'Portfolio Return':port_ret,'Stock Volatility':port_vol}))

portfolio = pd.DataFrame({'Return':port_ret, 'Volatility': port_vol}) # Merging the two dataframes in one 'portfolio dataframe
portfolio.plot(x='Volatility',y='Return', kind='scatter',color='Blue',figsize=(10,6))
plt.xlabel('Volatility')
plt.ylabel('Return')
plt.title('The Efficient Frontier of 7-asset portfolio')

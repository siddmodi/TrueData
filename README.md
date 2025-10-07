# Official Python repository for TrueData (Market Data APIs)
----
[![GitMCP](https://img.shields.io/endpoint?url=https://gitmcp.io/badge/siddmodi/TrueData)](https://gitmcp.io/siddmodi/TrueData)
This Python library attempts to make it easy for you to connect to TrueData Market Data Apis, thereby allowing you to concentrate on startegy development, while this library works to get you all the data you need from the TrueData backend both for Real Time & Historical.

Please make sure you follow us on our Telegram channel where we push a lot of information with regards to API updates, implementation ideas, tips & tricks to use this library & raw feed, etc...
* [TrueData Market Data API - Telegram Channel](https://t.me/truedata_ws_api)
* [TrueData Market Data APIs - Website](https://www.truedata.in/api)

We have also built a sandbox environmemt for testing the raw feed. Please feel free to use this environment to check/test/compare your results with the raw data feed (real time & historical).
* [TrueData Market Data API - Sandbox Environment](https://wstest.truedata.in)
* [sample code to use Truedata package](https://github.com/Nahas-N/Truedata-sample-code)

We are trying to improve this library continuously and feedback for the same is welcome. 

It is essential to ensure that the data received through our APIs is not being utilized for any commercial purposes. Additionally, please be mindful that all information or data provided to you is exclusively intended for your internal use and must be utilized and discontinued at your end as required.

----
## What have we covered so far ?

we have total three packages live , historical and analytics

**WebSocket APIs**

  * Live data (Streaming Ticks) - Enabled by Default
  * Live Data (Streaming 1 min bars) - Needs to be enabled from our backend
  * Live Data (Streaming 5 min bars) - Needs to be enabled from our backend 
  * Live data (Streaming Ticks + 1 min bars) - Needs to be enabled from our backend
  * Live Data (Streaming Ticks + 5 min bars) - Needs to be enabled from our backend
  * Live Data (Streaming Ticks + 1 min bars + 5 min bars) - Needs to be enabled from our backend
  * Live Data (Streaming 1 min + 5 min bars) - Needs to be enabled from our backend 
  * Option Greek streaming - Needs to be enabled from our backend

Note:- Kindly note that data that is not enabled by default may require exchange approvals, which vary depending on the specific exchange and segment in question. For any inquiries or clarifications, please feel free to reach out to our dedicated support team.

**REST APIs**

  *  Historical Data
  *  Analytical Data
  
----
# Getting Started 
**Installation**

* Installing the truedata library from PyPi
```shell script
python3 -m pip install truedata
```

**Minimum Requirements**
```
- Python >= 3.10

In-built dependencies

- websocket-client>=0.57.0
- colorama>=0.4.3
- python-dateutil>=2.8.1
- pandas>=1.0.3
- setuptools>=50.3.2
- requests>=2.25.0
- tqdm>=4.66.6
- lz4==3.1.3 (Note lz4 versions >3.1.3 currently have some errors and thus
  lz4 should not be upgraded till these dependency issues are resolved).

```

**Connecting / Logging in**
* Connecting / Logging in for Real time data feed subscriptions
```python
from truedata import TD_live
td_obj = TD_live('<enter_your_login_id>', '<enter_your_password>' )
```
* Connecting / Logging in for Historical Data Subscription Only
```python
from truedata import TD_hist
td_hist = TD_hist('<enter_your_login_id>', '<enter_your_password>')
```
**importing multiple module from truedata package**

* to use live and history module together 
```python
from truedata import TD_live, TD_hist
td_obj = TD_live('<enter_your_login_id>', '<enter_your_password>' )
td_hist = TD_hist('<enter_your_login_id>', '<enter_your_password>' )
```


# Logging

We have integrated the python stdlib logger.

You can provide LOG_LEVEL, LOG_HANDLER and LOG_FORMAT if you want.

Please try with various log levels & formats to understand what works best for you for a particular setting. Below are 2 samples provided to you. Please test with both to see what works best for you.
 

```python
from truedata import TD_live
import logging
td_obj = TD_live('<enter_your_login_id>', '<enter_your_password>', live_port=realtime_port,  url=url, 
            log_level=logging.WARNING, log_format="%(message)s")
```
To enable the *Heartbeats*, change your logging level to DEBUG as follows:-

```python
log_level=logging.DEBUG
```
The logging level to DEBUG, enables you to see:-

1) Market Status messages (Market Open / Close messages as and when they happen) 
2) Automatic Touchline update messages
3) Symbol Add Remove messages
4) Heartbeat messages (every 5 seconds)

If you do not want to see these messages, set your logging level to WARNING

Additional LOG_LEVEL info can be found [here](https://docs.python.org/3/library/logging.html#logging-levels). 

Additional LOG_FORMAT info can be found [here](https://docs.python.org/3/library/logging.html#logrecord-attributes).

Additional LOG_HANDER info can be found [here](https://docs.python.org/3/library/logging.html#handler-objects).

----
# Real Time Data Streaming (Live)
* Starting Live Data For Multiple symbols

```python
symbols = ['<symbol_1>', '<symbol_2>', '<symbol_3>', ...]
td_obj.start_live_data(symbols)
```

* Accessing live streaming data
  
    all livedata information available inside td_obj.live_data dict can be accessed via symbol as key. if new data for the same symbol comes then it will replace with latest data.
```python
td_obj.live_data[symbol_1]
```

> if subscribed to `1 Min` streaming bar data can be found at `td_obj.one_min_live_data[symbol_1]`.
> 
>if subcribed to `5 Min` streaming bar data can be found at `td_obj.five_min_live_data[symbol_1]`.

* all data can be accessed with dot access
```python
ltp = td_obj.live_data[symbol_1].ltp
timestamp = td_obj.live_data[symbol_1].timestamp
```
* live_data available fields are 
  
```python
["timestamp","symbol_id","symbol","ltp","ltq","atp","ttq","day_open","day_high","day_low","prev_day_close","oi","prev_day_oi","turnover","special_tag","tick_seq","best_bid_price","best_bid_qty","best_ask_price","best_ask_qty","change","change_perc","oi_change","oi_change_perc" ]
```

one_min_live_data and five_min_live_data available fields are 

```python
[ "symbol", "symbol_id", "day_open", "day_high", "day_low", "prev_day_close", "prev_day_oi", "oi", "ttq", "timestamp", "open", "high", "low", "close", "volume", "change","change_perc","oi_change","oi_change_perc" ]
```

* callback functions

* Accessing live streaming data
  user need to define callback function for ticks or minute stream according to their subscription. if callback function defined then when ever new tick or minute bar received then the corresponding callback function will be executed, so please dont block the functions using any indefinite loops
  
* available call back decorators are mentioned below . all fields corresponding tick_data or bar_data can be accessed with dotaccess mentioned above

```python

@td_obj.trade_callback
def my_tick_data( tick_data):
    print( "tick data " , tick_data )

@td_obj.bidask_callback
def my_bidask_data( bidask_data):
    print("bid ask data" , bidask_data)

@td_obj.one_min_bar_callback
def my_one_min_bar_data( bar_data):
    print("one min bar data ", bar_data)

@td_obj.five_min_bar_callback
def my_five_min_bar_data( bar_data):
    print( "five min bar data ", bar_data)

@td_obj.greek_callback
def my_greek_data( greek_data):
    print("greek data ", greek_data)

@td_obj.full_feed_trade_callback
def my_ff_trade_data( tick_data):
    print("full feed tick ", tick_data)

@td_obj.full_feed_bar_callback
def my_ff_min_bar_data( bar_data):
    print("full feed bar ", bar_data )
```
* Stopping live data

```python
td_obj.stop_live_data(['<symbol_1>', '<symbol_2>', '<symbol_3>', ...])
```
* Disconnect from the WebSocket service
```python
td_obj.disconnect()
```
----
# Option Greeks streaming
If user is subscribed to Nse options and also opt for option greek then whenever new greek data arrived the below function executed. 
```Python
@td_obj.greek_callback
def mygreek_callback( greek_data):
    print("Greek > ", greek_data)
```

* These are available fields for greek_data ->
```python 
 [ "timestamp", "symbol_id", "symbol", "iv", "delta", "theta", "gamma", "vega", "rho" ] 
```

* Each field can be accessed with dot access such as greek_data.symbol , greek_data.delta etc...
----
# Bidask Streaming 
If user is subscribed to bidask then whenever new bidask changed in exchange the below function executed. 
```Python
@td_obj.bidask_callback
def mybidask_callback( bidask_data):
    print("BidAsk > ", bidask_data )
```

* These are available fields for bidask_data -> 
```python
 [ "timestamp", "symbol_id", "symbol", "bid", "ask", "total_bid", "total_ask" ]
``` 
* Each field can be accessed with dot access such as bidask_data.symbol , bidask_data.ask etc...
* For Nse symbols we offer level 1 bid ask which have one best bid and ask data.
* For bse symbols we offer level 2 bid ask which have five best bid and ask data.
* For level 1 bid ask -> bidask_data.ask return with a list of tuples, containing ```[ ( ask, ask_qnty )]``` in the format, This has total length of one.
* For level 2 bid ask -> bidask_data.ask return with a list of tuples, containing ```[ ( ask_1, ask_1_qnty, ask_1_no_of_trades ) , ( ask_2, ask_2_qnty, ask_2_no_of_trades ) , ....... ]``` in the format , This has total length of five in bid and also same as the ask.
----

**QUICK START LIVE DATA CODE**

All code snippet can be found [here](https://github.com/Nahas-N/Truedata-sample-code).


----
**CONVERTING REAL TIME STREAM TO DICT**

if you need to convert the stream data to dict, use the dunder __dict__ method.

```python
td_obj.live_data[symbol_1].to_dict()

tick_data.to_dict()
```

----
# Option Chain Streaming
It is possible to stream single or multiple option chains.

```symbols , strike , type , ltp , ltt , ltq , volume , price_change , price_change_perc, oi , prev_oi , oi_change , oi_change_perc , bid ,bid_qty , ask , ask_qty , iv, delta, theta, gamma, vega, rho``` 

**Starting Option Chain data for a symbol**
```python
nifty_chain = td_obj.start_option_chain( 'NIFTY' , dt(2021 , 8 , 26) )
sensex_chain = td_obj.start_option_chain("SENSEX" , dt(2023 , 9 , 1) , chain_length = 80 )
#enabling option chain for NIFTY with corresponding expiry. 
```
* start_option_chain function takes following arguments:
  * symbols for egs: NIFTY , BANKNIFTY , SBIN etc......
  * expiry : date (datetime object) 
  * chain_length : number of strike need to pull with respect to future prices. default value is 10 (int)
  * bid_ask : enable live quote . default value is false (boolean)
  * greek : boolean, default value is false if need to stream greeks along with option chain

**Pulling an Option Chain**
```python
df = nifty_chain.get_option_chain()
#returns a dataframe that contain option chain for repective symbol
```
this get_option_chain function can call anywhere  that will return respective option chain

**Stop Option Chain Updates**
```python
nifty_chain.stop_option_chain()
```

**An example for pulling option chain and live data simultaneously**

code snippet can be found [here](https://github.com/Nahas-N/Truedata-sample-code).


----
# Historical Data
>Historical Data is provided over REST 

In version v6 we have seperated live and history as modules. so please import according to your configuration.

All historical success call return pandas dataframe. 

importing historical module

```python
from truedata import TD_hist
import logging
td_hist = TD_hist(username ,password , log_level= logging.WARNING  )
```
all these are the available methods in historical module.

```python
td_hist.get_historic_data('BANKNIFTY-I')
td_hist.get_historic_data('BANKNIFTY-I', duration='3 D')
td_hist.get_historic_data('BANKNIFTY-I', bar_size='30 mins')
td_hist.get_historic_data('BANKNIFTY-I', start_time=datetime.now()-relativedelta(days=3))
td_hist.get_historic_data('BANKNIFTY-I', end_time=datetime(2023, 11, 17, 12, 30))
td_hist.get_historic_data("BANKNIFTY-I", duration='2 D', bar_size='ticks', bidask=True , delivery = True)
td_hist.get_n_historical_bars('BANKNIFTY-I', no_of_bars=30, bar_size= '5 min')
td_hist.get_n_historical_bars("BANKNIFTY-I", no_of_bars=10, bar_size= 'ticks')
td_hist.get_gainers("NSEEQ", topn = 25 )
td_hist.get_losers("NSEEQ", topn = 25 )
td_hist.get_bhavcopy('EQ' , date=datetime(2023, 11, 16) )
td_hist.get_bhavcopy('FO')
result = td_hist.get_historic_data("NIFTY BANK", duration='1 D', bar_size='tick')
# print(result.to_dict(orient = 'records'))
```
**IMPORTANT NOTE:**

* all method returns pandas dataframe if want to convert to list of dict please use pandas inherent method  `result.to_dict(orient = 'records')`

Now that we have covered the basic parameters, you can mix and match the parameters as you please. If a parameter is not specified, the defaults are as follows
```python
end_time = datetime.now()
duration = "1 D"
bar_size = "1 min"
```
**Get Bhavcopy**

This function enables you to get the NSE & MCX bhavcopies for the day / date. 
```python
eq_bhav     = td_obj.get_bhavcopy('EQ')
fo_bhav     = td_obj.get_bhavcopy('FO')
mcx_bhav    = td_obj.get_bhavcopy('MCX')
```
The request checks if the latest completed bhavcopy has arrived for that segment and, if arrived, it returns the data. 

In case it has not arrived it provides the date and time of the last bhavcopy available which can also be pulled by providing the bhavcopy date.

**Limitations and caveats for historical data**

1) If you provide both duration and start time, duration will be used and start time will be ignored.
2) If you provide neither duration nor start time, duration = "1 D" will be used
3) If you do not provide the bar size bar_size = "1 min" will be used
4) The following BAR_SIZES are available:

    - tick
    - 1 min
    - 2 mins
    - 3 mins
    - 5 mins
    - 10 mins
    - 15 mins
    - 30 mins
    - 60 mins
    - eod (or EOD)
    - week (or WEEK)
    - month (or MONTH)
   

5) The following annotation can be used for DURATION:-

    - D = Days
    - W = Weeks
    - M = Months
    - Y = Years

**Get Gainers losers information**

This function enables you to get the NSE gainers losers information at present state. 
```python
gainers = td_obj.get_gainers(segment = "NSEEQ" , topn= 10 )
losers = td_obj.get_losers(segment = "NSEEQ" , topn= 10 )
```
* gainers , losers function takes following arguments:
  * segment (string) : NSEEQ, NSEFUT, NSEOPT, MCX
  * topn (int) : default 10 , top n number  

----
# Release Notes
Version 7.0.1
*   bugfix for the compression

Version 7.0.0
*   all websocket messages are compressed for performance improvments by default.

Version 6.0.7
*   bug fix and performance improved  

Version 6.0.5
*   added compression in analytical calls for faster perfomance 

Version 6.0.4
*   removing automatic reconnection after manual disconnection 

Version 6.0.3
*   lz4 version released to latest one without prebuild

Version 6.0.2
*   analytics new methods added 

Version 6.0.0
*  change: TD_live and TD_hist are two new modules under the package of truedata
*  change: option chain are coming with greeks
*  change: all historical calls returns pandas dataframe 
*  change: while loop methods are deprecated only efficient call back method is available now.  
*  change: TD_analytics module is added to package

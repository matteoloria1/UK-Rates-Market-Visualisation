# UK-Rates-Market-Visualisation
The aim of this code is to visualise the UK interest rate markets. After visualising UK-specific interest rate products, I go on to visualise the performance of Gilts vs Bunds and US Treasuries. Finally, I visualise the z-scores of these products. The caveat here is that Refinitiv Eikon only gives me access to the latest 3 months of asset data, meaning that the lookback period for z-score computation is not as long as I'd like it to be.
In the future, I hope to apply this visualisation code to EGBs and delve deeper into the Rates markets.

## Import Libraries
```
import eikon as ek
import pandas as pd
import numpy as np
from datetime import datetime
from datetime import date
```
## Access API
```
ek.set_app_key('')
```
## Read in Data
Here are examples of different datasets I read.
### UK Cash Yield Curve
```
uk_2y = ek.get_timeseries('GB2YT=RR')['OPEN']
uk_3y = ek.get_timeseries('GB3YT=RR')['OPEN']
uk_5y = ek.get_timeseries('GB5YT=RR')['OPEN']
uk_7y = ek.get_timeseries('GB7YT=RR')['OPEN']
uk_10y = ek.get_timeseries('GB10YT=RR')['OPEN']
uk_20y = ek.get_timeseries('GB20YT=RR')['OPEN']
uk_30y = ek.get_timeseries('GB30YT=RR')['OPEN']
uk_50y = ek.get_timeseries('GB50YT=RR')['OPEN']
```
### UK Cash Yield Curve Spreads
```
UK_2s5s = ek.get_timeseries('GB2GB5=RR')['CLOSE']
UK_2s10s = ek.get_timeseries('GB2GB10=RR')['CLOSE']
UK_2s30s = ek.get_timeseries('GB2GB30=RR')['CLOSE']
UK_5s10s = ek.get_timeseries('GB5GB10=RR')['CLOSE']
UK_5s30s = ek.get_timeseries('GB5GB30=RR')['CLOSE']
UK_10s30s = ek.get_timeseries('GB10GB30=RR')['CLOSE']
```

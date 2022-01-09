### UK-Rates-Market-Visualisation
The aim of this code is to visualise the UK interest rate markets. After visualising UK-specific interest rate products, I go on to visualise the performance of Gilts vs Bunds and US Treasuries. Finally, I visualise the z-scores of these products. The caveat here is that Refinitiv Eikon only gives me access to the latest 3 months of asset data, meaning that the lookback period for z-score computation is not as long as I'd like it to be.
In the future, I hope to apply this visualisation code to EGBs and delve deeper into the Rates markets.

##Import Libraries
import eikon as ek
import pandas as pd
import numpy as np
from datetime import datetime
from datetime import date

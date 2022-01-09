# UK-Rates-Market-Visualisation
The aim of this code is to visualise the UK interest rate markets. After visualising UK-specific interest rate products, I go on to visualise the performance of Gilts vs Bunds and US Treasuries. Finally, I visualise the z-scores of these products. The caveat here is that Refinitiv Eikon only gives me access to the latest 3 months of asset data, meaning that the lookback period for z-score computation is not as long as I'd like it to be.
In the future, I hope to apply this visualisation code to EGBs and delve deeper into the Rates markets.

This file will only show certain snippets of the code. Full code is in the "Code" file.

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
## Step 1: Read in Data
Here are examples of different datasets I read.
### UK Cash Bonds
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
### UK Cash Spreads
```
UK_2s5s = ek.get_timeseries('GB2GB5=RR')['CLOSE']
UK_2s10s = ek.get_timeseries('GB2GB10=RR')['CLOSE']
UK_2s30s = ek.get_timeseries('GB2GB30=RR')['CLOSE']
UK_5s10s = ek.get_timeseries('GB5GB10=RR')['CLOSE']
UK_5s30s = ek.get_timeseries('GB5GB30=RR')['CLOSE']
UK_10s30s = ek.get_timeseries('GB10GB30=RR')['CLOSE']
```
## Step 2: Clean and Manipulate Data
### UK Cash Bonds
```
# CONSTRUCT YIELD CURVE
# CREATE DATAFRAME FOR YIELD CURVE
UK_yieldcurve = pd.DataFrame([uk_2y, uk_3y, uk_5y, uk_10y, uk_20y, uk_30y, uk_50y]).transpose()

# RENAME COLUMNS
UK_yieldcurve.columns = ['2y', '3y', '5y', '10y', '20y', '30y', '50y']

UK_yieldcurve = UK_yieldcurve.fillna(method='ffill')


# GET HISTORICAL YIELD CURVE
yieldcurve_today = UK_yieldcurve.tail(1).transpose()
yieldcurve_1w = UK_yieldcurve.tail(5).head(1).transpose()
yieldcurve_1m = UK_yieldcurve.tail(20).head(1).transpose()
yieldcurve_2m = UK_yieldcurve.tail(40).head(1).transpose()
```
## Step 3: Set up PDF (to save the visualisation in a document)
```
import matplotlib.pyplot as plt
from matplotlib.backends.backend_pdf import PdfPages
import seaborn as sns
sns.set()
```
```
pdf = PdfPages('UK Rates Visualisation')
firstPage = plt.figure(figsize=(6,4))
firstPage.clf()
txt = "UK Market: " + str(date.today())
firstPage.text(0.5,0.5,txt, transform=firstPage.transFigure, size=24, ha="center")
pdf.savefig()
```
## Step 4: Visualise Data
### Cash Yield Curve
```
yieldcurve_vis = pd.concat([yieldcurve_today, yieldcurve_1m, yieldcurve_2m], axis=1)
yieldcurve_vis.columns = ['Today', '1 month ago', '2 months ago']

yieldcurve_vis.plot(style={'Today': 'ro-', '1 month ago': 'bx--', '2 months ago': 'g*:'}
        ,title='UK Nominal Yield Curve, %')
pdf.savefig(dpi=300, bbox_inches='tight')
```
![Screenshot 2022-01-09 at 22 23 17](https://user-images.githubusercontent.com/92649463/148703570-a95e5197-1460-4c76-a8f6-0019171c458b.png)

```
# BAR CHART OF ABSOLUTE CHANGES

# 1M CHANGE
yieldcurve_vis['Change (bps)'] = (yieldcurve_vis.iloc[:,0] - yieldcurve_vis.iloc[:,1]) * 100
indexNamesArr = yieldcurve_vis.index.values
listOfRowIndexLabels = list(indexNamesArr)
y_pos = np.arange(len(listOfRowIndexLabels))
plt.xticks(y_pos, listOfRowIndexLabels)
plt.xlabel("Maturity")
plt.ylabel("Change (bps)")
plt.title("1 Month Yield Curve Change (bps)")
plt.xticks(rotation=30)
plt.bar(y_pos, yieldcurve_vis['Change (bps)'])
plt.plot()

# 1W CHANGE
df_week_ago = pd.concat([yieldcurve_today, yieldcurve_1w], axis=1)
df_week_ago['Change (bps)'] = (df_week_ago.iloc[:,0] - df_week_ago.iloc[:,1]) * 100
indexNamesArr = df_week_ago.index.values
listOfRowIndexLabels = list(indexNamesArr)
y_pos = np.arange(len(listOfRowIndexLabels))
plt.xticks(y_pos, listOfRowIndexLabels)
plt.xlabel("Maturity")
plt.ylabel("Change (bps)")
plt.title("1 Week vs 1 Month Yield Curve Change (bps)")
plt.xticks(rotation=30)
plt.bar(y_pos, df_week_ago['Change (bps)'])
plt.plot()
pdf.savefig(dpi=300, bbox_inches='tight')
```
![Screenshot 2022-01-09 at 22 23 36](https://user-images.githubusercontent.com/92649463/148703602-8ae2d3fc-0bbb-451d-99b2-32a8b480c09c.png)

### Cash Spreads
```
# MULTIPLE LINE PLOTS
plt.plot(UK_2s5s.index, UK_2s5s,marker='', color='blue', linewidth=2, label="2s5s")
plt.plot(UK_2s10s.index, UK_2s10s, marker='', color='olive', linewidth=2, label="2s10s")
plt.plot(UK_2s30s.index, UK_2s30s, marker='', color='red', linewidth=2, label="2s30s")
plt.plot(UK_5s10s.index, UK_5s10s, marker='', color='orange', linewidth=2, label="5s10s")
plt.plot(UK_5s30s.index, UK_5s30s, marker='', color='yellow', linewidth=2, label="5s30s")
plt.plot(UK_10s30s.index, UK_10s30s, marker='', color='black', linewidth=2, label="10s30s")

# SHOW LEGEND
plt.legend(loc='center left', bbox_to_anchor=(1, 0.5))

plt.title('UK Cash Curve Spreads, bps')
plt.xticks(rotation=30)

# SHOW GRAPH
pdf.savefig(dpi=300, bbox_inches='tight')
plt.show()
```
![Screenshot 2022-01-09 at 22 23 58](https://user-images.githubusercontent.com/92649463/148703631-3192f857-8433-46f5-997f-f71a3ee2e160.png)

### Cash Yield Curve Z-Score Matrix
Quick note: we ignore the left-side of the diagonal. We only want to know the values to the right of the diagonal.
```
#COMPOSING MATRIX
tenors = [2,3,5,10,20,30,50]

df_spread = pd.DataFrame() 
df_spread['Tenors'] = tenors
for t in tenors: 
    df_spread[t] = [None]*len(tenors) 
df_spread = df_spread.set_index(df_spread['Tenors'], inplace=False, drop=True)

#print(df_spread)
for i in tenors: 
    for j in tenors: 
        #print(j, i)
        sspread = UK_yieldcurve[str(j)+'y'] - UK_yieldcurve[str(i)+'y'] 
        #print(sspread)
        #print(sspread.dtype)
        x = sspread[-1]
        mean = sspread[-31:-1].mean()#spot fix for rolling data
        sigma = sspread[-31:-1].std(ddof=1)

        z = (x-mean)/sigma
        #print(z)
        #print(x, mean, sigma)

        #appointt to "slot" 
        try:
            df_spread[j][i] = round(z,5)
        except:
            pass

#j - i and col = j ; rows = i 
df_spread = df_spread.drop(columns='Tenors')
df_spread = df_spread.fillna(0)
#print(df_spread)

plt.figure()
ax = sns.heatmap(df_spread, cmap='PiYG', annot=True)
ax.set_xlabel("Tenor (J)")
ax.set_ylabel("Tenor (I)")
ax.set_title('UK Cash 1 Month Rolling IsJs Spread Z-Score')

pdf.savefig(dpi=300, bbox_inches='tight')
```
![Screenshot 2022-01-09 at 22 24 18](https://user-images.githubusercontent.com/92649463/148703645-05fda272-ff83-4e48-951a-fb46654da8e6.png)

## Step 5: Z-Score Visualisation
```
import scipy.stats as stats
```
### Cash Yield Curve
```
uk_yieldcurve_zscore = UK_yieldcurve.apply(stats.zscore)
```
```
plt.plot(uk_yieldcurve_zscore.index, uk_yieldcurve_zscore['2y'],marker='', color='blue', linewidth=2, label="2y")
plt.plot(uk_yieldcurve_zscore.index, uk_yieldcurve_zscore['3y'],marker='', color='orange', linewidth=2, label="3y")
plt.plot(uk_yieldcurve_zscore.index, uk_yieldcurve_zscore['5y'],marker='', color='purple', linewidth=2, label="5y")
plt.plot(uk_yieldcurve_zscore.index, uk_yieldcurve_zscore['10y'],marker='', color='pink', linewidth=2, label="10y")
plt.plot(uk_yieldcurve_zscore.index, uk_yieldcurve_zscore['20y'],marker='', color='green', linewidth=2, label="20y")
plt.plot(uk_yieldcurve_zscore.index, uk_yieldcurve_zscore['30y'],marker='', color='black', linewidth=2, label="30y")
plt.plot(uk_yieldcurve_zscore.index, uk_yieldcurve_zscore['50y'],marker='', color='pink', linewidth=2, label="50y")

# SHOW LEGEND
plt.legend(loc='center left', bbox_to_anchor=(1, 0.5))

plt.title('Gilts Z-score')
plt.xticks(rotation=30)

# SHOW GRAPH
pdf.savefig(dpi=300, bbox_inches='tight')
plt.show()
```
![Screenshot 2022-01-09 at 22 24 42](https://user-images.githubusercontent.com/92649463/148703666-2ab54b2c-2528-48ab-b0d7-77c5d0cab815.png)

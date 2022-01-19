# UK-Rates-Market-Visualisation
The aim of this code is to visualise the UK interest rate markets. After visualising UK-specific interest rate products, I go on to visualise the performance of Gilts vs Bunds and US Treasuries. Finally, I visualise the z-scores of these products. The caveat here is that Refinitiv Eikon only gives me access to the latest 3 months of asset data, meaning that the lookback period for z-score computation is not as long as I'd like it to be.
In the future, I hope to apply this visualisation code to EGBs and delve deeper into the Rates markets.

This file will only show certain snippets of the code. Full code is in the "Code" file.

The final PDF looks like this:
[UK Rates Visualisation.pdf](https://github.com/matteoloria1/UK-Rates-Market-Visualisation/files/7882805/UK.Rates.Visualisation.PDF.pdf)

### Cash Yield Curve
```
![Screenshot 2022-01-09 at 22 23 17](https://user-images.githubusercontent.com/92649463/148703570-a95e5197-1460-4c76-a8f6-0019171c458b.png)

```
# BAR CHART OF ABSOLUTE CHANGES
```
![Screenshot 2022-01-09 at 22 23 36](https://user-images.githubusercontent.com/92649463/148703602-8ae2d3fc-0bbb-451d-99b2-32a8b480c09c.png)

### Cash Spreads
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

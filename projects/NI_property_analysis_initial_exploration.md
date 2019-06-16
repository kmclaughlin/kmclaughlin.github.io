
# Exploring the Data

Lets talk a little about the data columns before we dive any deeper, so we know what we are working with. Most are self explanatory but 'Capital Value Non-Exempt' and 'Capital Value Exempt' are a little less so. These are the value ofthe properties that are non-exempt and exempt for tax respectively. So all the non-exempt will be domestic houses and exempt will be churches, church halls etc. Let's rename those to 'Value' and 'Exempt Value' for easier reading


```python
properties_df = properties_df.rename(index=str, columns={"Capital Value Non-Exempt": "Value", "Capital Value Exempt": "Exempt Value"})
```

Let's look at the 2 most interesting columns, value and property size. We want to just look at domestic properties so filter for only those with an exempt value of 0.


```python
import matplotlib.pyplot as plt
import seaborn as sns

value_rows = (properties_df['Exempt Value'] == 0)
size_rows = value_rows & ~properties_df['Property Size'].isnull() & properties_df['Property Size'] > 0

f, axes = plt.subplots(1, 2, figsize=(14, 5))
sns.boxplot(y= properties_df[value_rows]['Value'], ax=axes[0])
sns.boxplot(y= properties_df[size_rows]['Property Size'], ax=axes[1])
```




    <matplotlib.axes._subplots.AxesSubplot at 0x2095134c470>




![png](images/output_40_1.png)


Unsurprisingly therre are some outliers. The value outliers seem reasonable, I can imagine there is a house in Northern Ireland valued at £3,000,000 (though I wonder if they know they are paying the highest rates in the country?). I have difficulty imagining on the other hand that there are any properties in Northern Ireland that are 22 km<sup>2</sup>. We won't overwrite this data as it might be interesting to look at at some point but instead we can filter it out. Also lets use violin plots instead of box plots so we can see the shape of the data better with the probability density.


```python

value_rows = (properties_df['Exempt Value'] == 0) & (properties_df['Value'] < 1000000)
size_rows = value_rows & (~properties_df['Property Size'].isnull()) & (properties_df['Property Size'] > 0)
size_rows = size_rows & (properties_df['Property Size'] < 1000)

f, axes = plt.subplots(1, 2, figsize=(14, 5))
sns.violinplot(y= properties_df[value_rows]['Value'], ax=axes[0])
sns.violinplot(y= properties_df[size_rows]['Property Size'], ax=axes[1])
```




    <matplotlib.axes._subplots.AxesSubplot at 0x20952a0b128>




![png](images/output_42_1.png)


These plots are quite interesting but nothing too unexpected. As expeted the vast majority of properties are in a relatively small range of values and sizes with a very long thing tail to the super expensive, very large properties that are very rare.

One insteresting detail is the blob on the bottom of the Value distribution. Let's look at the description of those very cheap properties to see what they are.


```python
properties_df[properties_df['Value'] < 2000]['Description'].unique()
```




    array(['Outbuilding', 'O/Closed/', 'Store', 'House (Caravan)', 'O.',
           'Outbuildings', 'Lock-up Garage', 'O & Y', 'Outbuilding Yard',
           'Caravan', 'Garage', 'House (Mobile)', 'H Caravan Ag', 'Stores',
           'Outbuilding (Closed)', 'Boat House', 'Alms Houses'], dtype=object)



It looks like that cluster of cheaper properties are outbuildings, caravans, garages etc which seems reasonable.
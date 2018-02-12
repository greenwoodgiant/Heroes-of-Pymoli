
# Heroes of Pymoli Data Anaysis

The overwhelming majority users are male, and the overwhelming majority of purchases comes from users aged 19-26, so it would be wise to market heavily towards the college/young-adult male crowd.

While the Top 5 Spenders bought at least three items, a majority bought only one. Introducing new items, or the increasing their relative power boost in-game, could encourage more purchases from the one-time shoppers

While the Top 5 Sellers are mostly under three dollars, the Top 5 Most Profitable items include three over four dollars. It would be wise to continue offering a selection of high-cost/high-value items for those who will pay, along with a large selection of low-cost items to suit the rest.

I noticed that some of the top-grossing items had been sold at lower prices, which led me to taking the average price sold at instead of using the max to simply return what I assumed would be the same value down the column. My comment about this would be it might be better to maintain exclusivity of high-cost items by not offering “sales” and instead introduce another, less-powerful option. If the people willing to pay for the premium items feel like they didn’t actually need to, they might be discouraged from future premium releases. 



```python
# DEPENDENCIES
import pandas as pd
import numpy as np
import os
```


```python
# Call the JSON
pymoli = pd.read_json("purchase_data.json")
```

# Player Count


```python
# Find the Total Number of Players from the length of the unique SN list
total_players = len(pymoli["SN"].unique())
total_players_df = pd.DataFrame([{"Total Players":total_players}])
total_players_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis (Total)


```python
# Find the Total Unique Items, Avg Price, Total Purchases, and Total Revenue

total_purch = pymoli["Price"].count()
unique_items = len(pymoli["Item Name"].unique())
avg_price = round(pymoli["Price"].mean(),2)
total_rev = round(float(pymoli["Price"].sum()),2)

summary_df = pd.DataFrame([{"Total Purchases":total_purch,
                            "Unique Items":unique_items,
                            "Average Price":avg_price,
                            "Total Revenue":total_rev}])

summary_df = summary_df[['Unique Items','Average Price','Total Purchases','Total Revenue']]
summary_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unique Items</th>
      <th>Average Price</th>
      <th>Total Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>179</td>
      <td>2.93</td>
      <td>780</td>
      <td>2286.33</td>
    </tr>
  </tbody>
</table>
</div>



# Gender Demographics


```python
# Drop duplicate SN entries to get unique players
unique_players = pymoli.drop_duplicates(["SN"],keep="first")

# Create df with the totals per gender, rename column
gender_count = pd.DataFrame(unique_players["Gender"].value_counts())
gender_count.columns = ["Total Count"]

# Set total users, Add column for percentage
total_users = gender_count["Total Count"].sum()
gender_count["Percentage"] = [round((x/total_users)*100) for x in gender_count["Total Count"]]

gender_count
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Count</th>
      <th>Percentage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>465</td>
      <td>81.0</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>100</td>
      <td>17.0</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>8</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis (Gender)


```python
# Find Purchase Count by Gender
male_purchases = pymoli.loc[pymoli["Gender"]=="Male",["Price"]]
female_purchases = pymoli.loc[pymoli["Gender"]=="Female",["Price"]]
other_purchases = pymoli.loc[pymoli["Gender"]=="Other / Non-Disclosed",["Price"]]

# Set the values for Total, Average, and Count of purchases per gender value
total_m_purchase = round(float(male_purchases.sum()),2)
total_f_purchase = round(float(female_purchases.sum()),2)
total_o_purchase = round(float(other_purchases.sum()),2)

avg_m_purchase = round(float(male_purchases.mean()),2)
avg_f_purchase = round(float(female_purchases.mean()),2)
avg_o_purchase = round(float(other_purchases.mean()),2)

num_m_purchase = round(int(male_purchases.count()),2)
num_f_purchase = round(int(female_purchases.count()),2)
num_o_purchase = round(int(other_purchases.count()),2)

# Copy the previous dataframe to retain formatting
gender_summary = gender_count

# Create new columns based on Purchase Count, Avg Purchase, and Total Value
gender_summary["Purchase Count"] = [num_m_purchase,num_f_purchase,num_o_purchase]
gender_summary["Average Purchase"] = [avg_m_purchase,avg_f_purchase,avg_o_purchase]
gender_summary["Total Value"] = [total_m_purchase,total_f_purchase,total_o_purchase]

# Create a Normalized Value column
# (This doesn't appear to be the same type of value as in the example, but I don't know any other meaning for "normalize" other than to make them equal 1.)
total_value = gender_summary["Total Value"].sum()
gender_summary["Normalized Total"] = [round((x/total_value),2) for x in gender_summary["Total Value"]]

# Remove columns from previous exercise
gender_summary = gender_summary[["Purchase Count","Average Purchase","Total Value","Normalized Total"]]
gender_summary
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase</th>
      <th>Total Value</th>
      <th>Normalized Total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>2.95</td>
      <td>1867.68</td>
      <td>0.82</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>2.82</td>
      <td>382.91</td>
      <td>0.17</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>3.25</td>
      <td>35.74</td>
      <td>0.02</td>
    </tr>
  </tbody>
</table>
</div>



# Age Demographics


```python
# Identify the lowest and highest age in the df
low = pymoli["Age"].min()
high = pymoli["Age"].max()

# Create a list from low to high in intervals of 4
age_bins = np.arange((low-1),high,4)

# Create a list of labels to associate with the bins
bin_labels = ["10 & Under","11-14","15-18","19-22","23-26","27-30","31-34","35-38","39+"]

# Create new column for bins
pymoli["Age Group"] = pd.cut(pymoli["Age"],age_bins,labels=bin_labels)

# Create GroupBy object
age_binned = pymoli.groupby(["Age Group"])

# Create a dataframe from the count of the Age column
age_binned_df = pd.DataFrame(age_binned["Age"].count())

# Rename Total column and Add Percentage column
age_binned_df.columns = ['Total Players']
age_binned_df["Percentage"] = [round(((x/total_players)*100),2) for x in age_binned_df["Total Players"]]
age_binned_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Players</th>
      <th>Percentage</th>
    </tr>
    <tr>
      <th>Age Group</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10 &amp; Under</th>
      <td>32</td>
      <td>5.58</td>
    </tr>
    <tr>
      <th>11-14</th>
      <td>31</td>
      <td>5.41</td>
    </tr>
    <tr>
      <th>15-18</th>
      <td>111</td>
      <td>19.37</td>
    </tr>
    <tr>
      <th>19-22</th>
      <td>231</td>
      <td>40.31</td>
    </tr>
    <tr>
      <th>23-26</th>
      <td>207</td>
      <td>36.13</td>
    </tr>
    <tr>
      <th>27-30</th>
      <td>63</td>
      <td>10.99</td>
    </tr>
    <tr>
      <th>31-34</th>
      <td>46</td>
      <td>8.03</td>
    </tr>
    <tr>
      <th>35-38</th>
      <td>37</td>
      <td>6.46</td>
    </tr>
    <tr>
      <th>39+</th>
      <td>20</td>
      <td>3.49</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis (Age)


```python
# Find Total Purchase Count by Age Group
count_binned = pd.DataFrame(age_binned["Price"].count())
count_binned.columns = ["Purchase Count"]
count_binned.reset_index(inplace=True)

# Find Average Purchase Price by Age Group
average_binned = pd.DataFrame(round(age_binned["Price"].mean(),2))
average_binned.columns = ["Average Purchase"]
average_binned.reset_index(inplace=True)

# Find Total Amount spent by Age Group
sum_binned = pd.DataFrame(round(age_binned["Price"].sum(),2))
sum_binned.columns = ['Total Spent']
sum_binned.reset_index(inplace=True)

# Merge dfs
merged_bins = pd.merge(count_binned,average_binned,on="Age Group")
merged_bins = pd.merge(merged_bins,sum_binned,on="Age Group")

# Add a Normalized Total column
total_sales = total_m_purchase + total_f_purchase + total_o_purchase
merged_bins["Normalized Total"] = [round((x/total_sales),2) for x in merged_bins["Total Spent"]]

merged_bins
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age Group</th>
      <th>Purchase Count</th>
      <th>Average Purchase</th>
      <th>Total Spent</th>
      <th>Normalized Total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10 &amp; Under</td>
      <td>32</td>
      <td>3.02</td>
      <td>96.62</td>
      <td>0.04</td>
    </tr>
    <tr>
      <th>1</th>
      <td>11-14</td>
      <td>31</td>
      <td>2.70</td>
      <td>83.79</td>
      <td>0.04</td>
    </tr>
    <tr>
      <th>2</th>
      <td>15-18</td>
      <td>111</td>
      <td>2.88</td>
      <td>319.32</td>
      <td>0.14</td>
    </tr>
    <tr>
      <th>3</th>
      <td>19-22</td>
      <td>231</td>
      <td>2.93</td>
      <td>676.20</td>
      <td>0.30</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23-26</td>
      <td>207</td>
      <td>2.94</td>
      <td>608.02</td>
      <td>0.27</td>
    </tr>
    <tr>
      <th>5</th>
      <td>27-30</td>
      <td>63</td>
      <td>2.98</td>
      <td>187.99</td>
      <td>0.08</td>
    </tr>
    <tr>
      <th>6</th>
      <td>31-34</td>
      <td>46</td>
      <td>3.07</td>
      <td>141.24</td>
      <td>0.06</td>
    </tr>
    <tr>
      <th>7</th>
      <td>35-38</td>
      <td>37</td>
      <td>2.81</td>
      <td>104.06</td>
      <td>0.05</td>
    </tr>
    <tr>
      <th>8</th>
      <td>39+</td>
      <td>20</td>
      <td>3.13</td>
      <td>62.56</td>
      <td>0.03</td>
    </tr>
  </tbody>
</table>
</div>



# Top Spenders


```python
# Group by Screenname 
by_sn = pymoli.groupby(["SN"])

# Find the sum of the price column per screenname
user_spent = pd.DataFrame(by_sn["Price"].sum())
user_count = pd.DataFrame(by_sn["SN"].count())

user_spent.columns = ["Total Spent"]
user_count.columns = ["Total Purchases"]

user_spent.reset_index(inplace=True)
user_count.reset_index(inplace=True)

# Merge the two dfs together
top_five = pd.merge(user_spent,user_count,on="SN")

# Sort the values by Total Spent
top_five = top_five.sort_values("Total Spent",ascending=False)

# Cut the list to the top five
top_five = top_five.head()

# Add a column for Average Purchase
top_five["Average Purchase"] = round((top_five["Total Spent"] / top_five["Total Purchases"]),2)

# Reorder columns, reset index
top5_spenders = top_five[["SN","Total Purchases","Average Purchase","Total Spent"]]
top5_spenders.reset_index(inplace=True,drop=True)

top5_spenders
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>SN</th>
      <th>Total Purchases</th>
      <th>Average Purchase</th>
      <th>Total Spent</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Undirrala66</td>
      <td>5</td>
      <td>3.41</td>
      <td>17.06</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Saedue76</td>
      <td>4</td>
      <td>3.39</td>
      <td>13.56</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Mindimnya67</td>
      <td>4</td>
      <td>3.18</td>
      <td>12.74</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Haellysu29</td>
      <td>3</td>
      <td>4.24</td>
      <td>12.73</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Eoda93</td>
      <td>3</td>
      <td>3.86</td>
      <td>11.58</td>
    </tr>
  </tbody>
</table>
</div>



# Most Popular Items


```python
# Group by Item Name
by_item = pymoli.groupby(["Item Name"])

# Create dfs with desired metrics to merge together
# Count by Item ID to get Number Sold
number_sold = pd.DataFrame(by_item["Item ID"].count())
number_sold.columns = ["Number Sold"]
number_sold.reset_index(inplace=True)

# Take the average of the price column for Item Price
# (Some items are sold at different prices)
item_price = pd.DataFrame(round(by_item["Price"].mean(),2))
item_price.columns = ["Price (Avg)"]
item_price.reset_index(inplace=True)

# Take the sum of the Price column for the total revenue per item
total_value = pd.DataFrame(by_item["Price"].sum())
total_value.columns = ["Total Value"]
total_value.reset_index(inplace=True)

# Take the max of the Item ID column to pull the Item ID
item_id = pd.DataFrame(by_item["Item ID"].max())
item_id.reset_index(inplace=True)

# Merge the dfs together
merged_byitem = pd.merge(number_sold,item_price,on="Item Name")
merged_byitem = pd.merge(merged_byitem,total_value,on="Item Name")
merged_byitem = pd.merge(merged_byitem,item_id,on="Item Name")

# Reorder columns
merged_byitem = merged_byitem[['Item ID', 'Item Name', 'Number Sold', 'Price (Avg)', 'Total Value']]

# Sort by Number Sold to find most popular items
top5_items = merged_byitem.sort_values("Number Sold",ascending=False)

# Cut to Top Five, reset index
top5_items = top5_items.head()
top5_items.reset_index(inplace=True,drop=True)
top5_items
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Number Sold</th>
      <th>Price (Avg)</th>
      <th>Total Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>101</td>
      <td>Final Critic</td>
      <td>14</td>
      <td>2.76</td>
      <td>38.60</td>
    </tr>
    <tr>
      <th>1</th>
      <td>84</td>
      <td>Arcane Gem</td>
      <td>11</td>
      <td>2.23</td>
      <td>24.53</td>
    </tr>
    <tr>
      <th>2</th>
      <td>39</td>
      <td>Betrayal, Whisper of Grieving Widows</td>
      <td>11</td>
      <td>2.35</td>
      <td>25.85</td>
    </tr>
    <tr>
      <th>3</th>
      <td>180</td>
      <td>Stormcaller</td>
      <td>10</td>
      <td>3.46</td>
      <td>34.65</td>
    </tr>
    <tr>
      <th>4</th>
      <td>175</td>
      <td>Woeful Adamantite Claymore</td>
      <td>9</td>
      <td>1.24</td>
      <td>11.16</td>
    </tr>
  </tbody>
</table>
</div>



# Most Profitable Items


```python
# Re-sort previous df by Total Value to get most profitable games
top5_profits = merged_byitem.sort_values("Total Value",ascending=False)

# Cut to Top Five
top5_profits = top5_profits.head()

top5_profits
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Number Sold</th>
      <th>Price (Avg)</th>
      <th>Total Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>56</th>
      <td>101</td>
      <td>Final Critic</td>
      <td>14</td>
      <td>2.76</td>
      <td>38.60</td>
    </tr>
    <tr>
      <th>112</th>
      <td>34</td>
      <td>Retribution Axe</td>
      <td>9</td>
      <td>4.14</td>
      <td>37.26</td>
    </tr>
    <tr>
      <th>137</th>
      <td>180</td>
      <td>Stormcaller</td>
      <td>10</td>
      <td>3.46</td>
      <td>34.65</td>
    </tr>
    <tr>
      <th>132</th>
      <td>115</td>
      <td>Spectral Diamond Doomblade</td>
      <td>7</td>
      <td>4.25</td>
      <td>29.75</td>
    </tr>
    <tr>
      <th>96</th>
      <td>32</td>
      <td>Orenmir</td>
      <td>6</td>
      <td>4.95</td>
      <td>29.70</td>
    </tr>
  </tbody>
</table>
</div>



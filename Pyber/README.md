

```python
#Colleen Karnas-Haines
#3/19/2018
#Uber Analysis
```


```python
#Observations:
#1. Rural areas, unsurprisingly, have the fewest drivers and 
#the lowest number of rides, but the prices tend to be higher.
#This may be due to the fact that driving distances may be farther or
#the low supply of drivers increases the demand and therefore cost of
#ride-sharing
#2.The most money made from ride-sharing is in urban areas. This is most likely
#due to the larger amount of rides, as the previous chart showed average
#prices tend to be higher in rural areas.
#3. The data is rather consistant when looking at total # of drivers, total 
# number of rides, and total money made from fares. Urban leads, Suburban
#follows, and Rural brings up the rear.
```


```python
#Dependencies
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import os
import math
import seaborn as sns
from scipy import stats

```


```python
#read in the two csv files
csvpath=os.path.join("raw_data","city_data.csv")
city_data=pd.read_csv(csvpath)

csvpath=os.path.join("raw_data","ride_data.csv")
ride_data=pd.read_csv(csvpath)
```


```python
#check the data
#drop cities that are duplicated
city_data.drop_duplicates(subset=["city"], keep='first', inplace=True)

#city_data.head()
#city_data.count()
```


```python
#Check the data
#ride_data.head()
#ride_data.count()
```


```python
total_data = pd.merge( ride_data,city_data, on="city", how="left")
#total_data.head()
```


```python
#Calculate the number of rides per city and make it a new column in the total_data set
rides=pd.DataFrame(total_data["city"].value_counts())
#total_data.head()
rides.reset_index(level=0, inplace=True)
rides=rides.rename(columns={"index": "city", "city": "num_rides"})
total_data=pd.merge(total_data,rides, on="city")
#total_data.head()
```


```python
#Calculate the average price of a ride in each city and make it a new column in the total_data set
average_fare=pd.DataFrame(total_data.groupby("city")["fare"].mean())
average_fare.reset_index(level=0, inplace=True)
average_fare=average_fare.rename(columns={"index": "city","fare":"avg_fare"})
total_data=pd.merge(total_data,average_fare, on="city")
#total_data.head()
#average_fare.head()
```


```python
#Create a data set that only has city summary data
summary_data=pd.DataFrame(total_data.groupby("city").mean())
summary_data.reset_index(level=0, inplace=True)
summary_data=summary_data.rename(columns={"index": "city"})
simple=total_data.filter(["city","type"], axis=1)
#simple.groupby("city").head()
simple=simple.drop_duplicates(subset="city", keep='first', inplace=False)
summary_data=pd.merge(summary_data,simple, on="city", how="inner")
#summary_data.head()

```


```python
#This looks great, but I cannot get the markers to change size. The instructions ask for a seaborn, but...
#sns.lmplot('avg_fare', 'driver_count', data=summary_data,scatter_kws={"s": 100},hue="type",fit_reg=False)
#plt.show()
```


```python
#Create a bubble chart using the city summary data
fig, ax = plt.subplots()
groups = summary_data.groupby('type')
colors = ["gold", "lightskyblue","lightcoral"]
for i, (name, group) in enumerate(groups):
    group.plot(kind='scatter', x="num_rides", y='avg_fare', edgecolors="black",alpha=.65, s=group['driver_count']*10, label=name, ax=ax, color=colors[i])
lgd = ax.legend(numpoints=1)
ax.grid(b=True, which='major')
for handle in lgd.legendHandles:
    handle.set_sizes([100.0])
ax.set_ylim((0, max(summary_data["avg_fare"]+5)))
ax.set_xlim((0, max(summary_data["num_rides"]+5)))
plt.xlabel("Total # of Rides per Cities")
plt.ylabel("Average Fare Price in $")
plt.title("Ride-sharing City Analysis\nBubble size indicates # of drivers")
#size relatef to number of drivers
 
plt.show()

```


![png](output_11_0.png)



```python
total_data.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>date</th>
      <th>fare</th>
      <th>ride_id</th>
      <th>driver_count</th>
      <th>type</th>
      <th>num_rides</th>
      <th>avg_fare</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Sarabury</td>
      <td>2016-01-16 13:49:27</td>
      <td>38.35</td>
      <td>5403689035038</td>
      <td>46</td>
      <td>Urban</td>
      <td>27</td>
      <td>23.49</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Sarabury</td>
      <td>2016-07-23 07:42:44</td>
      <td>21.76</td>
      <td>7546681945283</td>
      <td>46</td>
      <td>Urban</td>
      <td>27</td>
      <td>23.49</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Sarabury</td>
      <td>2016-04-02 04:32:25</td>
      <td>38.03</td>
      <td>4932495851866</td>
      <td>46</td>
      <td>Urban</td>
      <td>27</td>
      <td>23.49</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sarabury</td>
      <td>2016-06-23 05:03:41</td>
      <td>26.82</td>
      <td>6711035373406</td>
      <td>46</td>
      <td>Urban</td>
      <td>27</td>
      <td>23.49</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Sarabury</td>
      <td>2016-09-30 12:48:34</td>
      <td>30.30</td>
      <td>6388737278232</td>
      <td>46</td>
      <td>Urban</td>
      <td>27</td>
      <td>23.49</td>
    </tr>
  </tbody>
</table>
</div>




```python
compare_fare_Urban=total_data.loc[total_data["type"]=="Urban",["fare"]]
#compare_fare_Urban.head()
compare_fare_Rural=total_data.loc[total_data["type"]=="Rural",["fare"]]
#compare_fare_Rural.head()
compare_fare_Suburban=total_data.loc[total_data["type"]=="Suburban",["fare"]]
#compare_fare_Suburban.head()
compare_fare_Urban.mean()
```




    fare    24.663594
    dtype: float64




```python
(t_stat, p1) = stats.ttest_ind(compare_fare_Urban, compare_fare_Rural, equal_var=False)

# Report
print("The mean price of Urban rides is " +str(compare_fare_Urban.mean()))
print("The mean price of Rural is "+str(compare_fare_Rural.mean()))


print("p is {:0.22f}.".format(p1[0]))
#print(p)
if p1 < 0.05:
    print("The difference in sample means is significant.")
else:
    print("The difference in sample means is not significant.")
```

    The mean price of Urban rides is fare    24.663594
    dtype: float64
    The mean price of Rural is fare    34.04072
    dtype: float64
    p is 0.0000000000175310165167.
    The difference in sample means is significant.
    


```python
(t_stat, p2) = stats.ttest_ind(compare_fare_Urban, compare_fare_Suburban, equal_var=False)

# Report
print("The mean price of Urban rides is " +str(compare_fare_Urban.mean()))
print("The mean price of Rural is "+str(compare_fare_Suburban.mean()))


print("p is {:0.44f}.".format(p2[0]))
#print(p)
if p2 < 0.05:
    print("The difference in sample means is significant.")
else:
    print("The difference in sample means is not significant.")
```

    The mean price of Urban rides is fare    24.663594
    dtype: float64
    The mean price of Rural is fare    30.908608
    dtype: float64
    p is 0.00000000000000000000000000000010216896853130.
    The difference in sample means is significant.
    


```python
(t_stat, p3) = stats.ttest_ind(compare_fare_Rural, compare_fare_Suburban, equal_var=False)

# Report
print("The mean price of Urban rides is " +str(compare_fare_Rural.mean()))
print("The mean price of Rural is "+str(compare_fare_Suburban.mean()))


print("p is {:0.44f}.".format(p3[0]))
#print(p)
if p3 < 0.05:
    print("The difference in sample means is significant.")
else:
    print("The difference in sample means is not significant.")
```

    The mean price of Urban rides is fare    34.04072
    dtype: float64
    The mean price of Rural is fare    30.908608
    dtype: float64
    p is 0.01865978150071063990100839191654813475906849.
    The difference in sample means is significant.
    


```python
if (p1<0.05)&(p2<0.05)&(p3<0.05):
    print("All three prices are significantly different from each other")
```

    All three prices are significantly different from each other
    


```python
#Create a pie chart based on the ride data, fare price per city type
fare_type_data=pd.DataFrame(total_data.groupby("type").sum())
# Labels for the sections of our pie chart
labels = ["Rural", "Suburban", "Urban"]

# The values of each section of the pie chart
sizes = fare_type_data["fare"]

# The colors of each section of the pie chart
colors = ["gold", "lightskyblue", "lightcoral"]
explode=[0,0,0.1]

#fare_type_data
```


```python
# Creates the pie chart based upon the values above
# Automatically finds the percentages of each part of the pie chart
plt.pie(sizes, explode=explode, labels=labels, colors=colors,
        autopct="%1.1f%%", shadow=True, startangle=140)
# Tells matplotlib that we want a pie chart with equal axes
plt.axis("equal")
plt.title("Total Fare Prices by City Type")
plt.show()
```


![png](output_19_0.png)



```python
#Create a pie chart based on the ride data, rides by city type
numrides_type_data=pd.DataFrame(total_data.groupby("type").count())

# Labels for the sections of our pie chart
labels = ["Rural", "Suburban","Urban" ]

# The values of each section of the pie chart
sizes = numrides_type_data["num_rides"]

# The colors of each section of the pie chart
colors = ["gold", "lightskyblue", "lightcoral"]
explode=[0,0,0.1]

#numrides_type_data
```


```python
# Creates the pie chart based upon the values above
# Automatically finds the percentages of each part of the pie chart
plt.pie(sizes, explode=explode, labels=labels, colors=colors,
        autopct="%1.1f%%", shadow=True, startangle=140)
# Tells matplotlib that we want a pie chart with equal axes
plt.axis("equal")
plt.title("Percentage of Rides by City Type")
plt.show()
```


![png](output_21_0.png)



```python
#creates a pie chart based on the ride data, drivers by city type
driver_type_data=pd.DataFrame(total_data.groupby("type").count())

# Labels for the sections of our pie chart
labels = ["Rural", "Suburban","Urban" ]

# The values of each section of the pie chart
sizes = driver_type_data["driver_count"]

# The colors of each section of the pie chart
colors = ["gold", "lightskyblue", "lightcoral"]
explode=[0,0,0.1]

#numrides_type_data
```


```python
# Creates the pie chart based upon the values above
# Automatically finds the percentages of each part of the pie chart
plt.pie(sizes, explode=explode, labels=labels, colors=colors,
        autopct="%1.1f%%", shadow=True, startangle=140)
# Tells matplotlib that we want a pie chart with equal axes
plt.axis("equal")
plt.title("Percentage of Drivers by City Type")
plt.show()
```


![png](output_23_0.png)


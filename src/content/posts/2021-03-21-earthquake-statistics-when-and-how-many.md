---
template: blog-post
title: "Earthquake Statistics: When and How Many"
slug: /earthquakes
date: 2021-03-21 16:04
description: earthquakes, statistics
featuredImage: /assets/eq_map.png
---
Hundreds of earthquakes happen everyday, all over the Earth. The majority of these events are not felt by people and rarely cause any damage. But, the shaking of the ground doesn't go unnoticed. Earthquakes large and small are detected by number of instruments, including global and local arrays of seismographs recording in locations ranging from the tops of mountains to the bottom of the ocean.

Understanding when and where earthquakes occur is important for identifying locations where there is an increased risk from damaging earthquakes. Toward that effort, there have been various attempts to predict earthquakes, with limited success. We know that there are certain regions where damaging earthquakes occur more frequently, namely plate boundaries and especially convergent margins. While we might have a good idea of "where", it's a lot more complicated to determine "when".

Even if there aren't any obvious patterns when looking at the global occurrence of larger earthquakes, it is still interesting to explore the general statistics -- how many events occur each day, what are the year-to-year variations, are there any anomalous months or days, etc. We won't know whats in the data until we look. Fortunately, there's an abundance of earthquake data available from a variety of sources.

### Earthquake data

With global coverage using relatively sensitive instruments, it is possible to detect all events greater than about a magnitude 6 worldwide (more on [earthquake magnitude scales](https://earthquake.usgs.gov/learn/topics/measure.php)). Smaller events can be recorded by local networks and stations close to the event, but there are many small earthquakes that are too remote to be detected. For this analysis, I selected a lower magnitude limit of magnitude 6 so that my data set would have a complete record of all earthquakes at M6 and above.

The vast majority of the bigger earthquakes are now detected by the [The Global Seismographic Network (GSN)](https://www.iris.edu/hq/programs/gsn) which was formed in 1986. Prior to that, there were stations recording for many years as part of different networks or individually. 

Using data from 1985 to the end of 2020, let's take a look at all of the earthquakes greater than M6. The data is from the [USGS earthquake catalog](https://earthquake.usgs.gov/earthquakes/search/) with search parameters of M6 and greater worldwide, for the years 1985 to 2020.

```python
# Load in the csv file of earthquakes M6 and greater 

import pandas as pd
import numpy as np

# columns to keep
cols_keep = ['time', 'mag', 'magType', 'place']
df = pd.read_csv('/home/nicole/earthquakes/statistics/m6_1985_2020.csv', usecols=cols_keep)

df.head()
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

```
.dataframe tbody tr th {
    vertical-align: top;
}

.dataframe thead th {
    text-align: right;
}
```

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>time</th>
      <th>mag</th>
      <th>magType</th>
      <th>place</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2020-12-29T11:19:54.762Z</td>
      <td>6.4</td>
      <td>mww</td>
      <td>2 km WSW of Petrinja, Croatia</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020-12-27T21:39:14.770Z</td>
      <td>6.7</td>
      <td>mww</td>
      <td>146 km WNW of Corral, Chile</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-12-24T23:43:41.964Z</td>
      <td>6.3</td>
      <td>mww</td>
      <td>2 km ESE of Calatagan, Philippines</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-12-23T18:11:47.762Z</td>
      <td>6.1</td>
      <td>mww</td>
      <td>90 km SW of Angaur State, Palau</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020-12-20T17:23:22.668Z</td>
      <td>6.3</td>
      <td>mww</td>
      <td>100 km ENE of Hachinohe, Japan</td>
    </tr>
  </tbody>
</table>
</div>

We're only including columns of interest and events which are earthquakes and not explosion or other human-caused events. Let's explore some of the general statistics of these events.

### General statistics

```python
# Total number of earthquakes
print('Total number of events: ', len(df))
print('Average number per week:', round(len(df) / (52*(2020-1985+1)), 2))

# Largest magnitude
print('Largest magnitude earthquakes:')
df[['mag','time','place']].sort_values('mag',ascending=False).head()
```

```
Total number of events:  5463
Average number per week: 2.92
Largest magnitude earthquakes:
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

```
.dataframe tbody tr th {
    vertical-align: top;
}

.dataframe thead th {
    text-align: right;
}
```

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>mag</th>
      <th>time</th>
      <th>place</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2471</th>
      <td>9.1</td>
      <td>2004-12-26T00:58:53.450Z</td>
      <td>2004 Sumatra - Andaman Islands Earthquake</td>
    </tr>
    <tr>
      <th>1404</th>
      <td>9.1</td>
      <td>2011-03-11T05:46:24.120Z</td>
      <td>2011 Great Tohoku Earthquake, Japan</td>
    </tr>
    <tr>
      <th>1595</th>
      <td>8.8</td>
      <td>2010-02-27T06:34:11.530Z</td>
      <td>offshore Bio-Bio, Chile</td>
    </tr>
    <tr>
      <th>2404</th>
      <td>8.6</td>
      <td>2005-03-28T16:09:36.530Z</td>
      <td>northern Sumatra, Indonesia</td>
    </tr>
    <tr>
      <th>1200</th>
      <td>8.6</td>
      <td>2012-04-11T08:38:36.720Z</td>
      <td>off the west coast of northern Sumatra</td>
    </tr>
  </tbody>
</table>
</div>

Since 1985, there has been an average of almost three earthquakes each week globally with a magnitude M6 or greater. The largest earthquakes have all been relatively recent, between 2004 and 2012. As expected, these events occur at subducting plate boundaries and the five largest events are on the [Ring of Fire](https://en.wikipedia.org/wiki/Ring_of_Fire).

Separating the earthquakes into various time intervals, we can see if there are any patterns or other interesting features when looking at when earthquakes happen.

### Earthquakes: year, month, and day

```python
# Count the number of earthquakes over various time intervals

df['time'] = pd.to_datetime(df['time'])

# Add additional columns for day-of-year, year, month, and day
df['year'] = df['time'].dt.year
df['month'] = df['time'].dt.month
df['day'] = df['time'].dt.day
df['dofy'] = df['time'].apply(
    lambda x: x.strftime("%j")).astype(str).astype(int)

df.head()
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

```
.dataframe tbody tr th {
    vertical-align: top;
}

.dataframe thead th {
    text-align: right;
}
```

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>time</th>
      <th>mag</th>
      <th>magType</th>
      <th>place</th>
      <th>year</th>
      <th>month</th>
      <th>day</th>
      <th>dofy</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2020-12-29 11:19:54.762000+00:00</td>
      <td>6.4</td>
      <td>mww</td>
      <td>2 km WSW of Petrinja, Croatia</td>
      <td>2020</td>
      <td>12</td>
      <td>29</td>
      <td>364</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020-12-27 21:39:14.770000+00:00</td>
      <td>6.7</td>
      <td>mww</td>
      <td>146 km WNW of Corral, Chile</td>
      <td>2020</td>
      <td>12</td>
      <td>27</td>
      <td>362</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-12-24 23:43:41.964000+00:00</td>
      <td>6.3</td>
      <td>mww</td>
      <td>2 km ESE of Calatagan, Philippines</td>
      <td>2020</td>
      <td>12</td>
      <td>24</td>
      <td>359</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-12-23 18:11:47.762000+00:00</td>
      <td>6.1</td>
      <td>mww</td>
      <td>90 km SW of Angaur State, Palau</td>
      <td>2020</td>
      <td>12</td>
      <td>23</td>
      <td>358</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020-12-20 17:23:22.668000+00:00</td>
      <td>6.3</td>
      <td>mww</td>
      <td>100 km ENE of Hachinohe, Japan</td>
      <td>2020</td>
      <td>12</td>
      <td>20</td>
      <td>355</td>
    </tr>
  </tbody>
</table>
</div>

```python
# Find the number of earthquakes for each day, year, month, and day of the month. 
dofy_tot = df['dofy'].value_counts(sort=True)
dofy_tot.sort_index(axis=0, inplace=True)

year_tot = df['year'].value_counts(sort=True)
month_tot = df['month'].value_counts(sort=True)
day_tot = df['day'].value_counts(sort=True)

# Find the average number of events per year, day of year, and month
year_ave = year_tot.mean()
dofy_ave = dofy_tot.mean()
month_ave = month_tot.mean()
```

### Plotting time!

```python
# Import stuff for plotting
from matplotlib import pyplot as plt
%matplotlib inline

# Seaborn for plotting and styling
import seaborn as sns

# Settings for all figures
plt.rcParams['axes.labelsize'] = 14
plt.rcParams['axes.titlesize'] = 16
plt.rcParams['font.size'] = 14
plt.rcParams['legend.fontsize'] = 12
```

```python
# Plot earthquake occurrence as a function of year
plt.figure(figsize=(12,6))

sns.barplot(year_tot.index, year_tot.values, color='orange', alpha=0.6)
plt.axhline(y=year_ave, color='k', linestyle='-.', 
            alpha=0.5, label='average')
plt.xlabel('Year'), plt.ylabel('Number of earthquakes')
plt.xticks(rotation='vertical')
plt.legend()
plt.show()
```

![png](output_11_0.png)

With the exception of a few of the years (1998, 2017, etc), the number of earthquakes each year is pretty close to the average.

With a few exceptions, the number of earthquakes on any given day of the year is consistent. The dates that stand out are probably when some of the largest events occurred, since the biggest earthquakes are almost always followed by numerous aftershocks, many of which would be larger than M6. To find those events, we'll look at the day of the year with the greatest number of events.

```python
# Find the day of year with the greatest number of events
dofy_max = dofy_tot.sort_values(ascending=False).head(1).index[0]

# Print out the events that occur on those days of the year, 
# sorted by decreasing magnitude
df_max = df[df.dofy == dofy_max].sort_values('mag', ascending=False).head()
df_max[['place','time','dofy','year','mag']]
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

```
.dataframe tbody tr th {
    vertical-align: top;
}

.dataframe thead th {
    text-align: right;
}
```

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>place</th>
      <th>time</th>
      <th>dofy</th>
      <th>year</th>
      <th>mag</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1404</th>
      <td>2011 Great Tohoku Earthquake, Japan</td>
      <td>2011-03-11 05:46:24.120000+00:00</td>
      <td>70</td>
      <td>2011</td>
      <td>9.1</td>
    </tr>
    <tr>
      <th>1393</th>
      <td>near the east coast of Honshu, Japan</td>
      <td>2011-03-11 06:15:40.280000+00:00</td>
      <td>70</td>
      <td>2011</td>
      <td>7.9</td>
    </tr>
    <tr>
      <th>1388</th>
      <td>off the east coast of Honshu, Japan</td>
      <td>2011-03-11 06:25:50.300000+00:00</td>
      <td>70</td>
      <td>2011</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>1569</th>
      <td>Libertador General Bernardo O'Higgins, Chile</td>
      <td>2010-03-11 14:55:27.510000+00:00</td>
      <td>70</td>
      <td>2010</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>4852</th>
      <td>Tonga</td>
      <td>1989-03-11 05:05:00.630000+00:00</td>
      <td>70</td>
      <td>1989</td>
      <td>6.9</td>
    </tr>
  </tbody>
</table>
</div>

The [Tohoku-oki megathrust earthquake](https://earthquake.usgs.gov/earthquakes/eventpage/official20110311054624120_30#executive) occurred on March 11, 2011. With a magnitude of 9.1, there were hundreds of aftershocks with many of them above a magnitude 6.

What about the number of earthquakes for a given month? Grouping the events by month, maybe we can demonstrate that there isn't an "earthquake month" or a particular season in which earthquakes occur. 

```python
# Plot earthquake occurrence as a function of the month 
# (normalizing by the number of days in the month)

# Array of month lengths
month_length = np.array([31,28,31,30,31,30,31,31,30,31,30,31])
# Sort by month index
month_sort = month_tot.sort_index()
# Normalize by the length of the month
month_norm = month_sort.values / (month_length*(2017-1983+1))

# Plot (including daily average and the deviation)
plt.figure(figsize=(10,4))

ax = sns.barplot(month_sort.index, month_norm, alpha=0.5, color='orange')
ax.set_ylim([0, 0.7])
plt.axhline(y=np.mean(month_norm), color='k', linestyle='-.', 
            alpha=0.5, label='mean')
plt.axhspan(np.mean(month_norm)-np.std(month_norm), 
            np.mean(month_norm)+np.std(month_norm), 
            color='k', alpha=0.2, label='std')
plt.ylabel('Number of earthquakes per day'), plt.xlabel('Month')
plt.title('Worldwide Earthquakes M6 and Greater (1985-2020)')
plt.legend()
plt.show()
```

![png](output_15_0.png)

Well, there is maybe a little bit of a trend here, with a "peak" in April and "troughs" in January and July. It would be helpful to look at the number of events each month for individual years; a heatmap will be a great way to visualize this. Time to group some data!

### Grouping events for each year

```python
# Group the DataFrame: earthquake counts for each year (rows) by month (columns) 

# Group the entire dataframe by year and month, 
# find the number of earthquakes in each year and month,
# then unstack to remove the hierarchical indexing
# (pivot the 'month' level to return a DataFrame with new columns).
df_newcol = df.groupby(['year','month'])['day'].size().unstack()

# Divide each row by the month length array to return
# the number of earthquakes per day for each month 
df_newcol = df_newcol / month_length 
df_newcol.round(2).tail()
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

```
.dataframe tbody tr th {
    vertical-align: top;
}

.dataframe thead th {
    text-align: right;
}
```

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>month</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>10</th>
      <th>11</th>
      <th>12</th>
    </tr>
    <tr>
      <th>year</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2016</th>
      <td>0.42</td>
      <td>0.36</td>
      <td>0.16</td>
      <td>0.77</td>
      <td>0.23</td>
      <td>0.50</td>
      <td>0.23</td>
      <td>0.42</td>
      <td>0.43</td>
      <td>0.26</td>
      <td>0.43</td>
      <td>0.65</td>
    </tr>
    <tr>
      <th>2017</th>
      <td>0.26</td>
      <td>0.18</td>
      <td>0.19</td>
      <td>0.23</td>
      <td>0.35</td>
      <td>0.33</td>
      <td>0.32</td>
      <td>0.26</td>
      <td>0.30</td>
      <td>0.32</td>
      <td>0.53</td>
      <td>0.32</td>
    </tr>
    <tr>
      <th>2018</th>
      <td>0.35</td>
      <td>0.39</td>
      <td>0.26</td>
      <td>0.23</td>
      <td>0.19</td>
      <td>0.03</td>
      <td>0.32</td>
      <td>0.77</td>
      <td>0.40</td>
      <td>0.55</td>
      <td>0.47</td>
      <td>0.45</td>
    </tr>
    <tr>
      <th>2019</th>
      <td>0.39</td>
      <td>0.21</td>
      <td>0.39</td>
      <td>0.53</td>
      <td>0.42</td>
      <td>0.60</td>
      <td>0.45</td>
      <td>0.29</td>
      <td>0.30</td>
      <td>0.19</td>
      <td>0.57</td>
      <td>0.42</td>
    </tr>
    <tr>
      <th>2020</th>
      <td>0.52</td>
      <td>0.25</td>
      <td>0.29</td>
      <td>0.23</td>
      <td>0.29</td>
      <td>0.37</td>
      <td>0.35</td>
      <td>0.45</td>
      <td>0.43</td>
      <td>0.19</td>
      <td>0.27</td>
      <td>0.35</td>
    </tr>
  </tbody>
</table>
</div>

The grouped and unstacked data looks good. The number of earthquakes per day for each month is consistent with previous calculations; the average of each month column should equal the values plotted in the monthly bar graph above. Using the heatmap plot in the Seaborn package, let's see if there actually is an overall trend of more earthquakes in April and fewer in July.

### Heatmap for events by year and month

```python
# Plot a heatmap of the unstacked DataFrame created above
plt.figure(figsize=(12,8))
ax = sns.heatmap(df_newcol, linewidths=.5, cmap="bone_r", yticklabels=2,
                 cbar_kws={'label': 'Number of earthquakes per day'})
plt.yticks(rotation=0) 
plt.show()
```

![earthquake distribution heatmap](/assets/output_21_0.png)

### April showers bring...earthquakes?

It seems like there are actually more events in April and fewer in July. In April, especially from 2004 until 2016, there are more events than many of the other months, as shown by the line of darker shading. I don't quite know what is going on here so there is definitely more to be done to see where (on Earth) these events are happening and if there is any physical reason. We are also only looking at about 35 years of data here, so it may be that this pattern wouldn't be present with a larger sample size.

There is definitely more to do with this data set and other similar earthquake catalog data. Specifically, I would look at how the magnitudes of the earthquakes break down over individual years and months. It would also be helpful to look at where the earthquakes are occurring, especially by comparing the locations from month to month.

The data set used in this analysis is available [here](https://github.com/nwhoffman/earthquake_stats/blob/master/m6_1985_2020.csv) and the Jupyter notebook is [here](https://github.com/nwhoffman/earthquake_stats/eq_stats1.ipynb).
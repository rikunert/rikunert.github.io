---
layout: post
published: true
permalink: bike_rentals
comments: true

title: An analysis of the rental bike market in Berlin
subtitle: 200 lines of code (Python)
tags: [data science, python, visualisation, bike, market, pandas, matplotlib, time series]
lead: 2018 was a wild summer for the rental bike market in Berlin. Many new bike systems pushed into the market in the beginning. By now, two have already left. In between, I counted every rented bike I saw. Which bikes got rented the most?
---

![alt text](https://github.com/rikunert/bike_rentals/raw/master/rental_shares_total.png "2 months of rented bikes")

<!--excerpt-->

## The rental bike market in Berlin in 2018
On 7 May I started counting every single rented bike I saw in Berlin.
I cycle to work and the weather this summer was amazing, so there was a lot to be counted.
This summer was very particular because six new app-based rental services pushed into the market which previously only knew nextbike (Deezer brand), Call-A-Bike (Lidl brand) and Donky.

By May, the last of the new arrivals (Ofo) had started. So, I began counting and the figure at the top of this post shows the rental bike market shares overall.
I share and explain my analyses in great detail at the end of this post, so you can recreate everything I did.

Mo-Bike is clearly the most used bike rental App in Berlin at the moment.
Nextbike (using the brand Deezer) is in second place with nearly a third less rented bikes in my sample.
The incumbent in the Berlin rental bike market was Call-A-Bike (using the brand Lidl).
They only come in third place with a 17% market share.
The latest newcomer Ofo caught up fast and reaches the fourth place overall.

I did a little digging in the data and tried to find differences between days of the week.
Is one bike-app preferred over another based on whether it is a weekend or not?
The data are not really clear here, but my hunch is that this is not the case.
What is most striking from the following figure is how Mo-Bike dominates all days.

![alt text](https://github.com/rikunert/bike_rentals/raw/master/rental_shares_day.png "Different days different rented bikes?")

## A changing market
After I had stopped counting, two bike rental companies withdrew from the market: Ofo and O-Bike.
I didn't find the end of O-Bike particularly surprising as they had a weird pricing structure (see below), few bikes on offer and very few rented bikes in my sample.

But Ofo was a surprise. This newcomer did really well.
However, below an overall good performance lies a surprising development. Ofo pushed hard, boomed and then went bust.
These are the rental bike market shares of the seven weeks during which I counted rented bikes.

![alt text](https://github.com/rikunert/bike_rentals/raw/master/rental_shares_KW.png "Weekly rental bike market share")

The picture is even clearer when not looking at different weeks but instead at a 7-day rolling average.
In the following graph you can see the yellow line of Ofo briefly going up aroung 1 June, even surpassing Mo-Bike.
Afterwards it returns to a below 10% market share which is where the company had started off.

Moreover, you can see that I wasn't in Berlin around 18 May (the gap in the data).

![alt text](https://github.com/rikunert/bike_rentals/raw/master/rental_shares_rolling.png "Weekly rental bike market share")

## Different pricing structures
How can we explain the different market shares?
I believe a huge factor is the availability of bikes and the cost of rentals.
Cycling comfort probably only comes third.
The only factor I could get some quantifiable information on is cost.
The following plot shows the cost of rented bikes from different systems over time.

![alt text](https://github.com/rikunert/bike_rentals/raw/master/rental_costs.png "Rental bike costs in Berlin")

Lime-E is strikingly expensive. However, it is also the only system offering electric-assist bikes. I predict that if Lime-E stays, these bikes will remain a luxury without much market share.

Mo-Bike dominates the market and the cost-plot shows why. For a twenty minute bike ride, they offer the cheapest deal.
Byke is equally cheap and over time even cheaper. However, they didn't put enough bikes on the streets of Berlin in order to dominate the market.
If Mo-Bike manages to somehow turn a profit from these tiny prices, they stand a very good chance of dominating the rental bike market for the foreseeable future.

What the previous figure doesn't show are the set-up costs of different rental bike systems. Some apps require a yearly subscription fee or a one-time deposit.
Once one takes this into account, O-Bike is the most expensive system by far. A 79 EUR deposit is a huge barrier for new customers.
It's really no surprise, O-Bike was so unsuccessful.

![alt text](https://github.com/rikunert/bike_rentals/raw/master/rental_costs_extra.png "Rental bike costs in Berlin including set-up costs")

## The future of rental bikes in Berlin
There are currently huge discussions in Germany as to whether the new app-based rental bike systems 'litter' the streets with bikes.
I believe we will look back with envy at the summer of 2018 when there was a huge choice of different rental bikes.
If the legal framework remains unchanged (i.e. without regulation), Mo-Bike and perhaps one other system should remain on offer.
In the future, there will likely be less choice for Berliners and tourists who this summer quickly grew accustomed to cheap, high quality bikes at every corner, waiting for customers with smart phones.

For cyclists, the summer of 2018 was a wild ride.

## Time series analysis in Python
We start off the data analysis by importing the `pandas` module for data analytics and two `matplotlib` modules for plotting.
```python
# module import
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib.ticker as mtick
```
Next, we set some global parameters, such as the hex colours of the different brands.
```python
# global parameters
colours = ['#006183', '#7AB538', '#FF3000', '#FFD800', '#00C930', '#00838F', '#FF6A00', '#FF9A24', 'grey']
weekdays = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday', '0']
plt.style.use('fivethirtyeight')
```
We are ready to download the data from github and clean it a bit.
```python
# data gathering
df = pd.read_excel('https://github.com/rikunert/bike_rentals/raw/master/Bike-sharing_sample.xlsx',
                   skiprows=range(4))

# data cleaning
df.fillna(value=0, inplace=True)

# data types
df.loc[:, 'Date'] = pd.to_datetime(df.loc[:, 'Date'], errors='coerce')
df = df.set_index('Date')
```
The first plot we produce is this:
![alt text](https://github.com/rikunert/bike_rentals/raw/master/rental_shares_total.png "2 months of rented bikes")
```python
#overall proportion plot
prop_total = df.loc[:, 'Deezer': 'non-App'].sum()/df.loc[:, 'Deezer': 'non-App'].sum().sum() * 100
prop_total = pd.DataFrame({'market_share':prop_total,
                           'colours':colours})
prop_total.sort_values('market_share', inplace=True, ascending=False)
fig_total, ax = plt.subplots(figsize=[8, 6])
prop_total.loc[:, 'market_share'].plot(kind='bar', ax=ax, color=prop_total.loc[:, 'colours'])
ax.set(ylabel='Market share (May and June 2018)', title='Rental bike market in Berlin')

# yticks
ax.set_yticks(range(0, 40, 10))
fmt = '%.0f%%' # Format you want the ticks, e.g. '40%'
yticks = mtick.FormatStrFormatter(fmt)
ax.yaxis.set_major_formatter(yticks)

ax.grid(visible=False, axis='x')
ax.text(0.8, 0.8, 'N = {}'.format(int(df.loc[:, 'Deezer': 'non-App'].sum().sum())),
        horizontalalignment='right',
        transform=ax.transAxes)
fig_total.text(0.99, 0.01, '@rikunert', color='grey', style='italic',
         horizontalalignment='right')
plt.tight_layout()
fig_total.savefig('rental_shares_total.png')
```
Next, we can define a function to plot the market share over time.
Unfortunately, the function needs to be tweaked a bit for showing the development during the week (week day analysis) and the development across weeks (calendar week analysis).
```python
def prop_plotter(df_count, df_prop):

    fig, ax = plt.subplots(tight_layout=True, figsize=[10, 6])

    df_prop.plot(ax=ax, color=colours, legend=False,
                 marker='.', markersize=20,
                 linewidth=3)

    #x ticks
    if df_prop.index.dtype == 'int64' or df_prop.index.dtype == '<M8[ns]':  # calendar week
        ax.set_xticks(df_prop.index)

    labels = [item.get_text() for item in ax.get_xticklabels()]
    print(labels)
    for i in range(df_prop.shape[0]):
        if df_prop.index.dtype == 'int64':  # calendar week
            labels[i] = 'N={}\n\n{}'.format(int(df_count.loc[df_count.index[i], 'sum']),
                                            int(df_prop.index[i]))
        elif df_prop.index.dtype == 'str':  # weekdays
            labels[i + 1] = 'N={}\n\n{}'.format(int(df_count.loc[df_count.index[i], 'sum']),
                                            df_prop.index[i])

    ax.set_xticklabels(labels)

    # yticks
    ax.set_yticks(range(0, 50, 10))
    fmt = '%.0f%%' # Format you want the ticks, e.g. '40%'
    yticks = mtick.FormatStrFormatter(fmt)
    ax.yaxis.set_major_formatter(yticks)

    ax.set(ylabel='rental bike market share in Berlin',
           title=' ')

    # new legend
    if df_prop.index.dtype == 'int64':
        x_pos = ax.get_xticks()
    else:
        x_pos = ax.get_xticks()[1:-1]
    if df_prop.index.dtype == '<M8[ns]':
        x_adjustment = len(x_pos)/30
    else:
        x_adjustment = len(x_pos) / 50
    for i, brand in enumerate(df_prop.columns):
        print(brand)
        ax.text(x=x_pos[-1] + x_adjustment,
                y=df_prop.loc[df_prop.index[-1], brand],
                s=brand,
                color=colours[i],
                verticalalignment='center')

    ax.margins(0.15, 0.05)
    ax.grid(visible=False, axis='x')

    #author line
    fig.text(0.99, 0.01, '@rikunert', color='grey', style='italic',
                     horizontalalignment='right')

    return fig, ax
```
Thanks to Python's time series functionality, one can get to the aggregated data by calendar week pretty quickly.
We use `DataFrame.resample()` and give it the argument `"7d"` meaning seven days.
Because the first entry is a Monday, we aggregate periods from Monday to Sunday.
The following code produces this figure:
![alt text](https://github.com/rikunert/bike_rentals/raw/master/rental_shares_KW.png "Weekly rental bike market share")
```python
df_KW1 = df.resample("7d").sum().loc[:, 'Deezer':'sum']
df_KW1.index = df_KW1.index.week
df_KW1.index.name = 'calendar week'

df_KW1_prop = df_KW1.div(df_KW1['sum'], axis=0).loc[:, :'non-App'] * 100

fig_KW, ax_KW = prop_plotter(df_KW1, df_KW1_prop)
ax_KW.set(title='The rise and fall of Ofo')
```
Even the aggregation by day of the week is surprisingly pain free.
It makes use of `DataFrame.index.weekday` which stores which day of the week a `DatetimeIndex` corresponds to.
We can then use `DataFrame.groupby()` in order to aggregate by this new weekday index.
The following code produces this figure:
![alt text](https://github.com/rikunert/bike_rentals/raw/master/rental_shares_day.png "Different days different rented bikes?")
```python
df_d = df.copy()
df_d.index = pd.Series(weekdays)[df_d.index.weekday]
df_day1 = df_d.groupby(df_d.index).sum().loc[:, 'Deezer':'sum']
df_day1.index.name = 'week day'

df_day1_prop = df_day1.div(df_day1['sum'], axis=0).loc[:, :'non-App']

df_day1 = df_day1.reindex(weekdays[:-1])
df_day1_prop = df_day1_prop.reindex(weekdays[:-1]) * 100

fig_day, ax_day = prop_plotter(df_day1, df_day1_prop)
ax_day.set(title='Intra-week differences in bike rental market share')
fig_day.savefig('rental_shares_day.png')
```
The rolling average analysis uses the `DataFrame.rolling()` method.
We specify a 7 period rolling average for data which we aggregated by day just before.
If less than 4 periods during these 7 days are filled, we will get a `NaN` value.
![alt text](https://github.com/rikunert/bike_rentals/raw/master/rental_shares_rolling.png "Weekly rental bike market share")
```python
df_D = df.resample("D").sum().loc[:, 'Deezer':'sum']
df_rolling = df_D.rolling(7, min_periods=4, center=True).sum()
df_rolling_prop = df_rolling.div(df_rolling['sum'], axis=0).loc[:, :'non-App'] * 100

fig_rolling, ax_rolling = prop_plotter(df_rolling, df_rolling_prop)
ax_rolling.set(title='A volatile bike rental market')
ax_rolling.set(ylabel='rental bike market share in Berlin \n(7 day rolling average)',
               xlabel='May                                                  June\n2018')
fig_rolling.savefig('rental_shares_rolling.png')
```
Calculating the costs of different bike systems was done thanks to information in [this document](https://www.agora-verkehrswende.de/fileadmin/Projekte/2018/Stationslose_Bikesharing_Systeme/Agora_Verkehrswende_Bikesharing_WEB.pdf).
```python
duration = 120
df_cost = pd.DataFrame(data={'Deezer': [(i//30 + 1) * 1.5 for i in range(duration)],
                        'Lidl':[(i//30 + 1) * 1.5 for i in range(30)] + [((i//30 + 1) * 1) + 0.5 for i in range(30, duration)],
                        'Mo-Bike':[(i//20 + 1) * 0.5 for i in range(duration)],
                        'Ofo':[(i//30 + 1) * 0.8 for i in range(duration)],
                        'Lime-E':[(i//1 + 1) * 0.15 + 1 for i in range(duration)],
                        'Byke':[(i//30 + 1) * 0.5 for i in range(duration)],
                        'Donkey':[(i//30 + 1) * 1.25 for i in range(duration)],
                        'O-Bike':[(i//30 + 1) * 1 for i in range(duration)]})
df_cost = df_cost[df.columns[5:-2]]#  reorder columns
df_cost_extra = df_cost.copy()
df_cost_extra['Lidl'] = df_cost_extra['Lidl'] + 3
df_cost_extra['Mo-Bike'] = df_cost_extra['Mo-Bike'] + 2
df_cost_extra['O-Bike'] = df_cost_extra['O-Bike'] + 79
```
Having calculated the costs per minute, we can plot them. In order to be able to do that, we define a new function.
```python
def cost_plotter(df):

    fig_cost, ax_cost = plt.subplots(tight_layout=True, figsize=[10, 6])
    df.plot(drawstyle="steps-post", linewidth=4, color=colours,
             ax=ax_cost,
             legend=False)
    ax_cost.set(xlabel='Rental duration (minutes)',
       ylabel='Cost without deposit or subscription (EUR)',
       ylim=[0, 8],
       title='Price differences between rental bikes in Berlin')
    ax_cost.grid(visible=False, axis='x')

    # additional plotting with different line styles
    lstyle = [':', ':', ':',
                 '-', '-', '-',
                 ':', '-', '-']
    for i in [1, 2, 0]:
        df.iloc[:, i].plot(drawstyle="steps-post",
                    linewidth=4, color=colours[i],
                    linestyle=lstyle[i],
            ax=ax_cost,
            legend=False)

    # new legend
    x_pos = ax_cost.get_xticks()
    x_adjustment = len(x_pos)/5
    for i, brand in enumerate(df_cost.columns):
        if (brand == 'Lime-E') and (df.max().max() == df['Lime-E'].max()):
            ax_cost.text(x=x_pos[-2] + x_adjustment,
                y=max(ax_cost.get_yticks()),
                s=brand,
                color=colours[i],
                verticalalignment='center')
        else:
            ax_cost.text(x=x_pos[-2] + x_adjustment,
                y=df.loc[df.index[-1], brand],
                s=brand,
                color=colours[i],
                verticalalignment='center')

    ax_cost.margins(0.15, 0.05)
    ax_cost.grid(visible=False, axis=1)

    fig_cost.text(0.99, 0.01, '@rikunert', color='grey', style='italic',
                     horizontalalignment='right')
    return fig_cost, ax_cost
```
Having defined the function, the plotting of the costs can be done with the following code.
![alt text](https://github.com/rikunert/bike_rentals/raw/master/rental_costs.png "Rental bike costs in Berlin")
![alt text](https://github.com/rikunert/bike_rentals/raw/master/rental_costs_extra.png "Rental bike costs in Berlin including set-up costs")
```python
fig_cost, ax_cost = cost_plotter(df_cost)
fig_cost.savefig('rental_costs.png')

fig_cost_extra, ax_cost_extra = cost_plotter(df_cost_extra)
ax_cost_extra.set(ylabel='Cost including deposit or subscription (EUR)',
                  ylim=[0, 90],
                  title='Price differences between rental bikes including set-up costs')
fig_cost_extra.savefig('rental_costs_extra.png')
```
The complete script which I used for this post can be found on github [here](https://github.com/rikunert/bike_rentals/blob/master/rental_bike_analysis.py).

{% include twitter_plug.html %}

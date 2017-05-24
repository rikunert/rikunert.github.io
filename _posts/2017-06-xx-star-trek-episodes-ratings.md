---
layout: post
published: true
permalink: star_trek_episodes
comments: true

title: Using Python and the IMDb API to find the best Star Trek episode
subtitle: 100 lines of code 
tags: [data science, python, web scraping, IMDb, API, rotten tomatoes, metacritic, visualisation, ggplot, matplotlib]
lead: A new Star Trek series, Discovery, is on the horizon. Time to look back at the best episodes of the franchise so far. This guide will teach you to use the IMDb API to get the answers yourself.
---

![alt text](https://github.com/rikunert/Star_Trek_ratings/raw/master/Star%20Trek%20ratings_dates.png "Star Trek episodes according to airing date and IMDb rating")

<!--excerpt-->

**Which one is the best Star Trek episode?** 

TL;DR: Star Trek The Next Generation: [The Best of Both Worlds: Part 1](http://www.imdb.com/title/tt0708785/). 
The full top 10 based on IMDb ratings is in the table below. 

Why did I use IMDb ratings? Well, they are the only data base with ratings for each individual episode. 
And not few raters either. Each episode in the table below was rated by more than 1000 people.

Episode | Series | average IMDb rating
--------|----------|----------
[The Best of Both Worlds: Part 1](http://www.imdb.com/title/tt0708785/) | [TNG](http://www.imdb.com/title/tt0092455) | 9.4
[The Inner Light](http://www.imdb.com/title/tt0708803) | [TNG](http://www.imdb.com/title/tt0092455) | 9.4
[The Best of Both Worlds: Part 2](http://www.imdb.com/title/tt0708786/) | [TNG](http://www.imdb.com/title/tt0060028) | 9.3 
[Trials and Tribble-ations](http://www.imdb.com/title/tt0708655/) | [DS9](http://www.imdb.com/title/tt0106145) | 9.3
[In the Pale Moonlight](http://www.imdb.com/title/tt0708557) | [DS9](http://www.imdb.com/title/tt0106145) | 9.3
[The City on the Edge of Forever](http://www.imdb.com/title/tt0708455) | [TOS](http://www.imdb.com/title/tt0060028) | 9.3
[Yesterday's Enterprise](http://www.imdb.com/title/tt0708845) | [TNG](http://www.imdb.com/title/tt0092455) | 9.2
[Mirror, Mirror](http://www.imdb.com/title/tt0708438) | [TOS](http://www.imdb.com/title/tt0060028) | 9.2
[The Measure of a Man](http://www.imdb.com/title/tt0708807) | [TNG](http://www.imdb.com/title/tt0092455) | 9.1
[The Visitor](http://www.imdb.com/title/tt0708645) | [DS9](http://www.imdb.com/title/tt0106145) | 9.1

Man trekkies won't be surprised to hear that Star Trek: The Next Generation dominates the top 10. 
This series is generally regarded as the high point of Star Trek television. 
However, I was shocked to find that the worst Star Trek episode is from The Next Generation, too: [Shades of Gray](http://www.imdb.com/title/tt0708772).

This made me wonder what the best Star Trek series is.
From the table below, you can see that the five Star Trek series so far are extremely similar in terms of the average episode rating, whether measured in terms of the mean or the median.
However, this does not correspond to viewers rating the series as whole in which case The Next Generation is king.

Series | mean IMDb episode rating | median IMDb episode rating | IMDb series rating
----------|----------|----------|----------
[The Original Series](http://www.imdb.com/title/tt0060028) | 7.5 | 7.5 | 8.4
[The Next Generation](http://www.imdb.com/title/tt0092455) | 7.4 | 7.4 | 8.6
[Deep Space Nine](http://www.imdb.com/title/tt0106145) | 7.5 | 7.5 | 7.9
[Voyager](http://www.imdb.com/title/tt0112178)| 7.4 | 7.3 | 7.7
[Enterprise](http://www.imdb.com/title/tt0244365)| 7.7 | 7.6 | 7.5

I suspect that a series, in order to be remembered as 'generally good', has to include a few stellar episodes. Fandom will forgive a few atrocious episodes but it won't forgive mediocrity.
The figure below shows the distribution of episode ratings. In such a density plot a high vertical value corresponds to many ratings aroung this point, similar to a histogram.
It turns out that all the worst episodes are from The Next Generation. Fans forgave this mishaps, apparently. 

The mediocrity of Voyager is well illustrated by the high peak just above 7 and no very bad or indeed very good episodes.
This certainly mirrors my own experience: Voyager was not bad enough to tune out but gave you very little reason to really get engaged. 

![alt text](https://github.com/rikunert/Star_Trek_ratings/raw/master/Star%20Trek%20ratings_density.png "The distribution of episode ratings in each Star Trek series")

## Data acquisition

The data acquisition for this post was very very similar as for [my previous Star Trek post](http://rikunert.com/star_trek_movies).
So, I won't go into details here and simply link to [the data acquisition script](https://github.com/rikunert/Star_Trek_ratings/blob/master/imdb_STS_data_acquisition.py) 
and [the resulting data set](https://github.com/rikunert/Star_Trek_ratings/blob/master/Star_Trek_data.csv) on github. The script is well commented. Should you nonetheless have a question, just leave a comment.

## Data visualisation: using ggplot and matplotlib

Somewhat similarly to [my previous Star Trek post](http://rikunert.com/star_trek_movies), I use python 2.7 and start by loading modules and data:

```python
import matplotlib.pyplot as plt  # for plotting
from matplotlib.cbook import get_sample_data  # for adding image to plot
from matplotlib.offsetbox import (OffsetImage, AnnotationBbox)
from ggplot import *  # for plotting
import pandas as pd  # for plotting with ggplot
import datetime as dt  # for date handling

# load data
df = pd.read_csv('C:\Users\Richard\Desktop\python\IMDb_analyses\Star Trek\Star_Trek_data.csv')
df['date'] = pd.to_datetime(df['date'])
```

For the scatter plot (first figure above), I start with ggplot for the basic figure without images.

```python
p = ggplot(aes(x='date', y='rating', colour='title'), data=df) + geom_point() + theme_bw()  # basic plot
p = p + ylim(1, 10) + scale_x_date(labels='%Y') + xlab('Date') + ylab('Mean IMDb rating')  # make axes pretty
p = p + ggtitle('Star Trek episode ratings')  # add title
```

Then, I export the figure to use it with matplotlib which allows me to add graphics. First, I add my author tag. 

```python
p.make()  # exporting the figure to use it in matplotlib
plt.text(dt.datetime(2003, 1, 1), 1.2, '@rikunert')  # keep figure open for this to work
```

Then, I add star ships to show which colour corresponds to which series. 
I found the star ship images online and put them [on my github](https://github.com/rikunert/Star_Trek_ratings) for your convenience. 
Just download them, put them in a folder and add the path to this folder in line 2 of the function. 

```python
def add_starship(ax_, ship, xy, imzoom):
    fn = get_sample_data("PATH TO IMAGE FOLDER\\" + ship + ".jpg", asfileobj=False)
    arr_img = plt.imread(fn, format='jpg')
    imagebox = OffsetImage(arr_img, zoom=imzoom)
    imagebox.image.axes = ax_
    ab = AnnotationBbox(imagebox, xy,
                        xybox=(0., 0.),
                        boxcoords="offset points",
                        pad=-0.5)  # hide enclosing box behind image
    ax_.add_artist(ab)
    return ax_

ax = plt.gca()  # get current axes (axes is like the drawing area apparently)
ax.legend_.remove()  # remove legend

# add images of star ships
add_starship(ax, 'TOS', [dt.datetime(1972, 1, 1), 2], 0.1)
add_starship(ax, 'TNG', [dt.datetime(1989, 1, 1), 2.5], 0.1)
add_starship(ax, 'DS9', [dt.datetime(1994, 1, 1), 2], 0.15)
add_starship(ax, 'VOY', [dt.datetime(1999, 6, 1), 2.6], 0.1)
add_starship(ax, 'ENT', [dt.datetime(2002, 1, 1), 2], 0.1)
```

Now, we can just annotate, the three episodes which stand out and we are nearly done.

```python
#most delayed episode
max_TOS_date = max(df[df['title'] == 'Star Trek']['date'])
ax.annotate('That TOS episode \nwhich was not aired', xy=(max_TOS_date, df[df['date'] == max_TOS_date]['rating']),
            xytext=(dt.datetime(1975, 1, 1), 8),
            arrowprops=dict(facecolor='black', shrink=0.05))

#worst episode
min_rating = min(df['rating'])
episode_date = min(df[df['rating'] == min_rating]['date'])  # extract time stamp with min function
episode_name = df[df['rating'] == min_rating]['episode']
ax.annotate('The worst episode:\n' + episode_name.iloc[0], xy=(episode_date, min_rating),
            xytext=(dt.datetime(1980, 1, 1), 4),
            arrowprops=dict(facecolor='black', shrink=0.05))

#best episode
max_rating = max(df['rating'])
episode_date = min(df[df['rating'] == max_rating]['date'])  # extract time stamp with min function
episode_name = df[df['rating'] == max_rating]['episode']
ax.annotate('The best episode:\n' + episode_name.iloc[0], xy=(episode_date, max_rating),
            xytext=(dt.datetime(1975, 1, 1), 9.7),
            arrowprops=dict(facecolor='black', shrink=0.05))
```
Finally, we optimize the figure dimensions to social media standards and save it all.
```python
fig = plt.gcf()  # get current figure
fig.set_size_inches(1024 / 70, 512 / 70)  # reset the figure size to twitter standard
fig.savefig('PATH TO FOLDER/IMAGE NAME.png', dpi=96,
            bbox_inches='tight')
```

![alt text](https://github.com/rikunert/Star_Trek_ratings/raw/master/Star%20Trek%20ratings_dates.png "The final figure")

Regarding the first table, the pandas module makes it very easy to get the best ten episodes. Just call:
```python
df.sort_index(by = ['rating'], ascending = False)[:10]
```

We follow the exact same strategy for the density plot. 
We first make a density plot with ggplot, the turn to matplotlib to add images of star ships.
This should now be relatively straight forward.

```python
#  start plotting using ggplot
p = ggplot(aes(x='rating', colour='title'), data=df) + geom_density() + theme_bw()  # basic plot
p = p + xlim(1, 10) + xlab('IMDb rating') + ylab('Density')  # make axes pretty
p = p + ggtitle('Star Trek episode ratings')  # add title
print p  # in case you want to see what has been made in ggplot

# continue with matplotlib for annotations and adding images
p.make()  # exporting the figure to use it in matplotlib
plt.text(9.5, 0, '@rikunert')  # keep figure open for this to work

ax = plt.gca()  # get current axes (axes is like the drawing area apparently)
ax.legend_.remove()  # remove legend

# add images of star ships
add_starship(ax, 'TOS', [6.85, 0.52], 0.05)
add_starship(ax, 'TNG', [5.38, 0.2], 0.08)
add_starship(ax, 'DS9', [8.305, 0.52], 0.15)
add_starship(ax, 'VOY', [7, 0.58], 0.1)
add_starship(ax, 'ENT', [9.05, 0.4], 0.05)

fig = plt.gcf()  # get current figure
fig.set_size_inches(1024 / 70, 512 / 70)  # reset the figure size to twitter standard
fig.savefig('PATH TO FOLDER/IMAGE NAME.png', dpi=96,
            bbox_inches='tight')
```
![alt text](https://github.com/rikunert/Star_Trek_ratings/raw/master/Star%20Trek%20ratings_density.png "The final figure")
{% include twitter_plug.html %}

---
layout: post
published: true
permalink: star_trek_movies
comments: true

title: Using Python, the IMDb API, and web-scraping rotten tomatoes to find the best Star Trek movie
subtitle: 120 lines of code 
tags: [data science, python, web scraping, IMDb, API, rotten tomatoes, metacritic, visualisation, ggplot, matplotlib]
lead: Star Trek is a very rare positive vision of the future. Which movie captures the audience the most with this hopeful message? I learned python to sample all the movie ratings I could find in order to answer this question. If you follow this guide, so can you.
---

![alt text](https://github.com/rikunert/Star_Trek_ratings/raw/master/Star_Trek_movie_ratings_dates.png "The final figure")

<!--excerpt-->

**Which one is the best Star Trek movie?** 

The short answer is [Star Trek](http://www.imdb.com/title/tt0796366/) of the 2009 reboot. 
The top five Star Trek movies according to an average of user ratings on IMDb and rotten tomatoes as well 
critic ratings aggregated by metacritic and rotten tomatoes are:  

Movie | average rating
--------|----------
[Star Trek (2009)]((http://www.imdb.com/title/tt0796366/)) | 4.32 stars
[Star Trek: First Contact]((http://www.imdb.com/title/tt0117731/?ref_=nv_sr_1)) | 4.08 stars
[Star Trek Into Darkness](http://www.imdb.com/title/tt1408101/) | 4.04 stars
[Star Trek II: The Wrath of Khan](http://www.imdb.com/title/tt0084726/?ref_=nv_sr_2) | 4.04 stars
[Star Trek IV: The Voyage Home](http://www.imdb.com/title/tt0092007/?ref_=fn_al_tt_1) | 3.79 stars

To many trekkies the entries for First Contact and the Wrath of Khan won't come as a surprise. 
These movies are generally regarded as high points in the franchise. 
However, what astonished me was just how successful the 2009 reboot was. 
Star Trek never before managed to churn out three decent movies in a row. 
There is hope that the next film will be a similarly good. 

How you can get at all this information is shown in this blog post. 

>Data, the final frontier. These are the adventures of a data scientists. 
>His continuing mission to explore strange new patterns, to seek out new insights and new visualisations,
>to boldly find out what no one has found out before...

## Data acquisition: using IMDb API and web scraping

Start off by loading all the necessary modules. Because of IMDb-py I use python 2.7. 
```python
import imdb as imdb  # to access imdb API
import pandas as pd  # for data array handling
from BeautifulSoup import BeautifulSoup  # for website parsing and scraping (rotten tomatoes)
import requests  # for http access
import re  # for regular expressions
from ggplot import *  # for plotting
import urllib2  # for accessing url object (movie covers)
import matplotlib.pyplot as plt  # for plotting
from matplotlib.offsetbox import (OffsetImage, AnnotationBbox)
```

Next, we sample all Star Trek movies. We shall use the IMDb search function for that. 
```python
imdb_http = imdb.IMDb()  # create imdb API object
StarTrek = imdb_http.search_movie('Star Trek')  # general search for Star Trek among movie and series titles
```
Because we are not interested in series or video games, we extract only the movies with a simple list comprehension.
```python
STF = [i for i in StarTrek if i.data['kind'] == 'movie']
```
Unfortunately, IMDb's search function is not flawless and misses 5 movies. 
We search for them individually and add them to the list `STF` which holds the movies.
```python
StarTrekIII = imdb_http.search_movie('Star Trek III the Search for Spock')
StarTrekIV = imdb_http.search_movie('Star Trek IV the voyage home')
StarTrekV = imdb_http.search_movie('Star Trek V the Final Frontier')
StarTrekVI = imdb_http.search_movie('Star Trek VI the Undiscovered Country')
StarTrekFC = imdb_http.search_movie('Star Trek First Contact')
STF.extend([StarTrekIII[0], StarTrekIV[0], StarTrekV[0], StarTrekVI[0], StarTrekFC[0]])
```
Now, we can loop through each movie and add all the information we want to a pandas data frame `df`.
The IMDb API gives access to IMDb user ratings and metacritic ratings. 
However, in order to get rotten tomatoes ratings we turn to web scraping using `BeautifulSoup`. 

Note that different ratings use different scales. 
I decided to turn all of them into an intuitive 6-point scale (zero to five stars).
```python
df = pd.DataFrame(columns=['date', 'IMDb_rating', 'Metacritic_rating', 'title', 'image_url'])  # initialise data frame

for i in range(len(STF)):  # for each Star Trek movie

    imdb_http.update(STF[i])  # IMDb: augment movie info
    x = imdb_http.get_movie_critic_reviews(STF[i].movieID)  # Meta critic

    # rotten tomato: prepare website parsing
    tomato_base_url = 'https://www.rottentomatoes.com/m/'
    tomato_url = tomato_base_url + re.sub(':', '', re.sub(' ', '_', str(STF[i]['title'])))
    if 'Star Trek' not in STF[i]['title']:  # fix first contact problem
        tomato_url = tomato_base_url + re.sub(':', '', re.sub(' ', '_', 'Star Trek ' + str(STF[i]['title'])))
    elif 'Khan' in STF[i]['title']:  # fix wrath of khan problem
        tomato_url = tomato_base_url + re.sub(':', '_II', re.sub(' ', '_', str(STF[i]['title'])))
    soup = BeautifulSoup(requests.get(tomato_url).text)  # rotten tomatoes: website parse tree

    # add data to pandas data frame
    if 'year' in STF[i].data.keys() and bool(x['data']):  # filter out movies in production and those without MC data
        df = df.append(pd.DataFrame(data={
            'date': STF[i].data['year'],
            'IMDb_rating': [((STF[i].data['rating'] - 1) / 9.0) * 5],  # normalised to 5 star system
            'Metacritic_rating': [int(x['data']['metascore']) / 20.0],  # normalised to 5 star system
            'Tomatometer': [
                int(min(soup.find('span', {'class': 'meter-value superPageFontColor'}).contents[0])) / 20.0],
        # rotten tomatoe score (normalised to 5 star system)
            'Tomato_user': [
                int(filter(str.isdigit, str(soup.find('span', {'class': 'superPageFontColor'}).contents[0]))) / 20.0],
        # tomato audience score (normalised to 5 star system)
            'title': STF[i].data['title'],
            'image_url': STF[i]['cover url']}))
```
At this point one might want to save the data, for example by calling `df.to_csv('Star_Trek_movie_data.csv', sep=';')`. 
The result can be downloaded [here](https://github.com/rikunert/Star_Trek_ratings/blob/master/Star_Trek_movie_data.csv).

## Data visualisation: using ggplot and matplotlib

I start off by using the `ggplot` module as I am very familiar with the syntax from **R**.

```python
p = ggplot(aes(x='date', y='IMDb_rating'), data=df) + geom_point() + \
    geom_line(size=5, color='orange') + theme_bw()  # basic plot
p = p + geom_line(aes(x='date', y='Metacritic_rating'), data=df, size=5, color='purple')
p = p + geom_line(aes(x='date', y='Tomatometer'), data=df, size=5, color='grey')
p = p + geom_line(aes(x='date', y='Tomato_user'), data=df, size=5, color='blue')
p = p + ylim(0, 5) + xlim(1975, 2016) + xlab(' ') + ylab(' ') + ggtitle('Star Trek movie ratings')  # make axes pretty
```

![alt text](https://github.com/rikunert/Star_Trek_ratings/raw/master/Star_Trek_movie_ratings_dates_interim1.png "Interim figure")

The plot `p` now holds essentially all the information we need. 
But it is not pretty yet, as you can see by calling `print p` which is what I did to produce the figure above.
For visual gimicks we shall leave `ggplot` and turn to `matplotlib`.

The module `matplotlib` works very much like matlab figure production. 
So, the figure should not be saved in a variable like `p` above, but instead be open.

```python
p.make()  # exporting the figure to use it in matplotlib
```
The first thing to improve is to tell the reader what the different lines represent.
I personally believe that it is best practice to avoid separate legends and, instead, use intuitive explanations in the figure itself.
```python
plt.text(2017.5, 0, '@rikunert', color='black')  # keep figure open for this to work
plt.text(2017.5, 4.25, 'Rotten\nTomatoes', color='grey')  # keep figure open for this to work
plt.text(2017.5, 3.75, 'Rotten\nTomatoes\nusers', color='blue')  # keep figure open for this to work
plt.text(2017.5, 3.4, 'IMDb users', color='orange')  # keep figure open for this to work
plt.text(2017.5, 3.2, 'Metacritic', color='purple')  # keep figure open for this to work
```
The result:

![alt text](https://github.com/rikunert/Star_Trek_ratings/raw/master/Star_Trek_movie_ratings_dates_interim2.png "Interim figure")

How to tell the viewer which movie is where?
The film posters are the easiest way to achieve this. 

Including an image in a plot is not straight forward. 
I will use the annotation box approach and hide the box itself behind the image.

First off though, we need the drawing area called axes or `ax`.
```python
ax = plt.gca()
```
Next we define a new function `add_image()` to place an image from `url` at the coordinates `xy` 
of drawing area `ax_` with the image zoom `imzoom`. 
```python
def add_image(ax_, url, xy, imzoom):
    if 'http' in url:  # image on internet
        f = urllib2.urlopen(url)
    else:  # image in working directory
        f = url
    arr_img = plt.imread(f, format='jpg')
    imagebox = OffsetImage(arr_img, zoom=imzoom)
    imagebox.image.axes = ax_
    ab = AnnotationBbox(imagebox, xy, xybox=(0., 0.), boxcoords="offset points", pad=-0.5)  # hide box behind image
    ax_.add_artist(ab)
    return ax_
```
Adding each film poster is now easy. For the ordinate (y-axis), by the way, I chose the average rating.
```python
for i in range(len(df)):  # for each Star Trek movie
    add_image(ax, df['image_url'][i], [df['date'][i], sum(df.iloc[i, 1:5]) / 4.0], 0.3)
```

![alt text](https://github.com/rikunert/Star_Trek_ratings/raw/master/Star_Trek_movie_ratings_dates_interim3.png "Interim figure")

I think the x-axis could be simplified.
```python
ax.xaxis.set_ticks(range(1980, 2020, 10))  # minimal x-axis style
```
I replcae the y-axis by star symbols. All in the interest of avoiding text.
```python
ax.yaxis.set_visible(False)  # no numerical y-axis at all
add_image(ax, 'grey_star.jpg', [1975, 0], 0.05)  # zero stars on sort of y-axis
for i in range(1, 6):  # for each star rating from 1 onwards
    for j in range(i):  # for each individual star
        add_image(ax, 'gold_star.jpg', [1975 + j * 0.7, i], 0.05)
```

![alt text](https://github.com/rikunert/Star_Trek_ratings/raw/master/Star_Trek_movie_ratings_dates_interim4.png "Interim figure")

What stands out the most from the figure is just how awful Star Trek V was. Let's highlight this.
```python
ax.annotate('The absolute worst movie:\n' + df[df['IMDb_rating'] == min(df['IMDb_rating'])]['title'].iloc[0],
            xy=(1988, 1.5), xytext=(1978, 1), arrowprops=dict(facecolor='black', shrink=0.05))
```

![alt text](https://github.com/rikunert/Star_Trek_ratings/raw/master/Star_Trek_movie_ratings_dates_interim5.png "Interim figure")

Finally, the figure dimensions are not optimised for social media like twitter. 
And the margins are simply too big. The last statements deal with these problems 
```python
fig = plt.gcf()  # get current figure to show it
fig.set_size_inches(1024 / 70, 512 / 70)  # reset the figure size to twitter standard
fig.savefig('Star Trek movie ratings_dates.png', dpi=96, bbox_inches='tight')  # save figure
```
![alt text](https://github.com/rikunert/Star_Trek_ratings/raw/master/Star_Trek_movie_ratings_dates.png "The final figure")

{% include twitter_plug.html %}
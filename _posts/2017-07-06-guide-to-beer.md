---
layout: post
published: true
permalink: guide_to_beer
comments: true

title: The Rich Data guide to beer
subtitle: 200 lines of code (R)
tags: [data science, R, RStudio, untappd, beeradvocate, web scraping, visualisation, plotly, beer, modeling, choropleth,
3D, scatter plot, LOESS]
lead: I sampled millions of beer ratings from the biggest beer rating sites around in order to answer all the questions beer lovers have. What is the best beer? What makes it so good? Where are the best beers made?
---

<image src="https://github.com/rikunert/beer_rating/raw/master/ABV_IBU_UT_gif/ABV_IBU_ALL.gif"></image>

<!--excerpt-->

**Which beer is best?**

I sampled more than 128 million ratings of more than 8,000 beers from [untappd.com](http://untappd.com/)
and [beeradvocate.com](http://beeradvocate.com) in order to answer this question.
This data set certainly does not include all beers in the world.
For example, only beers present on both websites are included.
However, the analysis does reveal intriguing trends in the world of beer.

How I got my hands on this data through web-scraping is detailed in [this R-script](https://github.com/rikunert/beer_rating/blob/master/data_acquisition_untappd.R), should you be interested.
If you like the interactive graphs in this plot and want to learn how to make them yourself, scroll to the end of this post.

Let's dive right into it by answering all the varieties of the 'which beer is best' question.

# Which beer receives the highest rating and why?

In the entire data set an IPA from the US scored best (4.76 stars based on 6,704 ratings).
It's a relatively strong beer at 8.2% alcohol, placing it in the top 24% of beers according to alcohol by volume (ABV).
It is also very bitter at 85 International Bittering Units (IBU), placing it in the top 9% of beers.

I shall refrain from naming any top beers in this post as I am scared of the commercial interests involved.
However, those who know how to run three lines of code in R can get the answer here:

```r
load(url("https://github.com/rikunert/beer_rating/raw/master/UT_dat_2017-05-29.RData"))
int_dat = UT_dat[complete.cases(UT_dat[c('UT_beer_name', 'UT_brewery', 'UT_ABV', 'UT_IBU', 'UT_rating', 'UT_raters')]),]
head(int_dat[order(-int_dat[,'UT_rating']),], 10)
```

What is more interesting is whether the high ABV and IBU of the top beer is typical for top beers.
Yes and no. Feel free to play around with the figure below.
Dots represent actual beers while the semi-transparent surface represents a sort of best guess for hypothetical beers.  

<iframe width="700" height="650" frameborder="0" scrolling="no" src="//plot.ly/~rikunert/9.embed"></iframe>

You might notice that beers, i.e. the dots in the figure, are not randomly scattered around.
They tend to cluster around 6% alcohol, a light taste (low IBU) and a rating aroung 3.5 or so.
One might call such beers *typical*.

Notice how lighter, less bitter beers like light lagers tend to receive lower ratings.
The stronger, i.e. more alcoholic, the beer, the better rated it tends to be. However, this is not true across the board.
Between 5% and 10% alcohol ratings do rise quickly.
But thereafter, any additional increase in ABV hardly results in improved ratings.
The relationship between % alcohol and ratings strikes me as logarithmic: a fast rise which plateaus near an asymptote.

Bitterness interacts with this relationship between ABV and ratings.
While IBU hardly affects ratings of strong beers, it does influence weaker beers which benefit from a more characteristic taste, i.e. higher IBU.

So, the best beer, with high ABV and IBU score sort of played it double safe.
A high value on either scale, alcohol content or bitterness, would probably have been enough to ensure a high rating.

# What is the best beer which has never been brewed?

The wealth of data even allows for predictions on beers which don't exist, yet.
I modeled ratings as a function of ABV and IBU, as shown by the semi-transparent surface cutting through the dots in the aforementioned figure.
It turns out that relatively weak beers which are also very bitter should score very well.
Indeed so well that they would be off the chart.
If a brewery could produce such a beer, it might be surprised about the success it has.
I for one would certainly like to try it.  

# What kind of beer scores highest?

Looking at the average rating for each type of beer a certain kind of stout (American Imperial / Double) comes out on top.
This is true whether each rating is counted individually ('average per rating')
 or each beer within each beer type is counted individually,
  i.e. ignoring that some beers get rated more often than others ('average per beer'). 
Trends in beer brewing such as IPAs and homebrews are certainly visible in the list of favourite beer types, too.

At the other end of the scale, it might not surprise any beer lovers that non-alcoholic beers are the second least liked kinds of beers.
The complete break-down can be seen in the table.

N° | Type | average per rating | average per beer
--------|--------|----------|----------
1  |  Stout - American Imperial / Double  |  4.12  |  4.12  
2  |  Stout - Imperial Milk / Sweet  |  4.12  |  4.11       
3  |  Sour - American Wild Ale  |  4.09  |  4.10  
4  |  IPA - Triple  |  4.07  |  4.10 
5  |  Stout - Imperial / Double  |  4.07  |  4.09           
6  |  Homebrew  |  4.07  |  4.09              
7  |  Stout - Imperial Oatmeal  |  4.05  |  4.07            
8  |  Stout - Russian Imperial  |  4.03  |  4.05            
9  |  IPA - Imperial / Double  |  4.02  |  4.01             
10  |  Lambic - Other  |  4.02  |  4.01 
... |
137 | Pilsner - German  |  3.33  |  3.37
...|
154 | Non-Alcoholic  |  2.55  |  2.36                       


# Which country produces the best beer?

Switzerland! If you simply count the all the ratings for beers produced in each country, ratings of Swiss beers are the highest at 3.97 stars.
There are three Swiss breweries ([Brasserie des Franches-Montagnes](https://untappd.com/w/bfm-brasserie-des-franches-montagnes/4719),
 [Brasserie Trois Dames](https://untappd.com/BrasserieTroisDames),
  and [Brauerei Locher](https://untappd.com/BrauereiLocher)) producing a total of
seven beers in the data set.

<iframe width="700" height="650" frameborder="0" scrolling="no" src="//plot.ly/~rikunert/7.embed"></iframe>

What happens if you don't count each individual rating, but instead aggregate by beer as done for beer-types above?
Sweden wins! Followed by Norway and Denmark.

I will have to admit that I am impressed by the high quality of Nordic beers.
They match or even surpass German and Belgian beers. If only alcohol in Scandinavia wasn't so expensive.

The complete break-down of beers produced by each country:

N° | Country | average per rating | average per beer
--------|--------|----------|----------
1 | Switzerland | 3.97 | 3.78
2 | Norway | 3.82 | 3.79
3 | Sweden | 3.81 | 3.83
4 | Luxembourg | 3.80 | 3.75
5 | Belgium | 3.75 | 3.70
6 | New Zealand | 3.73 | 3.69
7 | Iceland | 3.70 | 3.71
8 | Denmark | 3.70 | 3.79
9 | United States | 3.70 | 3.72
10 | Sri Lanka | 3.62 | 3.37
... |
13 | Germany | 3.53 | 3.44

# Wait, but are beer ratings reliable?
As far as I can tell, yes. The beer ratings of one website predict those of the other website. 
Around 77% of the variability in untappd beer ratings is explained by beeradvocate beer ratings (R<sup>2</sup>). 
On average, the untappd rating is only 0.18 stars off from the beeradvocate rating (RMSE). 
Given how subjective beer appreciation can be, these are remarkably good values.

<iframe width="700" height="650" frameborder="0" scrolling="no" src="//plot.ly/~rikunert/11.embed"></iframe>

This should not be taken to mean that people do not differ in beer taste. 
Instead, the *average* beer rating is reliable. 
The supposedly different user bases of beeradvocate.com and untappd.com come to largely the same conclusion of what constitutes a good beer.

# ...and this is how I did it
This post was a substantial amount of work. But hell, I learned more about web-scraping, interactive plotting, even 3D plotting.
I used [this R-script](https://github.com/rikunert/beer_rating/blob/master/beer_rating_interactive_visualisations.R) to generate the figures. 
For those, who need a bit more hand-holding, I dive into more detail of what I did below. 
Let me know if anything is still unclear by commenting on this post.

## Interactive 3D scatter plot with plotly in R
Start off by loading plotly and the data from my github account. 
I used plotly version 4.5.6 for two reasons: 1) it shows dots in RStudio, 2) the resulting files are smaller.
You can get that version by calling `devtools::install_version("plotly", version = "4.5.6", repos = "http://cran.us.r-project.org")`.
```r
library(plotly)#version = "4.5.6"
load(url("https://github.com/rikunert/beer_rating/raw/master/UT_dat_2017-05-29.RData"))
UT_dat$ALL_rating = with(UT_dat, (UT_rating*UT_raters + BA_rating*BA_raters)/(UT_raters + BA_raters))#overall rating
UT_dat$ALL_raters = with(UT_dat, UT_raters + BA_raters)#total number of raters
int_dat = UT_dat[complete.cases(UT_dat[c('UT_beer_name', 'UT_brewery', 'UT_ABV', 'UT_IBU', 'ALL_rating', 'ALL_raters')]),]
```
Having loaded the data we have got everything in place for a 3D scatter plot. 
However, I want to include a surface of a LOESS solution in the same plot. 
So, let's prepare that by generating a LOESS model `m` and using the `predict()` function to get the predicted ratings according to this model. 
```r
m = loess(ALL_rating ~ UT_ABV * UT_IBU, data = int_dat, weights = ALL_raters)
x_marginal = seq(min(int_dat$UT_ABV), xmax, length = interp_dens)
y_marginal = seq(min(int_dat$UT_IBU), ymax, length = interp_dens)
data.fit <-  expand.grid(list(UT_ABV = x_marginal, UT_IBU = y_marginal))
pred_interp = predict(m, newdata = data.fit)#interpolated ratings    
```
Plotly is smart enough to generate the hover text from the input data frame for scatter plots.
However, the hover text for surface plots needs to be a separate variable. Is this confusing? Yes. 

```r
#hover text for surface plot (LOESS model)
hover <- with(data.frame(x = rep(x_marginal, interp_dens),
                         y = rep(y_marginal, each = interp_dens),
                         p = as.vector(pred_interp)),
              paste('Modeled beer (LOESS)', '<br>',
                    "Predicted rating: ", round(p, digits = 2), "stars", '<br>',
                    "Alcohol content: ", round(x, digits = 2), '%', '<br>',
                    'Bitterness: ', round(y, digits = 2), 'IBU'))
hover_m = matrix(hover, nrow = interp_dens, ncol = interp_dens, byrow = T)
```
Now, we finally have everything in place for the plotly call. 
```r
p <- plot_ly()
```
We first generate the scatter plot layer using `add_markers()`.
```r
p <- p %>%
  add_markers(data = int_dat, x = ~UT_ABV, y = ~UT_IBU, z = ~ALL_rating, size = ~ALL_raters,
             marker = list(symbol = 'circle', sizemode = 'area',
                           color = ~ALL_rating, colorscale = c('#708090', '#683531'), showscale = F,
                           zmin = 2, zmax = 5),
             sizes = c(50, 1000), opacity = 1,
             name = 'Beers',
             hoverinfo = 'text',
             text = ~paste('Actual beer',
                           '<br>Name: ', UT_beer_name,
                           '<br>Brewery: ', UT_brewery,
                           '<br>Type: ', UT_sub_style,
                           '<br>user rating: ', ALL_rating,
                           '<br>Untappd raters: ', ALL_raters,
                           '<br>Alcohol content: ', UT_ABV, '%',
                           '<br>Bitterness: ', UT_IBU, 'IBU'))
```
This is followed by the surface layer using `add_surface()`.
Beware, plotly expects the x-values on the columns of the matrix of predicted ratings. Notice the use of `t()`.
Confusing? Yes, it is.
 ```r
p <- p %>%
  add_surface(x = x_marginal, y = y_marginal,
              z = t(pred_interp),#add_surface() expects the x-values on the columns and the y-values on the rows (very confusing, I know)
              opacity = 0.7,
              name = 'LOESS model',
              hoverinfo = 'text',
              text = hover_m,
              showscale = F,
              colorscale = c('#708090', '#683531'),
              cmin = 2, cmax = 5)
```
Finally, we use `layout()` for making the chart pretty.
```r
p <- p %>%
  layout(title = 'How bitterness and alcohol make good beer',
         scene = list(xaxis = list(title = '% Alcohol',
                                   gridcolor = 'rgb(255, 255, 255)',#white
                                   range = c(0, xmax)),
                      yaxis = list(title = 'Bitterness',
                                   gridcolor = 'rgb(255, 255, 255)',
                                   range = c(0, ymax)),
                      zaxis = list(title = 'Rating',
                                   gridcolor = 'rgb(255, 255, 255)',
                                   range = c(1, 5))),
         annotations = list(list(x = 0, y = 0,#bottom left corner of frame
                                 text = '<a href="https://twitter.com/rikunert">@rikunert</a>',
                                 xref = 'paper', yref = 'paper',
                                 xanchor = 'left',#left aligned
                                 showarrow = F),
                            list(x = 1, y = 0,#bottom right corner of frame
                                 text = 'Source: <a href="http://untappd.com">untappd.com</a> <br>and <a href="http://beeradvocate.com">beeradvocate.com</a>',
                                 xref = 'paper', yref = 'paper',
                                 xanchor = 'right',#right aligned
                                 showarrow = F)))
```
We push the result to the plotly website. Don't forget to set up your individual plotly API credentials (https://plot.ly/r/getting-started).
The resulting plot is [here](https://plot.ly/~rikunert/9/how-bitterness-and-alcohol-make-good-beer/).
```r
plotly_POST(p, filename = "ABV_IBU_ratings")
```
## Interactive choropleth map with plotly in R
Just like above, we start off by loading plotly and the data. 
```r
library(plotly)#version = "4.5.6"
load(url("https://github.com/rikunert/beer_rating/raw/master/UT_dat_2017-05-29.RData"))
UT_dat$ALL_rating = with(UT_dat, (UT_rating*UT_raters + BA_rating*BA_raters)/(UT_raters + BA_raters))#overall rating
UT_dat$ALL_raters = with(UT_dat, UT_raters + BA_raters)#total number of raters
```
We need to aggregate the data by country. I could not find an authoritative list of country names and country codes.
Instead, what I found was a working example. 
So, I just used that. We shall see later that the country codes of the Koreas are mixed up in this file. 
I hope that whoever uses this file does not care about the Korean peninsula.
```r
ex <- read.csv('https://raw.githubusercontent.com/plotly/datasets/master/2014_world_gdp_with_codes.csv')
countries = ex$COUNTRY
```
We aggregate the data in a simple for-loop. Notice how untappd splits the UK into its nations while the map does not.
Also, we fix the Koreas problem and a few other countries.
Matching country names was simply done with a regular expression (`grep()`).
```r
map_dat = data.frame(country = ex$COUNTRY,
                     code = ex$CODE,
                     rating = rep(NaN, length(ex$COUNTRY)),
                     raters = rep(NaN, length(ex$COUNTRY)),
                     beers = rep(NaN, length(ex$COUNTRY)),
                     breweries = rep(NaN, length(ex$COUNTRY)),
                     best_beer = rep(NaN, length(ex$COUNTRY)),
                     best_brewery = rep(NaN, length(ex$COUNTRY)))

counter = 0
for(c in countries){#for each country in the world
  counter = counter + 1
  
  if(c == 'Korea, South') {
    c = 'South Korea' 
    map_dat[counter, 'code'] = 'KOR'}#curiously, the country codes of the Koreas were swapped
  if(c == 'Niger') c = ' Niger'#no Niger beers in data base, avoid confusion with Nigeria
  if(c == 'Korea, North') map_dat[counter, 'code'] = 'PRK'#curiously, the country codes of the Koreas were swapped
  if(c == 'Bahamas, The') c = 'Bahamas'
  if(c == 'United Kingdom') c = 'England|Scotland|Wales'
  
  map_dat[counter, 'rating_beermean'] = mean(UT_dat[grep(c, UT_dat$UT_loc),'ALL_rating'], na.rm = T)
  map_dat[counter, 'rating'] = weighted.mean(UT_dat[grep(c, UT_dat$UT_loc),'ALL_rating'], UT_dat[grep(c, UT_dat$UT_loc),'ALL_raters'], na.rm = T)
  map_dat[counter, 'raters'] = sum(UT_dat[grep(c, UT_dat$UT_loc),'ALL_raters'], na.rm = T)
  map_dat[counter, 'beers'] = length(unique(UT_dat[grep(c, UT_dat$UT_loc),'UT_beer_name']))
  map_dat[counter, 'breweries'] = length(unique(UT_dat[grep(c, UT_dat$UT_loc),'UT_brewery']))
}

map_dat = map_dat[!is.na(map_dat[,'rating']),]
```
The hover text for choropleth maps in plotly works very similar to the surface plot. 
So, prepare a variable within `map_dat` which holds the hover text.
```r
#hover text
map_dat$hover <- with(map_dat, paste("Country: ", country, '<br>',
                                     "Mean rating: ", round(rating, digits = 2), "stars", '<br>',
                                     "Raters: ", raters, "<br>",
                                     "Beers: ", beers, "<br>",
                                     "Breweries: ", breweries))
```
Finally, we are ready for the plotly calls. The map projection options are stored in `g` and later used in `layout()`.
```r
g <- list(
  showframe = F,
  showcoastlines = T,
  projection = list(type = 'orthographic'))

p <- plot_geo(map_dat) %>%
  add_trace(
    z = ~rating, color = ~rating, colors = c('#708090', '#d30f00'),
    text = ~hover, locations = ~code,
    marker = list(line = list(color = toRGB("grey"), width = 0.5))#light grey boundaries
  ) %>%
  colorbar(title = 'Rating', ticksuffix = ' stars') %>%
  layout(
    title = 'Which country produces the best beers in the world?',
    geo = g,
    hoverinfo = 'text',
    annotations = list(list(x = 0, y = 0,
                            text = '<a href="https://twitter.com/rikunert">@rikunert</a>',
                            xref = 'paper',
                            yref = 'paper',
                            xanchor = 'left',
                            showarrow = F),
                       list(x = 1, y = 0,
                            text = 'Source: <a href="http://untappd.com">untappd.com</a> <br>and <a href="http://beeradvocate.com">beeradvocate.com</a>',
                            xref = 'paper',
                            yref = 'paper',
                            xanchor = 'right',
                            showarrow = F)))
```
Push the result to plotly. Again, don't forget to set up your individual plotly API credentials (https://plot.ly/r/getting-started).
The resulting figure is [here](https://plot.ly/~rikunert/7/which-country-produces-the-best-beers-in-the-world/).
```r
plotly_POST(p, filename = "globe_beer_rating")
```
## Interactive 2D scatter plot with plotly in R
Just like above, we load plotly and the data. I do not believe that the precise version of plotly makes much of a difference here as long as it is in the 4.x family.
```r
library(plotly)
load(url("https://github.com/rikunert/beer_rating/raw/master/UT_dat_2017-05-29.RData"))
UT_dat$ALL_rating = with(UT_dat, (UT_rating*UT_raters + BA_rating*BA_raters)/(UT_raters + BA_raters))#overall rating
UT_dat$ALL_raters = with(UT_dat, UT_raters + BA_raters)#total number of raters
int_dat = UT_dat[complete.cases(UT_dat[c('UT_beer_name', 'ALL_rating', 'ALL_raters')]),]
```
No preparation is necessary. We can jump straight into the plotly calls. Neat, right? 
We start off by generating a plotly object using `plot_ly()` and adding markers of a 2D scatter plot using `add_markers()`. 
The code should otherwise be self-explanatory.
```r
p <- plot_ly() %>%
  add_markers(data = int_dat, x = ~BA_rating, y = ~UT_rating, size = ~ALL_raters,
              marker = list(symbol = 'circle', sizemode = 'area',
                            color = ~ALL_rating, colorscale = c('#708090', '#683531'), showscale = F,
                            cmin = 2, cmax = 5),
              sizes = c(50, 1000), opacity = 1,
              name = 'Beers',
              hoverinfo = 'text',
              text = ~paste('Beer: ', UT_beer_name,
                            '<br>beeradvocate rating: ', BA_rating, ' stars',
                            '<br>beeradvocate raters: ', BA_raters,
                            '<br>untappd rating: ', UT_rating, ' stars',
                            '<br>untappd raters: ', UT_raters))
```
We add an ideal line visualising where ratings should lie for a perfect match.
```r
p <- p %>%
  add_trace(x = c(1, 5), y = c(1, 5),
            mode = 'lines')
```
Finally, let's make the plot pretty. Notice how the 3D scatter plot embedded the axis titles within a scene argument.
 For 2D scatter plots, this is done directly in layout. Is this confusing? Yes.
 Otherwise, you might notice the extra annotation within the plot exaplaining what the line means.
```r
p <- p %>%
layout(title = 'Beer ratings largely agree across websites',
         xaxis = list(title = 'beeradvocate rating',
                                   gridcolor = 'rgb(255, 255, 255)',#white
                                   range = c(1, 5)),
         yaxis = list(title = 'untappd rating',
                                   gridcolor = 'rgb(255, 255, 255)',
                                   range = c(1, 5)),
         showlegend = FALSE,
         annotations = list(
           list(x = 1.8, y = 1.8,#bottom left corner of frame
                text = 'Ideal line:<br>untappd rating<br>=<br>beeradvocate rating',
                xanchor = 'left',#left aligned
                showarrow = T,
                ax = 100, ay = 40),
           list(x = 0, y = 0,#bottom left corner of frame
                                 text = '<a href="https://twitter.com/rikunert">@rikunert</a>',
                                 xref = 'paper', yref = 'paper',
                                 xanchor = 'left',#left aligned
                                 showarrow = F),
           list(x = 1, y = 0,#bottom right corner of frame
                                 text = 'Source: <a href="http://untappd.com">untappd.com</a> <br>and <a href="http://beeradvocate.com">beeradvocate.com</a>',
                                 xref = 'paper', yref = 'paper',
                                 xanchor = 'right',#right aligned
                                 showarrow = F)))
```
Push the result to plotly. Again, don't forget to set up your individual plotly API credentials (https://plot.ly/r/getting-started).
The resulting 2D scatter plot is [here](https://plot.ly/~rikunert/11/beer-ratings-largely-agree-across-websites/).
```r
plotly_POST(p, filename = "UT_BA_agreement")
```

{% include twitter_plug.html %}

---
layout: post
published: true
permalink: election_LSA
comments: true

title: How close are German political parties to each other? Using R to derive the latent semantic network of German election manifestos
subtitle: 150 lines of code (R)
tags: [data science, R, text mining, string, visualisation, text, politics, LSA, LSI, semantics, 3D, scatter plot, plotly]
lead: On 24 September Germans will elect a new federal parliament. In this tutorial, I text mine the main parties' election manifestos, derive the latent semantic space and visualise it to see who is closer to whom in German politics.
---

![alt text](https://github.com/rikunert/BTW2017_manifesto_analysis/raw/master/post_gif.gif
"How to explore the semantic space of the German election manifestos.")

<!--excerpt-->

**How close are German election manifestos in terms of their meaning?**

If you want to know what a text says, there is no good substitute to actually reading it.
But no one reads election manifestos. 
So, how about we ask an algorithm from computational linguistics to give us a general idea of how much the election manifestos for the upcoming German, federal parliament elections agree.

The algorithm is called Laten Semantic Analysis (sometimes called Latent Semantic Indexing).
It knows nothing about word order or indeed the meaning of words. 
It simply assumes that words which regularly appear together in text units (pragraphs in this case), mean similar things.

This idea allows the algorithm to derive hidden ('latent') meaning ('semantic') dimensions such as good-bad, agitated-calm, or gosspiy-serious.
The semantic dimensions could really be anything. It all depends on the texts you put into the analysis.

I put in the election manifesto paragraphs of the six parties which most likely will be present in the next parliament.
I limited myself to the first three latent semantic dimensions in order to be able to plot the result.
The importance of semantic dimensions declines the more you move down the list, so the first three are a natural subset to plot.

<iframe width="700" height="650" frameborder="0" scrolling="no" src="//plot.ly/~rikunert/47.embed"></iframe>

The first thing you might notice in the concentration of points near the origin (0; 0; 0). 
These tend to be short paragraphs which include so few words that the analysis wouldn't know what meaning to assign.
 So, it remains neutral.
 
By clicking on the legend entry of each party you can see  in how far it overlaps with the other parties.
Even though the new right populist AfD is widely considered to be quite removed from the German post-war consensus,
it does not stand out. Instead, the green party is the one with the most widely dispersed points, i.e. paragraphs with meanings which are far removed from all other paragraphs in the sample.
However, a note of caution. 
The greens also tend to have the longest paragraphs, facilitating a more extreme placement. 
The AfD, on the other hand, tends to have far shorter paragraphs and, thus, 
the latent semantic analysis probably underestimates its radicalism.
 
Finally, if you hide all dots and only concentrate on the plus-signs you are essentially looking at the average meaning of the election manifesto paragraphs of each party.
There are different ways to look at this but what I see are six well-spaced markers, meaning the next German parliament will most likely represent a healthy mix of different points of view.

Out of curiosity, I increased the dimension count to 10 and looked for each party what other party is closest to it.
The following table lists the distances in arbitrary semantic units and it orders them from closest to farthest.

 CDU/CSU | SPD | Die Linke | FDP | Grüne | AfD 
--------|----------|----------|----------|----------|----------
 AfD (0.010) | AfD (0.011) | SPD (0.014) | AfD (0.028) | SPD (0.013) | CDU/CSU (0.010)
 SPD (0.014) | Grüne (0.013) | AfD (0.016) | SPD (0.028) | CDU/CSU (0.018) | SPD (0.011)
  Grüne (0.018) | CDU/CSU (0.014) | CDU/CSU (0.022) | Grüne (0.030) | AfD (0.021) | Die Linke (0.016)
  Die Linke (0.022) | Die Linke (0.014) | Grüne (0.022) | CDU/CSU (0.031) | Die Linke (0.022) | Grüne (0.021)
  FDP (0.031) | FDP (0.028) | FDP (0.035) | Die Linke (0.035) | FDP (0.030) | FDP (0.028)

The one thing to take away from this table is that the analysis I did is very, very limited. 
The right populist AfD lies the closest to three other parties while the establishment, pro-business FDP lies the farthest from all parties?
I am not sure this makes much sense to me.

The take-home message is: **have fun with this analysis but take it all with a grain of salt**. 
A better approach would have been to train the analysis on a huge corpus of German text (which I don't have)
 using very generous computer resources (which I am unwilling to pay for) and then *fold* the election manifestos into the learned semantic dimensions.

As always, in the following I provide you with a fool proof guide to recreating the analyses and plot the results. 
The script I actually used can be found on github [here](https://github.com/rikunert/BTW2017_manifesto_analysis/blob/master/BTW_LSA.R). 
Please comment below if anything should be unclear or you want to lift my spirits.
 
## Performing Latent Semantic Analysis in R
It's usually best to load the necessary libraries first. 
Should you not have one of these downloaded, use the `install.packages()` function, e.g. `install.packages('plotly')`.

```R
library(tm)#text mining
library(stringr)#word counts
library(lsa)#latent semantic analysis
library(plotly)#interactive plotting
```
Next we will load the data. 
I went through the election manifesto pdfs by hand and turned them into machine readable text files.
In order to read them as intended, we need the little helper function `readManifesto()`.
```R
readManifesto = function(manifesto_address){
  #read a political manifesto separating paragraphs into list entries
  line_break = '<<>><<>>'
  m = paste(readLines(manifesto_address), collapse = line_break)
  m = strsplit(m, split = paste(rep(line_break, 2), collapse = ''), fixed = T)
  m = gsub(line_break, ' ', m[[1]])
}
```
We load the txt files from github using the `readManifesto()` function which we just defined.
Every election manifesto is now in a party specific vector.
 We put these vectors together in a data frame `df` and remove paragraphs which are too short, i.e. shorter than `minimal_paragraph_length`.
 The number I chose is completely arbitrary.
```R
wd = 'https://raw.githubusercontent.com/rikunert/BTW2017_manifesto_analysis/master/cleaned_manifestos'

afd = readManifesto(paste(wd, "afd_cleaned.txt", sep = '/'))
cdu = readManifesto(paste(wd, "cdu_cleaned.txt", sep = '/'))
die = readManifesto(paste(wd, "die_cleaned.txt", sep = '/'))
fdp = readManifesto(paste(wd, "fdp_cleaned_ANSI.txt", sep = '/'))
gru = readManifesto(paste(wd, "gru_cleaned_ANSI.txt", sep = '/'))
spd = readManifesto(paste(wd, "spd_cleaned.txt", sep = '/'))

#remove too short paragraphs and put it all together
minimal_paragraph_length = 12#defined in words

df <- data.frame(text = c(afd[str_count(afd, '\\w+') > minimal_paragraph_length],
                          cdu[str_count(cdu, '\\w+') > minimal_paragraph_length],
                          die[str_count(die, '\\w+') > minimal_paragraph_length],
                          fdp[str_count(fdp, '\\w+') > minimal_paragraph_length],
                          gru[str_count(gru, '\\w+') > minimal_paragraph_length],
                          spd[str_count(spd, '\\w+') > minimal_paragraph_length]),
                 view = factor(c(rep('AFD', sum(str_count(afd, '\\w+') > minimal_paragraph_length)),
                                 rep('CDU/CSU', sum(str_count(cdu, '\\w+') > minimal_paragraph_length)),
                                 rep('Die Linke', sum(str_count(die, '\\w+') > minimal_paragraph_length)),
                                 rep('FDP', sum(str_count(fdp, '\\w+') > minimal_paragraph_length)),
                                 rep('Bündnis 90/Die Grünen', sum(str_count(gru, '\\w+') > minimal_paragraph_length)),
                                 rep('SPD', sum(str_count(spd, '\\w+') > minimal_paragraph_length))
                 )),
                 stringsAsFactors = FALSE)

#How many paragraphs included
summary(df$view)
```
Having loaded the data we can turn to Laten Semantic Analysis (LSA).
We prepare a corpus `corp` using the `Corpus()` function, turn that into a term document matrix while performing basic data cleaning (punctuation removal etc.).
For some reason this has to be turned into an ordinary matrix `td.mat` with `as.matrix()` in order to apply a term weighting scheme and then perform the actual LSA.

The weighting scheme I chose penalizes extremely common words, e.g. *der*, *die*, *das*, and words which are so rare that they only occur in very few paragraphs.
The words which LSA can work with (which occur across paragraphs) and which are typical for that paragraph (which don't occur everywhere) get more weight.

Beware that the `lsa()` call will require patience. I only grab 10 dimensions from the analysis, because I am not interested in the rest.
I believe that choosing more does not change the values associated with the first ten dimensions.
```R
#prepare corpus
corp <- Corpus(VectorSource(df$text),
               readerControl = list(language = 'german'))

#turn corpus into TDM
corp <- TermDocumentMatrix(corp, control = list(removePunctuation = TRUE,
                                                stopwords = T,#I am not sure this works well in German
                                                tolower = T,
                                                stemming = T,#I am not sure this works well in German
                                                removeNumbers = TRUE))

td.mat <- as.matrix(corp)

td.mat.lsa <- lw_bintf(td.mat) * gw_gfidf(td.mat)  # weighting: global frequency * inverse document frequency
lsaSpace <- lsa(td.mat.lsa, dims = 10)  # create LSA space
```
And so we are ready for some 3D scatter plot action.
I want the user to be able to hover the cursor over a data point and see which paragraph it is, i.e. directly read the text.
Because `plotly` does not implement line breaks, I have to do it myself.

```R
br_max = 90#maximal number of characters before line break

for (i in 1:length(df$text)){#for each paragraph
  paragraph = df$text[i]
  spaces = str_locate_all(pattern =' ', paragraph)[[1]][,1]#locate where spaces are in paragraph
  br_idx = unique(unlist(lapply(br_max * 1:ceiling(nchar(paragraph) / br_max),
                                function(x) max(spaces[spaces < x]))))#locate future line breaks
  
  #implement line breaks
  paragraph_sep <- unlist(strsplit(paragraph,""))#split string into separate characters
  paragraph_sep[br_idx] <- '<br>'#replace characters where line break should occur with html line break
  df$text[i] = paste0(paragraph_sep,collapse='')#put the string back together
  
}
```
Prepare the data frame `plotly` expects to get. Include, besides the coordinates `x`, `y`, `z`, also the party `c` and the hover text `t`.
As always, `R` reorganises factor levels alphabetically. The last line enforces the order we want for `c`.

```R
#data frame for plotting paragraphs
points <- data.frame(x = lsaSpace$dk[,1],
                     y = lsaSpace$dk[,2],
                     z = lsaSpace$dk[,3],
                     c = df$view,
                     t = df$text)

#get colours of plot right
party_colours = c('black', 'red', 'magenta 3', 'orange', 'dark green', 'blue')
party_names = c("CDU/CSU", "SPD", "Die Linke", "FDP", "Bündnis 90/Die Grünen", "AFD")
points$c <- factor(points$c,levels= party_names) # add publication ready labels
```
We do something very similar for the centroids, i.e. the average coordinates of a given party's paragraphs.
Calculating a centroid is done easily enough by taking the mean along each dimension.
We simply loop through dimensions. 
I have the weird feeling that there is a more elegant version out there which uses tidyverse code.
If it is faster and shorter than 5 lines of code, please tell me!

If you are interested in the euclidean distances between centroids in the ten-dimensional space, just run the `dist()` statement I commented out.
Finally, tell `R` again that we do not want alphabetically ordered factor levels.
```R
centroids = matrix(NA, nrow =10, ncol =6)
colnames(centroids) = party_names
for(i in 1:10){#for each LSA dimension
  centroids[i,] = unlist(lapply(party_names, function(x) mean(lsaSpace$dk[df$view == x,i])))
}

#dist(t(centroids))#the distances between party centroids

points_centroids <- data.frame(x = centroids[1,],
                     y = centroids[2,],
                     z = centroids[3,],
                     c = paste(party_names, ' centroid'))
points_centroids$c <- factor(points_centroids$c,levels= paste(party_names, ' centroid')) # add publication ready labels
```
Now we have everything in place to start plotting. First things first: the paragraphs and centroids in 3D semantic space.
`plotly` somehow does not allow independent color specification between `add_markers()` calls. 
So, we give it the same list of colours twice, once for the paragraphs and once for the centroids, but all in the first `add_markers()`. 
I know this is confusing.
```R
p <- plot_ly() %>%
  add_markers(data = points, x = ~x, y = ~y, z = ~z, color = ~c,
              colors = rep(party_colours, 2),#requires repeating because plotly doesn't link paragraph and centroid legend entries
              hoverinfo = 'text',
              text = ~paste(c, ':<br>', t)) %>%
  add_markers(data = points_centroids, x = ~x, y = ~y, z = ~z, color = ~c,
              hoverinfo = 'text',
              text = ~paste(c),
              marker = list(symbol = 'cross'))
```
We now have a very basic 3D scatter plot which the following `layout()` call will prettify.
I am particularly happy with the legend title hack I found online somewhere. 
Both one annotation (legend title) and the legend itself have the same y-position (height on page).
However, the annotation is just above it because `yanchor = 'bottom'` while the legend is just below because `yanchor = 'top'`.
```R
p <- p %>%
  layout(scene = list(xaxis = list(title = 'Semantic<br>Dimension 1'),
                      yaxis = list(title = 'Semantic<br>Dimension 2'),
                      zaxis = list(title = 'Semantic<br>Dimension 3')),
         title = 'The semantic space of German election manifestos',
         annotations = list(list(x = 0, y = 0,#bottom left corner of frame
                                 text = '<a href="https://twitter.com/rikunert">@rikunert</a>',
                                 xref = 'paper', yref = 'paper',
                                 xanchor = 'left',#left aligned
                                 showarrow = F),
                            list(x = 1.02, y = 0.8,
                                 text = 'Click to show/hide',
                                 xanchor = 'left', yanchor = 'bottom',
                                 showarrow = F)),
         legend = list(y = 0.8, yanchor = 'top'))
#p#privately have a look at the plot in browser
```
Happy with what you see when calling `p`? Then push it to your own plotly account using `plotly_POST()`.
Don't forget to set up your credentials in your `.Rprofile`.
```
plotly_POST(p, filename = "BTW2017_manifesto_LSA")#push plotly post to plotly website # Set up API credentials: https://plot.ly/r/getting-started
```
{% include twitter_plug.html %}
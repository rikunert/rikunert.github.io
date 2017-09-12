---
layout: post
published: false
permalink: election_LSA
comments: true

title: How close are German political parties to each other? Using R to derive the latent semantic network of German election manifestos
subtitle: 150 lines of code (R)
tags: [data science, R, text mining, string, visualisation, text, politics, LSA, LSI, semantics, 3D, scatter plot, plotly]
lead: On 24 September Germans will elect a new federal parliament. In this tutorial, I text mine the main parties' election manifestos, derive the latent semantic space and visualise it to see who is closer to whom in German politics.
---

![alt text](address to gif
"What the main German political parties are talking about in their manifestos")

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

[insert figure here]

The first thing you might notice in the concentration of points near the origin (0; 0; 0). 
These tend to be short paragraphs which include so few words that the analysis wouldn't know what meaning to assign.
 So, it remains neutral.
 
By clicking on the legend entry of each party you can see  in how far it overlaps with the other parties.
Even though the new right populist AfD is widely considered to be quite removed from the German post-war consensus,
it does not stand out. Instead, the green party is the one with the most widely dispersed points, i.e. paragraphs with means which are far removed from all other paragraphs in the sample.
However, a note of caution. The green also tend to have the longest paragraphs, facilitating a more extreme placement. 
The AfD, on the other hand, tends to have far shorter paragraphs and, thus, 
the latent semantic analysis probably underestimates its radicalsm.
 
Finally, if you hide all dots and only concentrate on the plus-signs you are essentially looking at the average meaning of the election manifesto paragraphs of each party.
There are different ways to look at this but what I see are six well-spaced markers, meaning the next German parliament will most likely represent a healthy mix of different points of view.

Out of curiosity, increased the dimension count to 10 and looked for each party what other party is closest to it.
The following table lists the distances in arbitrary semantic units and it orders them from closest to farthest.

 CDU/CSU | SPD | Die Linke | FDP | Grüne | AfD 
--------|----------|----------|----------|----------|----------
 AfD (0.010) | AfD (0.011) | SPD (0.014) | AfD (0.028) | SPD (0.013) | CDU/CSU (0.010)
 SPD (0.014) | Grüne (0.013) | AfD (0.016) | SPD (0.028) | CDU/CSU (0.018) | SPD (0.011)
  Grüne (0.018) | CDU/CSU (0.014) | CDU/CSU (0.022) | Grüne (0.030) | AfD (0.021) | Die Linke (0.016)
  Die Linke (0.022) | Die Linke (0.014) | Grüne (0.022) | CDU/CSU (0.031) | Die Linke (0.022) | Grüne (0.021)
  FDP (0.031) | FDP (0.028) | FDP (0.035) | Die Linke (0.035) | FDP (0.030) | FDP (0.028)

The one thing to take away from this table is that the analysis I did is very, very limited. 
The right populist AfD lies the closest to three other parties while the establishment, pro-business FDP lies the farthes for all other parties?
I am not sure this makes much sense to me.

The take-home message is: **have fun with this analysis but take it all with a grain of salt**. 
A better approach would have been to train the analysis on a huge corpus of German text (which I don't have)
 using very generous computer resources (which I am unwilling to pay for) and then *fold in* the election manifestos into the learned semantic dimensions.

As always, in the following I provide you with a fool proof guide to recreating the analyses and plot the results. 
The script I actually used can be found on github [here](https://github.com/rikunert/BTW2017_manifesto_analysis/blob/master/BTW_LSA.R). 
Please comment below if anything should be unclear or you want to lift my spirits.
 
## Performing Latent Semantic Analysis in R
As always, it's best to load the necessary libraries first. 
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
We load the txt files using the `Corpus()` function and then turn the corpus into a so-called term document matrix which represents words as rows and documents in which these words occur as columns. 
Cell entries are word frequency counts.
The second step is combined with some text pre-processing (turning all to lower case, removing stop words, etc.)
```R

```

```R

```


```R

```


```R

```


```R

```


```R

```


## Interactive 3D Scatter Plot in R

Given the variable length of the manifestos, we don't want to give a party an advantage for just writing a lot.
So, we adjust word frequencies by how many words are in the document using the `prop.table()` function.
This is also a good moment to get proper column headers saved in `party_names`.
```R
ft_matrix_prop = prop.table(as.matrix(txt.tdm), margin = 2)#column proportions
party_names = c("CDU/CSU", "SDP", "Die Linke", "FDP", "Grüne", "AFD")
colnames(ft_matrix_prop) <- party_names # add publication ready column labels
```
And that's it in terms of data preparation. We can start plotting using the `wordcloud` package.
The first plot showing which party dominates which word uses traditional party colours defined in `party_colours` and the `comparison_cloud()` function.
Note that the maximum word count is not per party but instead for the whole plot.
We add the caption into the margin using `R`'s `mtext()` function. 
Side 1 is the bottom and lines are counted from the inside out.
```R
if(!require(wordcloud)){install.packages('wordcloud')}
library(wordcloud)

party_colours = c('black', 'red', 'magenta 3', 'orange', 'dark green', 'blue')

comparison.cloud(ft_matrix_prop, max.words = 200, random.order = FALSE,
                 colors = party_colours, title.size = 1.5)
mtext('@rikunert', side = 1, line = 4, adj = 0) # caption
```
The party specific word clouds follow the same principle. 
However, in order to plot all words, the word size had to be reduced through the `scale` parameter.
  I added a title using `R`'s `mtext()` function to unambiguously show which party is meant.
```R
for (i in 1:length(party_names)) { 
  wordcloud(rownames(ft_matrix_prop), ft_matrix_prop[,i],
            min.freq = 0.001, max.words = 100,
            colors = party_colours[i],
            scale = c(3.2, .4))
  mtext('@rikunert', side = 1, line = 4, adj = 0) # caption
  mtext(party_names[i], side = 3, line = 3, adj = 0.5) # title
}
```
{% include twitter_plug.html %}
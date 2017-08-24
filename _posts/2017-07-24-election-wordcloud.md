---
layout: post
published: true
permalink: election_wordcloud
comments: true

title: Using R to derive the German election manifesto word clouds
subtitle: 60 lines of code (R)
tags: [data science, R, text mining, string, visualisation, text, politics]
lead: In just one month the biggest country of Europe, Germany, is going to the polls. In this short tutorial, I text mine the main parties' election manifestos in order to visualise the state of German politics.
---

![alt text](https://github.com/rikunert/HU_text_classification/raw/master/Wahlprogramme_2017/German_party_manifesto_wordcloud.png
"What the main German political parties are talking about in their manifestos")

<!--excerpt-->

**What themes preoccupy German politics?**

I do not know a single human being who reads election manifestos.
In order to nonetheless get a feeling for what they talk about, I text mined the current German general election manifestos. 
The general result is summarised in the above word cloud showing which party dominates which word.

Words are sized by how often they occur across election manifestos and coloured according to the manifesto where they occur the most.
The colours will be familiar to most readers but just in case I included party names as well.

The word clouds for each political party reveal what different themes. 
I plot the top 100 words of each manifesto and scale the words by how often they occur.

![alt text](https://github.com/rikunert/HU_text_classification/raw/master/Wahlprogramme_2017/CDU_CSU_wordcloud.png
"What Angela Merkel's CDU/CSU are talking about in their manifesto")

The ruling conservative parties CDU/CSU of chancelor Merkel appear to focus on Germany (*deutschland*) and to a lesser extent Europe (*europa*).
 Otherwise, community related words (*menschen*, *unser*, *unserer*) are noticeable.

 ![alt text](https://github.com/rikunert/HU_text_classification/raw/master/Wahlprogramme_2017/SPD_wordcloud.png
"What Angela Merkel's CDU/CSU are talking about in their manifesto")

The other ruling party, the social democratic SPD, appear to fail to find a strong verbal focus, instead opting to talk more or less equally about Germany (*deutschland*) and Europe (*europa*).
 The relatively low frequency of social justice (*Gerechtigkeit*) is noticeable given that the SPD tried to make this the theme of this election.
 
![alt text](https://github.com/rikunert/HU_text_classification/raw/master/Wahlprogramme_2017/LINKE_wordcloud.png
"What the post-socialist DIE LINKE are talking about in their manifesto")

The currently biggest opposition party, the far left DIE LINKE, talk about 'having to do something' (*müssen*) and human beings (*menschen*) more than other parties. 
However, they failed the competition for promising 'more' (*mehr*) to the social democrats. 
Otherwise, welfare state vocubulary appears quite common (*solidarische*, *sozial(en)*, *pflege*, *arbeit*). 

![alt text](https://github.com/rikunert/HU_text_classification/raw/master/Wahlprogramme_2017/Gruene_wordcloud.png
"What the green party, Bündnis 90 / Die Grünen, are talking about in their manifesto")

People (*menschen*), the future (*zukunft*), and Germany (*deutschland*), seem to be the most common words for the greens.
Surprisingly, environment related words (*ökologische*) hardly make it into the top 100 words displayed in the word cloud.

![alt text](https://github.com/rikunert/HU_text_classification/raw/master/Wahlprogramme_2017/FDP_wordcloud.png
"What the liberal FDP are talking about in their manifesto")

The pro-business liberal FDP is likely to join the federal parliament again after a four year hiatus. 
Expectedly, they use business vocabulary a lot (*leistungen*, *unternehmen*, *wirthschaft*, *wettbewerb*). 
Perhaps in an attempt to come across as a modern party a lot of computer vocabulary is included as well (*digitalisierung*, *digitalen*, *daten*).

![alt text](https://github.com/rikunert/HU_text_classification/raw/master/Wahlprogramme_2017/AFD_wordcloud.png
"What the right populist AFD are talking about in their manifesto")

The right populist AFD are widely expected to enter the federal parliament for the first time this autumn. 
Their excitement about this prospect appears to be mirrored in the frequent mention of 'general election' (*bundestagswahl*).
They unexpectedly lose the competition to mention Germany (*deutschland*) the most to Merkel's CDU/CSU. 
Notice that vocabulary related to foreigners coming to Germany makes it into their word cloud (*asyl*, *islam*, *zuwanderung*, *türkei*).

As an aside, a certain Martin Schweinberger did a similar project for the last German general election published on [his blog](http://www.martinschweinberger.de/blog/creating-word-clouds-with-r/).
Compare his word cloud to mine and see how the German political discourse has changed. 
For example, 4 years ago Germany (*deutschland*) was mentioned a lot less and the liberal FDP dominated that word. A far cry from the situation in 2017.

In the following I show how you can make these plots from the comfort of your own home.
The full script can be found [on github](https://github.com/rikunert/HU_text_classification/blob/master/Wahlprogramme_2017/BTW_wordcloud.R).

## Making word clouds with R

We start off by acquiring the election manifestos directly from the party websites.
 We put the urls in `maifestos_pdf` and then loop through them using `sapply`. 
 `sapply` uses a nameless function defined on the spot. 
 That function prepares a location `dest` in the tmp folder where the manifestos will be stored.
 It then downloads the manifesto of this loop iteration and uses `pdftotext.exe` (which you should download before) in order to turn the pdf into a txt file whose name is returned by the function through its last statement.

```R
manifestos_pdf = c('https://www.cdu.de/system/tdf/media/dokumente/170703regierungsprogramm2017.pdf',
                   'https://www.spd.de/fileadmin/Dokumente/Bundesparteitag_2017/Es_ist_Zeit_fuer_mehr_Gerechtigkeit-Unser_Regierungsprogramm.pdf',
                   'https://www.die-linke.de/fileadmin/download/wahlen2017/wahlprogramm2017/die_linke_wahlprogramm_2017.pdf',
                   'https://www.fdp.de/sites/default/files/uploads/2017/08/07/20170807-wahlprogramm-wp-2017-v16.pdf',
                   'https://www.gruene.de/fileadmin/user_upload/Dokumente/BUENDNIS_90_DIE_GRUENEN_Bundestagswahlprogramm_2017.pdf',
                   'https://www.afd.de/wp-content/uploads/sites/111/2017/06/2017-06-01_AfD-Bundestagswahlprogramm_Onlinefassung.pdf')

#download and install pdftotext.exe

files_txt = sapply(manifestos_pdf, function(x) {
  dest <- tempfile(pattern = substr(x, 13, 15), fileext = ".pdf") # prepare a tmp file which includes party in name
  download.file(x, dest, mode = "wb") # fill the tmp file with a downloaded election manifesto
  system(paste('"C:\\Program Files\\xpdf-tools-win-4.00\\bin64\\pdftotext.exe" -layout ', # make windows use this pdf-to-txt program
               "\"", dest, "\"", sep = ""), # on this election manifesto
         wait = F)
  sub(".pdf", ".txt", dest)}) #return name of final txt file
```
These txt files allow us to do some text mining with the R library `tm`.
```R
if(!require(tm)){install.packages('tm')}
library(tm) # text mining
```
We load the txt files using the `Corpus()` function and then turn the corpus into a so-called term document matrix which represents words as rows and documents in which these words occur as columns. 
Cell entries are word frequency counts.
The second step is combined with some text pre-processing (turning all to lower case, removing stop words, etc.)
```R
txt <- Corpus(URISource(files_txt),
              readerControl = list(reader = readPlain, language = 'german'))

txt.tdm <- TermDocumentMatrix(txt, control = list(removePunctuation = TRUE,
                                                  stopwords = T,#I am not sure this works well in German
                                                  tolower = T,
                                                  stemming = F,#disabled to get full words
                                                  removeNumbers = TRUE,
                                                  bounds = list(global = c(3, Inf))))#only words mentioned at least three times across manifestos
```
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
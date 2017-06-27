---
layout: post
published: true
permalink: grade_discrimination
comments: true

title: Germany discriminates against foreign grades
subtitle: 280 lines of code (R)
tags: [data science, R, visualisation, ggplot2, grades, discrimination]
lead: There are more than 23,000 Germans studying in the Netherlands. Many of them don’t realise that back in Germany they will be penalised. The reason is foreign grade discrimination. What can be done about it?
---

![alt text](https://github.com/rikunert/german_grade_discrimination/raw/master/NL_dis_ex.png "Discrimination of Dutch grades in Germany")

<!--excerpt-->

**How does Germany penalise students from abroad?**

TL;DR: by using the so-called 'modified Bavarian formula'.
It is enshrined in a little known [agreement](http://www.kmk.org/fileadmin/Dateien/pdf/ZAB/Hochschulzugang_Beschluesse_der_KMK/GesNot04.pdf) between the education ministers of the German federal states. It looks perfectly innocent:
![alt text](https://github.com/rikunert/german_grade_discrimination/raw/master/Bavarian_formula.png "Bavarian formula")

However, its effect can be highly discriminatory. Take the case of the Netherlands, the second most prominent destination for Germans studying abroad.
I used to be one such German student in the Netherlands when I did my Master's degree in Amsterdam.
Many Germans I spoke to in the Netherlands told me that they got stuck there.
They couldn't go back to German universities because their Dutch grades would not count for much.
They would miss entry requirements which, they felt, they should really pass.

In this post, I will have a look at whether this subjective sense of discrimination is at all justified and, if so, what universities can do to avoid it.
As always, the code for recreating all figures is shared and explained below.

## Quantifying foreign grade discrimination in Germany

As an example, let's take my own Dutch average grade of 8.7, an outstanding grade on the Dutch scale ranging from 10 (best) to 5.5 (worst pass grade).
When applying the aforementioned, official formula for turning foreign grades into German grades, the impressive Dutch 8.7 becomes a meagre 1.9 on the German scale ranging from 1 (best) to 4 (worst pass grade). A German 1.9 is still a good but no longer an outstanding grade.

In order to quantify this feeling of 'good' versus 'outstanding', I looked for population data showing me how many people receive each grade.
I use school grades because they are a) not selective (everybody goes to school), and b) general (every subject and school uses the same scale).

The figure below, based on this data [here](https://www.nuffic.nl/publicaties/vind-een-publicatie/cijfervergelijking-examencijfers.pdf), shows that a Dutch GPA of 8.7 places me in the top 1% of the population.
However, on the right, I show how the translation to German grades suddenly places me only in the top 24% of students according to this data [here](https://www.kmk.org/fileadmin/Dateien/pdf/Statistik/Aus_Abiturnoten_2006_2013.zip).
The top 1% of students in Germany receive a grade of 1.0, not the 1.9 my Dutch 8.7 got translated to.

![alt text](https://github.com/rikunert/german_grade_discrimination/raw/master/NL_dis_ex.png "Discrimination of my GPA in Germany")

It turns out my GPA of 8.7 is not a weird aberration of an otherwise fair formula. Far from it.
Below, I plot the fate of every Dutch grade before (red) and after (blue) being translated to the German scale.
The grey gap between the red curve and the blue curve reflects the discrimination of Dutch grades in Germany.

![alt text](https://github.com/rikunert/german_grade_discrimination/raw/master/NL_dis_overall_official.png "Discrimination of every Dutch grade in Germany")

As you can see, only extreme (left and right most) Dutch grades get adequately translated. Any other Dutch grade looks worse, i.e. higher (further away from 'best'), after the translation.
My own case, which I annotated in black, is not even the most extreme example.

## Avoiding foreign grade discrimination in Germany

I see two ways for avoiding foreign grade discrimination. Either one tweaks the translation formula or one abandons it.

RWTH Aachen university has chosen the first approach.
It advises students to regard a Dutch 8.5 as the maximal grade, rather than the actual 10.
Essentially, the adjustment consists of moving the blue line 1.5 units to the right.
The effect is a more or less adequate overlap between the blue curve and the red curve, i.e. little to no discrimination.

![alt text](https://github.com/rikunert/german_grade_discrimination/raw/master/NL_dis_overall_RWTH.png "The RWTH Aachen adjustment to Dutch grade translations")

However, the RWTH Aachen adjustment is specific to the Dutch case. 
If you want to generalise grade translations you will have to abandon the formula approach.
The reason is simple: grades are distributed very, very differently across countries. 
It is too much to ask of a formula to take all these differences into account.

In general, whenever the highest possible grade is deliberately beyond reach, the translation formula discriminates.
The Dutch system is a case in point and, it turns out, not an isolated case. 
France handles a similar system with grades ranging from a near-impossible 20 (best) to 10 (lowest pass grade).

![alt text](https://github.com/rikunert/german_grade_discrimination/raw/master/FR_dis.png "The French grading scale _ Baccalauréat moyenne 2016")

As a result, French grades get discriminated in Germany in much the same way as Dutch grades.

![alt text](https://github.com/rikunert/german_grade_discrimination/raw/master/FR_dis_overall_official.png "The French Baccalauréat translated to German grading: discrimination of foreign grades")

The translation formula can discrimiate in the opposite way, too. 
That is, it can confer an unfair advantage to holders of foreign grades.
This happens when the foreign grading scale has a very common best grade.
One example is the Scottish educational system.

![alt text](https://github.com/rikunert/german_grade_discrimination/raw/master/SCOT_dis.png "The Scottish grading scale _ Scottish advanced highers")

A Scottish A is the best grade and also the most common grade for an Advanced Higher course.
As a result, the translation formula spectacularly fails in the opposite way to the Dutch and French cases.

![alt text](https://github.com/rikunert/german_grade_discrimination/raw/master/SCOT_dis_overall_official.png "The French Baccalauréat translated to German grading: discrimination of foreign grades")

A new tweak to the translation formula for every foreign grading system would be necessary in order to keep the translations fair.
A single formula cannot account for the vast differences in grading scales across the world.

A better way would be to simply abandon the use of a formula and directly operate with an objective measure of how good a student is according to the result s/he receives.
 Such a measure would be the 'top X%' I used in this blog post. 
 The [Dutch Nuffic]((https://www.nuffic.nl/publicaties/vind-een-publicatie/cijfervergelijking-examencijfers.pdf)) and the [UK's Naric](National Recognition Information Centre) had the same idea and made grade distributions available for many countries.
 The right information is out there, I hope this blog post convinces you why it is important to use it.
  
European integration means more and more people receive grades in different educational systems.
Germany, the biggest country in Europe, cannot afford to turn the best foreign minds away because it doesn't know how to interpret its grades.
A better, fairer way is possible.
Let's adopt it.

## Distribution plotting in R using ggplot2

The regular reader of this blog will not encounter many surprises here. 
Arguably, the biggest challenge I faced was the grey shading area in the plots above.
So, what follows is fairly basic ggplot2 use. The full script is [on github](https://github.com/rikunert/german_grade_discrimination/blob/master/grade_discrimination_vis.R).

We start off by loading the packages.
```r
if(!require(ggplot2)){install.packages('ggplot2')}# visualisation library
library(ggplot2)

if(!require(scales)){install.packages('scales')}# percent axis labels
library(scales)

if(!require(gridExtra)){install.packages('gridExtra')}#for plotting
library(gridExtra)

if(!require(openxlsx)){install.packages('openxlsx')}#for handling excel files
library(openxlsx)
```
I like my plots in a certain way: as clean as possible. All plots will follow this theme.
```r
theme_set(theme_bw(14)+#number refers to font size
            theme(axis.line = element_line(colour = "black"),
                  panel.grid.major = element_blank(),
                  panel.grid.minor = element_blank(),
                  panel.border = element_blank(),
                  plot.title = element_text(hjust = 0.5, face="bold"),
                  legend.position = 'none'))
```
Because I made the bar charts showing grade distributions in four countries, I decided to just create one general function `dis_plot()` to plot these distributions.
```r
dis_plot = function(
  dis_dat,#the data whose distribution we will plot (data frame with columns prop, and grades)
  xlabel = 'Abitur grade',
  xticklabels = dis_dat$grades,#customisable x-axis labels
  caption_text = 'Source: Kultusminister Konferenz, 2015',#true for German Abitur distribution 
  percentiles = list(c(0, 0), c(0, 0)),#the percentiles which get highlighted first red, then blue; c(0,0) means no highlight
  bar_colours = c("#000000", "#c00000", '#0000CD'),#first colour is non-highlight (black), red, blue
  high_text = c(' ', ' '),#The text of the highlight, first red then blue
  title_text = ' '){#plot title
  ```
  Just from the arguments the function takes you can already see that it will allow for customisable highlights and labels.
  The function starts off with turning 'share in population' encoded in `dis_dat$prop` into cumulative proportions, i.e. top X%, in `dis_dat$perc.`
  We need the latter to know where to put highlights encoded in `percentiles`.
  If the user wishes to include highlighted bars, this is encoded in `dis_dat$highlight`.
  Notice how the `dis_dat$highlight` entries are in the alphatetical order '-', 'correct', and 'false', corresponding to the colour order in `bar_colours`.
  Finally, ggplot2 loves to re-order factors alphabetically. Let's *not* do that here with `dis_dat$grades`.
  ```r
dis_dat$perc = cumsum(dis_dat$prop)#cumulative proportion
  dis_dat$highlight = rep('-', length(dis_dat$prop))
  if(percentiles[[1]][1] != 0 || percentiles[[1]][2] != 0){
    dis_dat$highlight[dis_dat$perc >= percentiles[[1]][1] & dis_dat$perc <= percentiles[[1]][2]] = 'correct'
  }
  if(percentiles[[2]][1] != 0 || percentiles[[2]][2] != 0){
    dis_dat$highlight[dis_dat$perc >= percentiles[[2]][1] & dis_dat$perc <= percentiles[[2]][2]] = 'false'
  }
  dis_dat$grades = factor(dis_dat$grades, levels = dis_dat$grades)#preserve factor order
```
And we are ready to start plotting. Beware that the `scale_y_continuous(labels = percent)` command requires the library `scales`.
I increase the y-axis' upper limit to have space for the text explaining the highlights.
```r
  D = ggplot() +
    geom_bar(data = dis_dat, aes(x = grades, y = prop, fill = highlight), stat="identity") +
    labs(y="Share of population", x=xlabel) +
    scale_y_continuous(labels = percent, limits = c(0, max(dis_dat$prop) + max(dis_dat$prop)/ 2.5)) +
    scale_x_discrete(breaks = xticklabels,
                     labels = xticklabels) +
    scale_fill_manual(values=bar_colours) +
    ggtitle(title_text) +
    labs(caption = caption_text) + 
    theme(plot.caption = element_text(size = 10, color = 'grey', face= 'italic'))
 ```
 Finally, just add the highlights and we are done.
 The highlights are a horizontal line with ticks on either end and explanatory text.
 Unfortunately, I couldn't get the line to work with a single `geom_errorbarh()` command, so I had to turn to `geom_segment()` and literally draw horizontal and vertical lines.
 ```r
  #add line and annotation of highlight [[1]]
  if(percentiles[[1]][1] != 0 || percentiles[[1]][2] != 0){
    y1 = max(dis_dat$prop) + max(dis_dat$prop)/ 3#line height
    x1 = mean(which(dis_dat$perc >= percentiles[[1]][1] & dis_dat$perc <= percentiles[[1]][2]))#horizontal line midpoint
    e1 = 0.4 + (x1 - min(which(dis_dat$perc >= percentiles[[1]][1] & dis_dat$perc <= percentiles[[1]][2])))#horizontal line extent on either side of midpoint
    vl1 = max(dis_dat$prop)/ 50#vertical line extent
    
    D = D +
      geom_segment(aes(x=x1 - e1, xend=x1 + e1, y=y1, yend=y1), color = bar_colours[2], size = 1.3) +#horizontal line
      geom_segment(aes(x=x1 - e1, xend=x1 - e1, y=y1 - vl1, yend=y1 + vl1), color = bar_colours[2], size = 1.3) +#vertical line (left)
      geom_segment(aes(x=x1 + e1, xend=x1 + e1, y=y1 - vl1, yend=y1 + vl1), color = bar_colours[2], size = 1.3) +#vertical line (right)
      annotate("text", x = x1 - e1, y = y1 + vl1 * 3, 
               vjust = 0, hjust=0, label=high_text[1], size = 5, color = bar_colours[2])
  }
  
  #add line and annotation of highlight [[2]]
  if(percentiles[[2]][1] != 0 || percentiles[[2]][2] != 0){
  y2 = max(dis_dat$prop) + max(dis_dat$prop)/ 10#0.06#line height
  x2 = mean(which(dis_dat$perc >= percentiles[[2]][1] & dis_dat$perc <= percentiles[[2]][2]))
  e2 = 0.4 + (x2 - min(which(dis_dat$perc >= percentiles[[2]][1] & dis_dat$perc <= percentiles[[2]][2])))
  vl2 = max(dis_dat$prop)/ 50
  
  D = D +
    geom_segment(aes(x=x2 - e2, xend=x2 + e2, y=y2, yend=y2), color = bar_colours[3], size = 1.3) +#horizontal line
    geom_segment(aes(x=x2 - e2, xend=x2 - e2, y=y2 - vl2, yend=y2 + vl2), color = bar_colours[3], size = 1.3) +#vertical line (left)
    geom_segment(aes(x=x2 + e2, xend=x2 + e2, y=y2 - vl2, yend=y2 + vl2), color = bar_colours[3], size = 1.3) +#vertical line (right)
    annotate("text", x = x2 - e2, y = y2 + vl2 * 3, 
             vjust = 0, hjust=0, label=high_text[2], size = 5, color = bar_colours[3])
  }
  return(D)
}
```
The second kind of plot is the cumulative distribution plot showing grade discrimination across the board.
The function `cum_plot()` will handle that.  
```r
cum_plot = function(
  perc_dat,#data frame with columns grades, cum_prop_A, and cum_prop_B
  xlabel = 'Foreign grade',
  xticklabels = perc_dat$grades,#customisable x-axis labels
  caption_text = 'Source: ',#true for German Abitur distribution 
  line_colours = c("#c00000", '#0000CD', '#808080'),#red, blue, grey
  title_text = ' ',
  in_legend = data.frame(x = c(7, 7, 7), y = c(0.45, 0.8, 0.25),
                         label = c('Actual foreign grading scale',
                                   'Foreign grading translated to German scale', 'Discrimination'),
                         angle = c(0, 0, 45))){
 ```
 The `geom_ribbon()` call highlights the area between the lines drawn with `geom_line()`.
 It was surprisingly difficult to get this to work well. 
 When the lines cross, the result can get funky but this implementation worked well enough for me.
 ```r
  D = ggplot(data = perc_dat, aes(x = grades)) +
    geom_ribbon(aes(ymax = cum_prop_B, ymin = cum_prop_A), fill=line_colours[3]) +#shading between lines (discrimination space)
    geom_line(aes(y = cum_prop_A), colour = line_colours[1], size = 2) +
    geom_line(aes(y = cum_prop_B), colour = line_colours[2], size = 2) +
    labs(y="Cumulative share of population", x=xlabel) +
    scale_y_continuous(breaks = c(0, 0.25, 0.5, 0.75, 1),
                       labels = c('Best', 'Top 25%', 'Top 50%', 'Top 75%', '100%')) +
    scale_x_reverse(limits = c(max(perc_dat$grades), min(perc_dat$grades))) +
    ggtitle(title_text) +
    labs(caption = caption_text) + 
    theme(plot.caption = element_text(size = 10, color = 'grey', face= 'italic')) +
    annotate('text', x = in_legend$x[1], y = in_legend$y[1], hjust = 0, 
             label = in_legend$label[1], colour = line_colours[1], in_legend$angle[1]) +#red label of foreign grading
    annotate('text', x = in_legend$x[2], y = in_legend$y[2], hjust = 1, 
             label = in_legend$label[2], colour = line_colours[2], in_legend$angle[2]) +#blue label of translated grading
    annotate('text', x = in_legend$x[3], y = in_legend$y[3], hjust = 0,
             label = in_legend$label[3], colour = 'white', angle = in_legend$angle[3])
  
  return(D)
}
```
The only function left to implement is the official translation formula for foreign grades into Germany grades.
```r
mod_bay = function(Nmax, Nmin, Nd) return(max(c(1, 1 + (3*((Nmax - Nd)/(Nmax-Nmin))))))
```
Having all the functions in place, we can start plotting in a very efficient way.
The first half of the first plot is just my place on the Dutch grading scale: between the top 0.05% and the top 0.97%.
For some obscure reason, I need to increase the latter by a very small amount for the code to catch the correct grade bracket corresponding to these percentages.
```r
VW = data.frame(prop = c(0.0004, 0.97, 9.19, 40.6, 49.2)/100,
                grades = c('10 - 9.5', '9.4 - 8.5', '8.4 - 7.5', '7.4 - 6.5', '6.4 - 5.5'))
p1 = dis_plot(dis_dat = VW, xlabel = 'Dutch VWO exam' ,
                caption_text = 'Source: Nuffic, 2014',
                percentiles = list(c(0.0005, 0.0005 + 0.97)/100, c(0,0)), high_text = c('Actual: top 1%', ' '),
                title_text = 'A Dutch 8.7 on the Dutch grading scale')
```
The second half of the first plot is my place on the German grading scale.
The highlighted percentages in this case come directly from the actual place on the Dutch grading scale (see above) and the translation via `mod_bay()`.
The German grade resulting from `mod_bay()` is then turned into a population percentage called `D_trans_prop`.
```r
dat <- read.csv("https://raw.githubusercontent.com/rikunert/german_grade_discrimination/master/Aus_Abiturnoten_2015.csv",
                sep = ';')
AB_raw = dat$X.16[9:39]
AB = data.frame(prop = AB_raw/sum(AB_raw),
                perc = cumsum(AB_raw/sum(AB_raw)),#cumulative proportion
                grades = as.character(format(seq(1, 4, 0.1), nsmall = 1)))
D_trans_grade = round(mod_bay(10, 5.5, 8.7), digits = 1)
D_trans_prop = AB$perc[AB$grades == D_trans_grade]
p2 = dis_plot(AB, xticklabels = c('1.0', '1.5', '2.0', '2.5', '3.0', '3.5', '4.0'),
                percentiles = list(c(0, 0.009704),c(D_trans_prop-1e-10, D_trans_prop + 1e-10)),
                high_text = c('Actual: top 1%',
                              sprintf('Translated: top %d%%', round(D_trans_prop * 100))),
                title_text = 'A Dutch 8.7 translated to German grade')
```
We use `grid.arrange()` from the `gridExtra` package to combine the plots into a single one.
```r
grid.arrange(grobs = list(p1,p2), ncol = 2, widths = c(2,2))
```
This brings us to the first cumulative share in population plot using `cum_plot()`.
 This cumulative share is easy to get for the Dutch grades on the Dutch scale via `cumsum()`.
 Their translations' place on the German scale is done via this intricate `sapply()` statement.
 `sapply()` works similarly to list comprehension in `python`. 
 I give it the list of all Dutch pass grades `seq(10, 5.5, -0.5)` and an unnamed function with input `x` (which stands for the pass grades).
 These Dutch grades get turned into German grades via `mod_bay()` and forced into a single digit with single decimal notation via `format()`.
 This gets then looked up on the German cumulative share in population list for grades `AB$perc` which we generated before.
```r
NL_perc = rep(cumsum(VW$prop), each = 2)
D_perc = sapply(seq(10, 5.5, -0.5), function(x) AB$perc[AB$grades == format(round(mod_bay(10, 5.5, x), digits = 1), nsmall = 1)])
perc_dat = data.frame(
  grades = seq(10, 5.5, -0.5),
  cum_prop_A = NL_perc,
  cum_prop_B = D_perc)
```
In the ensuing plotting call you might notice the weird caption text using an `sprintf()` command.
The reason is simple. I like my plots to display both who is responsible for the plotting (me!) and who is responsible for the data (Source:...).
Unfortunately, `ggplot2` currently only supports a single caption. 
So, I fill a lot of space between the two things I want to say in the caption with spaces. 
To be precise, `sprintf()` first prints '@rikunert' and then a 225 character string on whose right hand end is the second string.
The result is the impression of a left adjusted and a right adjusted caption on the same line.
```r
D = cum_plot(perc_dat, 
           xlabel = 'Dutch grade',
           caption_text = sprintf('%s%225s', '@rikunert', 'Source: Nuffic and KMK'),
           title_text = 'How Dutch grades get discriminated in Germany',
           in_legend = data.frame(x = c(7, 7, 7.7),
                                  y = c(0.45, 0.85, 0.32),
                                  label = c('Actual Dutch grading scale',
                                            'Dutch grading translated to German scale',
                                            'Discrimination'),
                                  angle = c(0, 0, 45))) +
  annotate("segment", x = 8.7, xend = 8.7, y = 0.01, yend = 0.235,
           colour = "black", size = 2) +
  annotate('text', x = 8.65, y = 0.145, hjust = 0, 
           label = 'Dutch 8.7\n= German 1.9', colour = "black")
D
```
The plot for the RWTH Aachen adjustment follows the above example very closely.
The only substantial difference is the altered call to `mod_bay()`.
```r
D_perc = sapply(seq(10, 5.5, -0.5), function(x) AB$perc[AB$grades == format(round(mod_bay(8.5, 5.5, x), digits = 1), nsmall = 1)])
perc_dat = data.frame(
  grades = seq(10, 5.5, -0.5),
  cum_prop_A = NL_perc,
  cum_prop_B = D_perc)

D = cum_plot(perc_dat, 
         xlabel = 'Dutch grade',
         caption_text = sprintf('%s%225s', '@rikunert', 'Source: Nuffic and KMK'),
         title_text = 'The RWTH Aachen adjustment to grade translations',
         in_legend = data.frame(x = c(7, 7, 6.75),
                                y = c(0.45, 0.58, 0.58),
                                label = c('Actual Dutch grading scale',
                                          'Dutch grading translated to German scale',
                                          'Discrimination'),
                                angle = c(0, 0, 45))) +
  annotate("segment", x = 8.7, xend = 8.7, y = -0.015, yend = 0.025,
           colour = "black", size = 2) +
  annotate('text', x = 8.7, y = 0.145, hjust = 0, 
           label = 'Dutch 8.7\n= German 1.0', colour = "black")
D
```
With these examples in mind, plotting the French case is easy.
Notice how reading in the data is done a lot better now with a direct call to the French government website.
```r
dat <- read.xlsx("http://cache.media.education.gouv.fr/file/2017/09/9/NI-EN-05-2017-donnees_730099.xlsx",
                 sheet=5, startRow = 38)
FR_N = dat$Tous.baccalauréats[1:201] * dat$Tous.baccalauréats[202]
FR = data.frame(prop = rev(FR_N[101:201]/sum(FR_N[101:201])),
                    grades = as.character(format(as.double(rev(dat$`Moyenne.à.l'issue.du.1er.groupe`[101:201]))), nsmall = 1))
                    
p = dis_plot(FR, xlabel = 'French Baccalauréat grade',
                caption_text = sprintf('%s%218s', '@rikunert','Source: French ministry of education, 2016'),
                xticklabels = c('20.0', '17.5', '15.0', '12.5', '10.0'),
                title_text = 'The French grading scale')
p

FR_perc = cumsum(FR$prop)
D_perc = sapply(seq(20, 10, -0.1), function(x) AB$perc[AB$grades == format(round(mod_bay(20, 10, x), digits = 1), nsmall = 1)])
perc_dat = data.frame(
  grades = seq(20, 10, -0.1),
  cum_prop_A = FR_perc,
  cum_prop_B = D_perc)

D = cum_plot(perc_dat, 
             xlabel = 'French grade',
             caption_text = sprintf('%s%213s', '@rikunert', 'Source: French ministry of education and KMK'),
             title_text = 'The discrimination of French grades in Germany',
             in_legend = data.frame(x = c(14, 14, 15), 
                                    y = c(0.20, 0.75, 0.3),
                                    label = c('Actual French grading scale',
                                              'French grading translated to German scale',
                                              'Discrimination'),
                                    angle = c(0, 0, 35)))
D
```
And finally, we can play this game for Scotland, too.
Notice that both `mod_bay()` and `cum_plot()` cannot deal with alphabetical grades. 
So we turn Scottish grades into points, like 'A' = 1, 'B' = 2, etc.
 In the plot itself we change the labels back to 'A', 'B', and 'C'.
```r
SH = data.frame(prop = c(0.34, 0.26, 0.22)/sum(c(0.34, 0.26, 0.22)),#7% D (FAIL) are ignored, based on 23,794 pupils
                grades = c('A', 'B', 'C'))

p = dis_plot(SH, xlabel = 'Scottish Advanced Highers grade',
                caption_text = sprintf('%s%217s', '@rikunert','Source: Scottish Qualifications Authority, 2016'),
                title_text = 'The Scottish grading scale')
p

SH_perc = cumsum(SH$prop)
D_perc = sapply(seq(3, 1, -1), function(x) AB$perc[AB$grades == format(round(mod_bay(3, 1, x), digits = 1), nsmall = 1)])
perc_dat = data.frame(
  grades = c(3, 2, 1),
  cum_prop_A = SH_perc,
  cum_prop_B = D_perc)

D = cum_plot(perc_dat, 
             xlabel = 'Scottish grade',
             caption_text = sprintf('%s%213s', '@rikunert', 'Source: Scottish Qualifications Authority and KMK'),
             title_text = 'The unfair advantage of Scottish grades in Germany',
             in_legend = data.frame(x = c(2.4, 1.25, 2.8), 
                                    y = c(0.82, 0.45, 0.3),
                                    label = c('Actual Scottish grading scale',
                                              'Scottish grading translated to German scale',
                                              'Unfair foreign advantage'),
                                    angle = c(0, 0, 20))) +
  scale_x_reverse(breaks = c(3, 2, 1), labels = c('A','B','C'), limits = c(3, 1))
D
```
{% include twitter_plug.html %}

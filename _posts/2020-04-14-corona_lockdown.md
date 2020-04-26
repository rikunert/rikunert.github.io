---
layout: post
published: true
permalink: corona_lockdown
comments: true

title: The severity of Europe's reaction to Covid-19
subtitle: 230 lines of code (Python)
tags: [matplotlib, seaborn, data science, python, Sars-Cov2, Covid-19, Corona, gif, choropleth, map, geopandas]
lead: While the global pandemic unfolded, European countries followed different strategies. Some reacted radically and fast. Others are still taking their time. In this blog post I will characterise the policy decisions to the novel Corona virus.
---

![Gif of the policy restriction severity in Europe](https://raw.githubusercontent.com/rikunert/corona/master/severity_gif/corona_lockdown_timeline_100dpi.gif "Gif of the policy restriction severity in Europe (Sars-Cov-2, Covid-19, Corona))")

<!--excerpt-->

## Germany's reaction to Covid-19 over time

Nearly one month ago, on 16 March 2020, Germany instituted a soft lockdown in response the Covid-19 epidemic.
At the time, 4838 infections with Sars-Cov2 were confirmed.
A month later that number has ballooned to over 100,000.

![Development of total Covid-19 cases over time in Germany](https://raw.githubusercontent.com/rikunert/corona/master/D_cum_cases_ox.png "Development of total Covid-19 cases in Germany")

While most analyses centre on the exact moment of the lockdown, a series of policy decisions were taken before and after.
This very rough and incomplete timeline gives an impression.

Date | Policy
---|---
27/2/2020 | National disease prevention centre urges public health measures
28/2/2020 | Travellers from some countries are screened
29/2/2020 | Government recommends cancellation of some events
16/3/2020 | School closure
18/3/2020 | Chancellor Merkel urges Germans to limit their movement
20/3/2020 | One federal state (Bavaria) implements a curfew

Over time, the limitations of freedoms in the name of public health became increasingly stringent.
Researchers from the [University of Oxford's Blavitnik School of Governance](https://www.bsg.ox.ac.uk/research/research-projects/coronavirus-government-response-tracker) compiled these data and summarised them in an overall Stringency Index.
For Germany, the Stringency Index only went up since the start of the pandemic.

![Development of Germany's Stringency Index quantifying the policy response to Covid-19](https://raw.githubusercontent.com/rikunert/corona/master/D_restriction_severity.png "Development of Germany's Stringency Index quantifying the policy response to Covid-19")

The vertical orange lines are the same between the two line graphs for easy orientation.
Next week the Stringency Index might go down a little as media speculation increases that the soft lockdown will be eased.
The right vertical orange line shows when this might happen.

## Europe's reaction to Covid-19

Nearly all countries of Europe have implemented some form of lockdown.
That is easily apparent when visualising each country's Stringency Index in a choropleth map.

![Development of Europe's Stringency Index quantifying the policy response to Covid-19](https://raw.githubusercontent.com/rikunert/corona/master/EU_restriction_severity.png "Development of Europe's Stringency Index quantifying the policy response to Covid-19")

Sweden truly stands out with a remarkably lax approach to disease control.
Time will tell whether they will also have to restrict freedoms to save lives.

Looking at the map one might think that Europe is responding relatively homogeneously.
However, when combining the time and the space dimensions, stark differences between countries become apparent.
The following gif shows how Italy paved the way and the rest followed.

![Gif of the policy restriction severity in Europe](https://raw.githubusercontent.com/rikunert/corona/master/severity_gif/corona_lockdown_timeline_100dpi.gif "Gif of the policy restriction severity in Europe (Sars-Cov-2, Covid-19, Corona))")

By combining temporal and geographical information one gets a feeling for how Europe responded to Covid-19.
While ignoring the virus during the month of January, February saw Italy react harshly.
That was followed by March, a month in which nearly all countries went into some form of lockdown led by Italy.

## Conclusion

I remember the conversations in January when the Chinese government locked down Hubei province.
Similar measures in Europe were frankly completely unthinkable.
And yet, China only offered us a glimpse into our own future.

It was Italy that woke up Europe.
A rich Western European country was brought to its knees by a virus.
The stringency with which Italy responded was soon emulated by the rest of the continent.

The complete code to recreate the analyses and plots of this blog post can, as always, be found on github [here](https://github.com/rikunert/corona/blob/master/government_response.ipynb).

{% include twitter_plug.html %}

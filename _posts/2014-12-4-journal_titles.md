---
layout: post
title: What do we publish in plant biology?
description: "A first run in examining what was published this year"
tags: [plant biology, science, publications, R, text mining]
comments: true
image:
  feature: publish.jpg
  credit: Nan Rinella
  creditlink: http://www.nanrinella.com/2012/10/why-developing-a-plan-for-attending-a-writers-conference-is-the-2nd-step-to-success/
---

I was reading articles the other day and began to wonder what we publish, where we publish it, and what it can tell us, so I started exploring some data.
<br><br>
With no idea of how to approach this problem, I dove in and attempted to figure it out. I started by extracting xml files for tables of contents from 2014 from some popular plant science journals, retrieving the titles, and analyzing those to see what words show up most frequently. I started with The Plant Cell, Plant Phys, New Phytologist, JxB, and The Plant Journal. I did all of this in R and will gladly share code once I clean it up and annotate it a bit (look for a future edit on this). Once I had all of these terms, I made simple word clouds for all of the journals and for each of them individually. I eliminated some common words (the, and, etc.) and made separate clouds that didn't include arabidopsis. For all of the journals, we get this:

<figure>
	<img src="/images/journals/all.png"></a>
</figure>
<br>
If we remove arabidopsis, it looks a little different. Here are the two side by side:
<figure class="half">
	<img src="/images/journals/all.png">
	<img src="/images/journals/allnoa.png">
</figure>

<br>
Clearly, some different words gain prominence when we eliminate arabidopsis. They're not necessarily surprising terms (plant, protein, and development). 

#### A single journal: The Plant Cell
I did the same with each journal that I named above and saw similar trends. Rather than put all of them here, I'll show you The Plant Cell data.
<figure class="half">
	<img src="/images/journals/pcall.png">
	<img src="/images/journals/pcnoa.png">
</figure>

#### More journals!
<br>
Though I found this all to be interesting, I wasn't sure of where to go next. I wanted to have more journals to use and some that aren't specific to plant publications. I turned to pubmed and did my best to figure out their search process to narrow it down. I started by grouping together some general journals that sometimes publish plant articles (Cell, Science, Nature, Nature communications, Nature genetics, Development, PNAS, PLoS One, PLoS genetics) and used a slew of search terms that would give me articles about plants ("plant science" OR "plant biology" OR "plant" OR "plants" OR "arabidopsis" OR "root" OR "rice" OR "maize" OR "photosynthesis" OR "choloroplast" OR "planta" OR "leaves" OR "leaf") (this can be tweaked further, and probably should be, but was a starting point for me). This gave me about 3600 publications to play with.
<br><br>
I got similar data from plant only journals that are in PubMed (Plant Phys, Planta, Plant Molecular Biology, The plant journal, The plant cell, Plant cell reports, Plant & cell physiology, Plant signaling & behavior, MPMI, BMC plant biology, The new phytologist, JxB, Crop science, Journal of plant physiology, Frontiers in plant science, Trends in plant science, Molecular plant, Current opinion in plant biology, Annals of botany). No need to add search terms here as they should all, or mostly, be focused on plants. This gave me another 5100+ articles.
<br><br>
I started where I did previously, with word clouds. I made them for the general journals with and without arabidopisis: 
<figure class="half">
	<img src="/images/journals/big_journals_all.png">
	<img src="/images/journals/big_journals_noa.png">
</figure>

And then I did the same for the plant-specific journals:
<figure class="half">
	<img src="/images/journals/plant_journals_all.png">
	<img src="/images/journals/plant_journals_noa.png">
</figure>

This is clearly a richer dataset, the clouds are getting bigger and harder to interpret, but we can still see some words clearly show up more frequently than others.

#### Looking deeper
The word clouds are nice, but I wanted to figure a bit more out. I had seen this [blog post](http://www.noamross.net/blog/2014/7/24/esacorpuscompare.html) in which the author examines the use of words in ESA meeting abstracts in 2013 and 2014. If you haven't seen it, check it out - super interesting. I used the code provided in github, tweaked it a bit, and came up with this.
<br><br>
First, I got the 30 most frequently used words from general journals and plant-specific journals. 

<figure class="half">
	<img src="/images/journals/bigtop30.png">
	<img src="/images/journals/planttop30.png">
</figure>
<br><br>

One thing to notice is the scale in each of those plots. Arabidopsis shows nearly 1000 times in the plant-specific journals (almost 20% of all articles!) but the most common word in the general articles, plant, shows up only 355 times, around 10%.  
<br>
I followed the framework from above and figured out what words were used most in plant journals in relation to general journals, and vice versa.  This graph shows the top 50 terms that are used most frequently in plant-specific versus general journals. 
<figure>
	<img src="/images/journals/difference50plot.png"></a>
</figure>
This shows an interesting spread. The general journals have more articles about sequencing, genomes, humans (I'm guessing impacts on human health or human outcomes), microbes, communities, and China. The plant-specific journals seem to focus on specific processes and stresses.

#### Conclusions

There's still a lot to look at here. This was a simple first pass. I'll hopefully come up with a more insightful and informative data analysis in the future. I may delve into some real text analysis and may also look at what is funded in relation to what we publish. More to come, for sure.
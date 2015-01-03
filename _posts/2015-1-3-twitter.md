---
layout: archive2
title: Who am I? Who are we? 
excerpt: "An exploration of identity, via Twitter"
tags: [plant biology, science, twitter, python, R, text mining]
comments: true
image:
  feature: idenitity.jpg
  credit: Tara Hunt
  creditlink: http://tarahunt.com/2013/07/05/the-identity-scorecard/
  teaser: twitterbird.jpg
---


I've always been bad, *really bad*, at those games where you have to describe yourself using five words. I usually end up picking words that are indisputable facts: student or graduate student, American or Pennsylvanian, twenty-something, swimmer, scientist. Though all of these are true, they're boring and don't tell you much about me. This lack of description doesn't bother me: all of those words that I can't quite identify blend together in a perfect emulsion to be me. I think that our descriptors aren't for us as individuals, but for everyone else who is trying to perceive us, so telling someone that I'm just me doesn't go over too well. If I had an online dating profile that said "I'm just me," that wouldn't be too helpful in matching up with potential suitors, but it's all I ever come up with
<br><br>
So I turned to one of my friends and asked her for five words that she would use to describe me. Without skipping a beat, she started:

- Trustworthy
- Fun-loving
- Loyal
- Witty
- Determined

<br>
These aren't really words that tell you a lot about me, though I think they are good descriptors. I could rely on other popular quantifiers that people use, saying that I'm introverted or extroverted, but I don't think fall into either camp, so I'm not a fan of these. A PI that I know really likes to classify people and often says that I do this or that because I'm extroverted, which I don't really think is true. Related, I'm looking forward to reading the book *Me, Myself, and Us* by Brian Little ([reviewed by Annie Murphy Paul in the New York Times](http://www.nytimes.com/2014/12/28/books/review/me-myself-and-us-by-brian-r-little.html?_r=0)). I relate to the author's apparent angst toward these overly simplistic binary groupings, further impeding my ability to describe myself.
<br><br>
If we're only using our descriptors to project ourselves into the world, then we can just pick and choose words that will put us into groups with others who have similar interests and inclinations. That seems fairly innocuous. Complications arise when we use these descriptors to create our identity. I think this [satirical piece](http://the-toast.net/2014/11/10/sorry-murdered-everyone-im-introvert/) from The Toast captures the sentiment perfectly. The author's apology is hidden under an excuse: I act this way because I'm an introvert. I would argue that it's not because you're an introvert, but because you're you, and that's how you act. The end. I just may be sick of people using MBTIs to provide reason to behavior (I'm an ENTJ, in case you were wondering me and wanted to rationalize my behaviors).


### Identity
All of this is an experiment in identity, to some extent. How we see ourselves and how others see us is one of the hallmarks of our movement through the world. How I project myself IRL may be slightly different from how I project myself in internet life (someone else who knows me in both realms would have to really answer this). As a graduate student, I'm working to build my professional identity: finding a research niche, publishing papers, networking at conferences, and building connections in the greater research web. The barriers between personal and professional life have dropped quite dramatically, with a lot of overlap between the two. Rather than just rambling about this, I decided to give it a but of a deeper look, using Twitter.
<br><br>
All Twitter users have 160 characters to describe themselves and project who they are into the internet-sphere. My bio looks like this:

<div id="wrapper" style="width:100%; text-align:center">
<img src="/images/Twitter/me.png">
</div>

It takes some of those indisputable descriptors and combines a bit of flavor to say "hey, this is me!"

### Methods
I used a combination of Python Twitter APIs and information from [Mining the Social Web](http://nbviewer.ipython.org/github/furukama/Mining-the-Social-Web-2nd-Edition/blob/master/ipynb/Chapter%201%20-%20Mining%20Twitter.ipynb) to collect a bunch of Twitter bios. I started with who I follow and who follows me. Since a lot of scientists use Twitter, I decided to explore this a bit further in my particular field. I used [Twiangulate](http://twiangulate.com/) to search for users that said something about plant biology in their bio. That wasn't *too* fruitful, so I moved onto using the [Plant Science](https://Twitter.com/AnneOsterrieder/lists/plant-science) list curated by [@Anne Osterrieder](https://Twitter.com/AnneOsterrieder). I then figured that people who have an interest in plant biology would probably be following [@plant biology](https://Twitter.com/plantbiology). If you have no interest in plant biology and follow plant biology, well, then, ok.

### What do we say about ourselves?
Now that I had all of these bios, I had to analyze them. I could count words and see what shows up and what occurs in relation to what else. I could, but I don't really know how to do that, so I made some word clouds instead because that's easy.


#### My followers
I have a relatively small following, and not all of them have bios written. After I cleaned everything up, I had 183 bios to draw data from. I eliminated numbers and some common words (and, then, for, with, are, that, into, you, I) and only included words that showed up more than 3 times. 

<div id="wrapper" style="width:100%; text-align:center">
<img src="/images/Twitter/followers.png">
</div>

It seems as though my followers are plant science lovers of things. They're also PhD moms interested in learning about biology and music.

#### Who I follow
A bit of a larger sample here, with 579 members after filtering. I used a minimum frequency  of 6 here. 

<div id="wrapper" style="width:100%; text-align:center">
	<img src="/images/Twitter/following.png">
</div>

Again, science takes the lead. I also seem to have a few feminist women nerds . An interesting mix, though I'm surprised nothing related to swimming shows up.

#### Everything else
The plant biology search group had 79 members, so words with a frequency greater than 2 are included in this cloud.

<div id="wrapper" style="width:100%; text-align:center">
	<img src="/images/Twitter/search.png">
</div>

The Plant Science List from [@Anne Osterrieder](https://Twitter.com/AnneOsterrieder) has almost 300 members, 286 of whom have bios. I used a minimum frequency of 3.

<div id="wrapper" style="width:100%; text-align:center">
	<img src="/images/Twitter/list.png">
</div>

The largest set, those following [@plant biology](https://Twitter.com/plantbiology), a group of about 13.5k users, had 9250 bios. With a set this large, I used a minimum frequency of 25.

<div id="wrapper" style="width:100%; text-align:center">
	<img src="/images/Twitter/plantbiology.png">
</div>

### What does this tell us?
This tells me that my Twittersphere is a science-loving bunch. It didn't tell me a lot about how we project ourselves into the world or where I fall with respect to everyone else. I may try to come up with a different way to analyze this data and get something more meaningful from it. If nothing else, this was a good exercise in Python, which made me sad and grumpy. I crawled back to R, where I was accepted with open arms to make the word clouds.
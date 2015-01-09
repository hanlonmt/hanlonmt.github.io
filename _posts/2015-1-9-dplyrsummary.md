---
layout: post
title: dplyr, enhancing my R experience
description: I turned a behemoth function into a simple chunk to summarize my data
tags: [R]
comments: true
---

When I get data, one of the first things that I want to do is get a basic summary and make some plots to look at things. I usually start with getting the mean, SD, and SE. A few years ago, this required using aggregate and some sort of apply function. Then, I discovered plyr and wrote a function of my own. It allowed you to get these data out and then generate graphs for them. I generated some random data, that looks like this: 


{% highlight text %}
##   var1 var2 out1 out2 out3
## 1    a high   15   23   13
## 2    a high   14   17    5
## 3    a high   19    8   21
## 4    a  med   18   26   23
## 5    a  med    1   24   10
## 6    a  med   23    9   24
{% endhighlight %}


I then had this clunky and old function that ran over all of the data and gave you an output of means, SD, and SE:


{% highlight r %}
if(!require(plyr)) { 
install.packages("plyr"); require(plyr)} 

summarydata <- function (df,splittingfactors=NULL, convert_integers=F, calc_mean=T, calc_sd=T, calc_se=T){
  
  if((convert_integers)){
    for (i in 1:length(df)) if (is.integer(df[,i]))(df[,i]<-as.factor(df[,i]))
  }
  
  #this section gets an index of factors and numeric variables to use later
  v.numeric <- (rep(0,length(df)))
  v.factor <- (rep(0,length(df)))
  for (i in 1:length(df)) if (is.factor(df[,i])) (v.factor[i]<-as.vector((names(df[i]))))
  for (i in 1:length(df)) if (is.numeric(df[,i])) (v.numeric[i]<-as.vector((names(df[i]))))
  v.factor<-v.factor[!v.factor==0]
  v.numeric<-v.numeric[!v.numeric==0]
  
  if(!is.null(splittingfactors)){
    v.factor <- as.vector(c(splittingfactors))
  }
  
  # each of these three if loops calculate mean, sd, and se for each column (numcolwise) and then combines into a results df
  if(calc_mean){
    mean.res <- ddply(df, c(v.factor), numcolwise(mean, na.rm=TRUE))
    mean.names <- (rep(0,length(v.numeric)))
    for (i in 1:length(v.numeric)) (mean.names[i]<-as.vector(paste(v.numeric[i],"mean",sep=".")))
    column.names <- c(v.factor,mean.names)
    colnames(mean.res) <- column.names
  }
  
  if(calc_sd){
    sd.res <- ddply(df, c(v.factor), numcolwise(sd, na.rm=TRUE))
    sd.names <- (rep(0,length(v.numeric)))
    for (i in 1:length(v.numeric)) (sd.names[i]<-as.vector(paste(v.numeric[i],"sd",sep=".")))
    column.names <- c(v.factor,sd.names)
    colnames(sd.res) <- column.names
    ifelse(calc_mean,ifelse(calc_sd,mean.sd.res <- merge(mean.res,sd.res), mean.sd.res<-mean.res),mean.sd.res<-sd.res)
  }

  
  if(calc_se){
    se <- function(x) sqrt(var(x,na.rm=TRUE)/length(na.omit(x)))
    se.res <- ddply(df, c(v.factor), numcolwise(se))
    se.names <- (rep(0,length(v.numeric)))
    for (i in 1:length(v.numeric)) (se.names[i]<-as.vector(paste(v.numeric[i],"se",sep=".")))
    column.names <- c(v.factor,se.names)
    colnames(se.res) <- column.names
  }
  ifelse(calc_mean, ifelse(calc_sd, ifelse(calc_se, results <- merge(mean.sd.res,se.res), results <- merge(mean.res, se.res)), ifelse(calc_se, results<- merge(mean.res,se.res), results <- mean.res)), ifelse(calc_sd, ifelse(calc_se, results <- merge(sd.res,se.res), results <- sd.res), results <- se.res))
              
  
  return(results)
}

sumd <- summarydata(data)
sumd
{% endhighlight %}



{% highlight text %}
##   var1 var2 out1.mean out2.mean out3.mean   out1.sd   out2.sd   out3.sd
## 1    a high 16.000000  16.00000 13.000000  2.645751  7.549834  8.000000
## 2    a  low 19.000000  18.33333 23.666667 13.000000  7.637626  3.214550
## 3    a  med 14.000000  19.66667 19.000000 11.532563  9.291573  7.810250
## 4    b high 17.666667  12.33333  9.666667  8.504901  5.507571 10.692677
## 5    b  low  8.333333  12.33333  8.666667  5.686241  7.637626  2.516611
## 6    b  med 10.666667  11.33333 16.333333  5.686241  6.506407  2.516611
## 7    c high 12.666667   4.00000  9.333333  8.621678  2.645751  7.371115
## 8    c  low  7.333333  18.66667 20.666667  4.509250  4.932883  5.507571
## 9    c  med 20.333333  13.33333  5.666667  4.509250 13.051181  3.214550
##    out1.se  out2.se  out3.se
## 1 1.527525 4.358899 4.618802
## 2 7.505553 4.409586 1.855921
## 3 6.658328 5.364492 4.509250
## 4 4.910307 3.179797 6.173420
## 5 3.282953 4.409586 1.452966
## 6 3.282953 3.756476 1.452966
## 7 4.977728 1.527525 4.255715
## 8 2.603417 2.848001 3.179797
## 9 2.603417 7.535103 1.855921
{% endhighlight %}

## dplyr

That works fine, actually, but with bigger data frames, the speed isn't that good. I wanted to streamline the code. I'm using the [dplyr](http://cran.rstudio.com/web/packages/dplyr/vignettes/introduction.html) package instead of the plyr package, which has slightly different syntax, and performs a bit differently. Here's what I've come up with(assuming you've checked the class of each variable that you want to work with):


{% highlight r %}
if(!require(dplyr)) { 
  install.packages("dplyr"); require(dplyr)} 

se <- function(x) sqrt(var(x,na.rm=TRUE)/length(na.omit(x)))

summaryresults <-
  data %>% 
  group_by(var1, var2) %>%
  summarise_each(funs(mean, sd, se))
summaryresults
{% endhighlight %}



{% highlight text %}
## Source: local data frame [9 x 11]
## Groups: var1
## 
##   var1 var2 out1_mean out2_mean out3_mean   out1_sd   out2_sd   out3_sd
## 1    a high 16.000000  16.00000 13.000000  2.645751  7.549834  8.000000
## 2    a  low 19.000000  18.33333 23.666667 13.000000  7.637626  3.214550
## 3    a  med 14.000000  19.66667 19.000000 11.532563  9.291573  7.810250
## 4    b high 17.666667  12.33333  9.666667  8.504901  5.507571 10.692677
## 5    b  low  8.333333  12.33333  8.666667  5.686241  7.637626  2.516611
## 6    b  med 10.666667  11.33333 16.333333  5.686241  6.506407  2.516611
## 7    c high 12.666667   4.00000  9.333333  8.621678  2.645751  7.371115
## 8    c  low  7.333333  18.66667 20.666667  4.509250  4.932883  5.507571
## 9    c  med 20.333333  13.33333  5.666667  4.509250 13.051181  3.214550
## Variables not shown: out1_se (dbl), out2_se (dbl), out3_se (dbl)
{% endhighlight %}

With four lines, I can now do the same with what I did before. Though I was quite fond of my old funciton (we had a good run together), this new method makes things much easier and faster.
<br><br>
I also wrote this post in R (Rmd) knit it using knitr, and then converted it and got it on my website using [these instructions](http://www.jonzelner.net/jekyll/knitr/r/2014/07/02/autogen-knitr/). It worked without a hitch and will be something that I'll do for all of my R-related posts from here forward.

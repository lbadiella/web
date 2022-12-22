---
title: Lord's paradox and the third statistician
author: Llorenç Badiella
date: '2022-12-04'
slug: []
Description: ''
tags: []
categories: []
disableComments: no
---



The paradox presented by Lord in 1967 describes the following situation:

> A large university is interested in investigating the effects on the students of the diet provided in the university dining halls and any sex differences in these effects. Various types of data are gathered. In particular, the weight of each student at the time of his arrival in September and his weight the following June are recorded. 

> At the end of the school year, the data are independently examined by **two statisticians**. Both statisticians divide the students according to sex.

> **The first statistician** examines the mean weight of the girls at the beginning of the year and at the end of the year and finds these to be identical. On further investigation, he finds that the frequency distribution of weight for the girls at the end of the year is actually the same as it was at the beginning. He finds the same to be true for the boys.  **He concludes that there is no evidence of any interesting effect of the school diet (or of anything else) on students.** 

> **The second statistician**, working independently, decides to do an analysis of covariance. After some necessary preliminaries, he determines that the slope of the regression line of final weight on initial weight is essentially the same for the two sexes. This is fortunate since it makes possible a fruitful comparison of the intercepts of the regression lines. **He finds that the difference between the intercepts is statistically highly significant. The second statistician concludes, as is customary in such cases, that the boys showed significantly more gain in weight than the girls** when proper allowance is made for differences in initial weight between the two sexes. 

### The data

The data may look like this:




```r
library(ggplot2)
ggplot(bd,aes(x=W1,y=W2,color=factor(sex))) + geom_point() +
scale_color_manual(values=c("#E69F00", "#56B4E9")) +
coord_fixed(xlim=c(30,90),ylim=c(30,90))  + theme(aspect.ratio=1) + theme_bw() + labs(x="Weight June",y="Weight September",color="Sex")
```

<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/f1-1.png" alt="Weight by group and period" width="672" />
<p class="caption">Figure 1: Weight by group and period</p>
</div>

The average and variance for each group are the same in both periods.


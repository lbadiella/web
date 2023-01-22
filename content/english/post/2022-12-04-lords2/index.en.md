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



<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/f1-1.png" alt="Weight by group and period" width="672" />
<p class="caption">Figure 1: Weight by group and period</p>
</div>


<div class="figure">
<img src="{{< blogdown/postref >}}index.en_files/figure-html/f2-1.png" alt="Weight distribution by group and period" width="672" />
<p class="caption">Figure 2: Weight distribution by group and period</p>
</div>

The average and variance for each group are essentially the same in each periods. However, individual weights did not remain constant...


### The problem


Through the years, several authors have discussed the original problem. Here we have several examples:

- **Lord (1967):** Granted the usual linearity assumptions of the analysis of covariance, **the conclusions of each statistician are visibly correct**. The usual research study of this type is attempting to answer a question that **simply cannot be answered in any rigorous way on the basis of available data**.

 - **Bock (1975):**Both statisticians in the scenario are correct once the question being asked is clarified. The first statistician asks: “Is there a difference in the average gain in weight of the population?” and correctly answered: “No!” The second statistician asks: “Is a man expected to show a greater weight gain than a woman, given that they are initially of the same weight?” and answers it correctly: “Yes!”

- **Cox and McCullagh (1982)**: The first statistician was right when asking about group differences, while the second was right when asking about the effect on an individual.

- **Holland and Rubin (1983)**: Both statisticians are correct: their statements simply describe the situation. But no causal conclusions can be reached. 

- **Pearl (2010)**: None of these early responses is satisfactory because they fail to address what misaligns with intuition, namely that the outcomes seemingly violate the sure-thing principle. This principle states that if a causal relation holds for all subpopulations, then it should hold for the whole population. **The two statisticians are asking two different questions, with the first one asking about the total effect, and the second asking about the direct effect**. 

The discussions deals with the necessity of adjusting by confounders, and wether in this case initial weight is a confounder, a mediator or what. 


### The Analysis of the original problem

**First Statistician:**

The first statistician uses the following model, with weigth change as response variable and sex as the only explanatory variable:


```r
(summary(lm(Change ~ sex,bd))) 
```
She obtains that there are no differences in weight change between sex groups.

**Second statistician:**

The second statistician, fits a similar model using the final weight as the response variable and corrects the analysis by the initial weight since it is a confounder.


```r
(summary(lm(September ~ June + sex ,bd))) 
```

She obtains that there are differences between boys and girls.


## My opinion

The previous approaches discuss the necessity of adjusting by a covariate if it is a confounder and to be very careful if it is a mediator. In the present example, since dinning halls and diets and sexes are confused with each other, we can agree that the study design is a complete disaster, and that no solid conclusions could be obtained.

See that according to the second statistician the diet effect can be described as 
 - Start with 27Kg 
 - Add 46% of the weight in June 
 - Add 7Kg for boys  

What kind of diet could produce this effect? It has no sense...

However, there is a different point of view that can add some clarity to understand why both statisticians reach different conclusions.


### Measurement error

Observing the results obteined by the second statistician we can realize that the effect produced by the diet (and given by the model) is quite implausible. The September weight is given by the model's formula and corresponds to an intercept plus a proportion of the weight in June and some amount depending on the sex and finally a random perturbation.   

This abnormal consequence of the diet could be explained by a wrong model proposal.

A simpler model could explain the results: What if we consider that the explanatory variable has been measured with error?

In this case, the model can be fitted now using as Structural Equation Model as follows:


```r
library(lavaan)
```

```
## This is lavaan 0.6-12
## lavaan is FREE software! Please report any bugs.
```

```r
bd$sex <- as.numeric(bd$sex)
mod = "
W0 =~ 1*June 
W0 ~~ w*W0
June ~~ v*June
September ~ 1 + W0 + sex
w == v * 3
"
summary(sem(mod, data=bd))
```

```
## lavaan 0.6-12 ended normally after 24 iterations
## 
##   Estimator                                         ML
##   Optimization method                           NLMINB
##   Number of model parameters                         7
##   Number of equality constraints                     1
## 
##   Number of observations                           200
## 
## Model Test User Model:
##                                                       
##   Test statistic                               145.060
##   Degrees of freedom                                 1
##   P-value (Chi-square)                           0.000
## 
## Parameter Estimates:
## 
##   Standard errors                             Standard
##   Information                                 Expected
##   Information saturated (h1) model          Structured
## 
## Latent Variables:
##                    Estimate  Std.Err  z-value  P(>|z|)
##   W0 =~                                               
##     June              1.000                           
## 
## Regressions:
##                    Estimate  Std.Err  z-value  P(>|z|)
##   September ~                                         
##     W0                0.604    0.053   11.347    0.000
##     sex               7.065    0.809    8.732    0.000
## 
## Intercepts:
##                    Estimate  Std.Err  z-value  P(>|z|)
##    .September        46.795    1.320   35.452    0.000
##    .June             58.141    0.716   81.158    0.000
##     W0                0.000                           
## 
## Variances:
##                    Estimate  Std.Err  z-value  P(>|z|)
##     W0         (w)   76.982    7.698   10.000    0.000
##    .June       (v)   25.661    2.566   10.000    0.000
##    .September        25.713    3.570    7.203    0.000
## 
## Constraints:
##                                                |Slack|
##     w - (v*3)                                    0.000
```

Now, the adjusted analysis including the covariate gives a similar result as the unadjusted model. Both statisticians would have obtained the same conclusion if they had considered that initial weight could have been measured with error.



### References

Pearl, J. 2014. *Lord's Paradox Revisited -- (Oh Lord! Kumbaya!)*. [link](http://ftp.cs.ucla.edu/pub/stat_ser/r436.pdf)

Senn, S. 2006. *Change from baseline and analysis of covariance revisited*. [link](http://onlinelibrary.wiley.com/doi/10.1002/sim.2682/pdf)

Clark, M. 2016. *Lord’s Paradox*. [link](https://github.com/m-clark/lords-paradox)

Lord, F. M. 1967. *A paradox in the interpretation of group comparisons*.Psychological Bulletin 68304–305.

Holland, P. and Rubin, D. 1983. *On Lord’s paradox. InPrincipals of Modern Psycho-logical Measurement*. Lawrence Earlbaum, Hillsdale, NJ,3–25

Bock, R. 1975. *Multivariate statistical methods in behavioral research*. McGraw-Hill, NY.

Cox, D. and McCullagh, P. 1982. *A biometrics invited paper with discussion. someaspects of analysis of covariance*. Biometrics 38541–561.


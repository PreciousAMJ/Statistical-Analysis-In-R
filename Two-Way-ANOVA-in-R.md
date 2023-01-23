Two-Way ANOVA in R
================

| A two-way ANOVA (“analysis of variance”) is used to determine whether or not there is a statistically significant difference between the means of three or more independent groups that have been split on two factors.                                                                                                                                                                                                                                                                                                      |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| \### *Exploring the Data*                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Before we even fit the two-way ANOVA model, we can gain a better understanding of the data by finding the mean weight by cholesterol_level and Height                                                                                                                                                                                                                                                                                                                                                                        |
| `r Data %>% group_by(Height, Cholesterol_level ) %>% summarise(Mean_Weight = mean(Weight))`                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `` ## `summarise()` has grouped output by 'Height'. You can override using the ## `.groups` argument. ``                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `## # A tibble: 9 × 3 ## # Groups:   Height [3] ##   Height Cholesterol_level Mean_Weight ##   <fct>  <fct>                   <dbl> ## 1 medium borderline               175. ## 2 medium high                     181. ## 3 medium normal                   172. ## 4 short  borderline               157. ## 5 short  high                     160. ## 6 short  normal                   187 ## 7 tall   borderline               194. ## 8 tall   high                     193. ## 9 tall   normal                   178` |
| `r #create boxplots boxplot(Weight ~ Cholesterol_level:Height , data = Data, main = "Weight Distribution by Group", xlab = "Group", ylab = "Weight", col = "steelblue", border = "black", las = 2 #make x-axis labels perpendicular )`                                                                                                                                                                                                                                                                                       |
| ![](Two-Way-ANOVA-in-R_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->                                                                                                                                                                                                                                                                                                                                                                                                                                                       |

### *Fitting the Two-Way ANOVA Model*

The general syntax to fit a two-way ANOVA model in R is as follows:

aov(response variable \~ predictor_variable1 \* predictor_variable2,
data = dataset)

Note that the \* between the two predictor variables indicates that we
also want to test for an interaction effect between the two predictor
variables.

``` r
model <- aov(Weight ~ Cholesterol_level * Height , data = Data)

summary(model) # We can then use the summary() function to view the output of our model
```

    ##                           Df Sum Sq Mean Sq F value  Pr(>F)   
    ## Cholesterol_level          2   2851    1426   0.898 0.40818   
    ## Height                     2  19800    9900   6.237 0.00216 **
    ## Cholesterol_level:Height   4   2707     677   0.426 0.78959   
    ## Residuals                387 614249    1587                   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

From the output we can see that only Height is statistically significant
at the .05 significance level, Cholesterol_level and the interaction
between the two predictor variables are not.

### *Checking the Model Assumptions*

Before we go any further, we should check to see that the assumptions of
our model are met so that the our results from the model are reliable.
In particular, a two-way ANOVA assumes:

1.  Independence – the observations in each group need to be independent
    of each other. Since we used a randomized design, this assumption
    should be met so we don’t need to worry too much about this.

2.  Normality – the dependent variable should be approximately normally
    distributed for each combination of the groups of the two factors.

One way to check this assumption is to create a histogram of the model
residuals. If the residuals are roughly normally distributed, this
assumption should be met.

``` r
#define model residuals
resid <- model$residuals

#create histogram of residuals
hist(resid, main = "Histogram of Residuals", xlab = "Residuals", col = "steelblue")
```

![](Two-Way-ANOVA-in-R_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->
The histogram looks right skewed, which suggest that normaliy assumption
may be violated.

3.  Equal Variance – the variances for each group are equal or
    approximately equal.

One way to check this assumption is to conduct a Levene’s Test for
equality of variances using the car packHeight :

``` r
#load car packHeight 
library(car)
```

    ## Loading required package: carData

    ## 
    ## Attaching package: 'car'

    ## The following object is masked from 'package:dplyr':
    ## 
    ##     recode

    ## The following object is masked from 'package:purrr':
    ## 
    ##     some

``` r
#conduct Levene's Test for equality of variances
leveneTest(model)
```

    ## Levene's Test for Homogeneity of Variance (center = median)
    ##        Df F value Pr(>F)
    ## group   8  1.3167 0.2334
    ##       387

Since the p-value of the test is greater than our significance level of
0.05, we can assume that our assumption of equality of variances among
groups is met.

### *Analyzing Group Differences*

Once we have verified that the model assumptions are met, we can then
conduct a post hoc test to determine exactly which groups differ from
one another.

For our post hoc test, we will use the function TukeyHSD() to conduct
Tukey’s Test for multiple comparisons:

``` r
#perform Tukey's Test for multiple comparisons
TukeyHSD(model, conf.level=.95)
```

    ##   Tukey multiple comparisons of means
    ##     95% family-wise confidence level
    ## 
    ## Fit: aov(formula = Weight ~ Cholesterol_level * Height, data = Data)
    ## 
    ## $Cholesterol_level
    ##                        diff        lwr       upr     p adj
    ## high-borderline    3.838291  -8.390006 16.066588 0.7407390
    ## normal-borderline -4.927760 -17.544171  7.688651 0.6286379
    ## normal-high       -8.766051 -24.188446  6.656344 0.3753628
    ## 
    ## $Height
    ##                   diff        lwr      upr     p adj
    ## short-medium -16.25509 -41.050772  8.54059 0.2723104
    ## tall-medium   15.13372   3.288556 26.97888 0.0079217
    ## tall-short    31.38881   4.989031 57.78859 0.0149184
    ## 
    ## $`Cholesterol_level:Height`
    ##                                          diff         lwr       upr     p adj
    ## high:medium-borderline:medium        5.504288  -13.150821  24.15940 0.9917425
    ## normal:medium-borderline:medium     -3.516402  -22.556837  15.52403 0.9997066
    ## borderline:short-borderline:medium -17.816402  -58.142838  22.51003 0.9055348
    ## high:short-borderline:medium       -14.616402  -77.409507  48.17670 0.9984039
    ## normal:short-borderline:medium      11.883598 -112.722730 136.48993 0.9999982
    ## borderline:tall-borderline:medium   18.720333   -1.202622  38.64329 0.0845930
    ## high:tall-borderline:medium         17.616931  -15.720547  50.95441 0.7771946
    ## normal:tall-borderline:medium        2.883598  -30.453881  36.22108 0.9999992
    ## normal:medium-high:medium           -9.020690  -32.411113  14.36973 0.9555719
    ## borderline:short-high:medium       -23.320690  -65.874128  19.23275 0.7404552
    ## high:short-high:medium             -20.120690  -84.366683  44.12530 0.9877630
    ## normal:short-high:medium             6.379310 -118.965457 131.72408 1.0000000
    ## borderline:tall-high:medium         13.216045  -10.898221  37.33031 0.7404046
    ## high:tall-high:medium               12.112644  -23.886818  48.11211 0.9806151
    ## normal:tall-high:medium             -2.620690  -38.620151  33.37877 0.9999998
    ## borderline:short-normal:medium     -14.300000  -57.023766  28.42377 0.9812463
    ## high:short-normal:medium           -11.100000  -75.458937  53.25894 0.9998243
    ## normal:short-normal:medium          15.400000 -110.002694 140.80269 0.9999870
    ## borderline:tall-normal:medium       22.236735   -2.176846  46.65032 0.1069418
    ## high:tall-normal:medium             21.133333  -15.067306  57.33397 0.6681576
    ## normal:tall-normal:medium            6.400000  -29.800640  42.60064 0.9997884
    ## high:short-borderline:short          3.200000  -70.323847  76.72385 1.0000000
    ## normal:short-borderline:short       29.700000 -100.643849 160.04385 0.9986303
    ## borderline:tall-borderline:short    36.536735   -6.587576  79.66105 0.1725289
    ## high:tall-borderline:short          35.433333  -15.302941  86.16961 0.4218098
    ## normal:tall-borderline:short        20.700000  -30.036274  71.43627 0.9386931
    ## normal:short-high:short             26.500000 -112.447010 165.44701 0.9996272
    ## borderline:tall-high:short          33.336735  -31.288792  97.96226 0.7994949
    ## high:tall-high:short                32.233333  -37.701795 102.16846 0.8825442
    ## normal:tall-high:short              17.500000  -52.435128  87.43513 0.9973337
    ## borderline:tall-normal:short         6.836735 -118.702987 132.37646 1.0000000
    ## high:tall-normal:short               5.733333 -122.620416 134.08708 1.0000000
    ## normal:tall-normal:short            -9.000000 -137.353750 119.35375 0.9999998
    ## high:tall-borderline:tall           -1.103401  -37.775901  35.56910 1.0000000
    ## normal:tall-borderline:tall        -15.836735  -52.509235  20.83577 0.9162946
    ## normal:tall-high:tall              -14.733333  -60.113237  30.64657 0.9845538

The p-value indicates whether or not there is a statistically
significant difference between each group. since non is less than 0.05,
we’ll fail to reject the null hypothesis that there’s no difference in
mean, for each group.

``` r
#set axis margins so labels don't get cut off
par(mar=c(4.1, 13, 4.1, 2.1))

#create confidence interval for each comparison
plot(TukeyHSD(model, conf.level=.95), las = 2)
```

![](Two-Way-ANOVA-in-R_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->![](Two-Way-ANOVA-in-R_files/figure-gfm/unnamed-chunk-9-2.png)<!-- -->![](Two-Way-ANOVA-in-R_files/figure-gfm/unnamed-chunk-9-3.png)<!-- -->
If your confidence interval for a correlation or regression includes
zero, that means that if you run your experiment again there is a good
chance of finding no correlation in your data

------------------------------------------------------------------------

### *Reporting the Results of the Two-Way ANOVA*

Lastly, we can report the results of the two-way ANOVA in such a way
that summarizes the findings:

A two-way ANOVA was conducted to examine the effects of
cholesterol_level and height on weight. There was no statistically
significant interaction betwen the effect of cholesterol_level and
height combined on weight, though height seems to affect weight.

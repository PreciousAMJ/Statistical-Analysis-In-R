Good Practice Steps Before Regression - Correlation
================

``` r
COPD <- read.csv("C:\\Users\\HP\\Documents\\Datasets\\COPD_student_dataset.csv") # used \\ instead of \ due to an error

View(COPD)
```

``` r
hist(COPD$MWT1Best)
```

![](Good-Practice-Steps-Before-Regression---Correlation_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

You can adjust the graph in different ways:

Change the title by adding the command main = “New figure title” in the
function parentheses;

Change the x or y axis labels by using the commands xlab = “New x axis
label” or ylab = “New y axis label”;

Change the number of bins displayed by using the command breaks = and
specifying the number of bins you want displayed.

``` r
 hist(COPD$MWT1Best, main="Histogram of MWT1Best", xlab="MWT1Best", breaks=12)
```

![](Good-Practice-Steps-Before-Regression---Correlation_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

From these, you can see that the distribution looks reasonably symmetric
and there is a distinctive peak with a high proportion of patients
walking between 400 to 500 meters in 6 minutes. There are no obvious
impossible values, but there is quite a high value which is above 650.
For this specific value, you can look at it using the *subset()*
function. The basic code for this is: subset(dataframe, variable \> x),
so here:

``` r
subset(COPD, MWT1Best > 650)
```

    ##       X  ID AGE PackHistory COPDSEVERITY MWT1 MWT2 MWT1Best FEV1 FEV1PRED  FVC
    ## 100 100 108  54          30       SEVERE  688  699      699 1.72       44 4.07
    ##     FVCPRED CAT HAD  SGRQ AGEquartiles copd gender smoking Diabetes muscular
    ## 100      82  10  10 20.55            1    3      1       2        0        0
    ##     hypertension AtrialFib IHD
    ## 100            0         0   0

If you now want to look at more than one value, let’s say samples which
have values of MWT1Best over 650 and under 150, you can use the
following code:

``` r
 subset(COPD, MWT1Best > 600 | MWT1Best < 150) #where ‘|’ stands for ‘and’. useful in finding extreme values
```

    ##       X  ID AGE PackHistory COPDSEVERITY MWT1 MWT2 MWT1Best FEV1 FEV1PRED  FVC
    ## 1     1  58  77          60       SEVERE  120  120      120 1.21       36 2.40
    ## 100 100 108  54          30       SEVERE  688  699      699 1.72       44 4.07
    ##     FVCPRED CAT HAD  SGRQ AGEquartiles copd gender smoking Diabetes muscular
    ## 1        98  25   8 69.55            4    3      1       2        1        0
    ## 100      82  10  10 20.55            1    3      1       2        0        0
    ##     hypertension AtrialFib IHD
    ## 1              0         1   0
    ## 100            0         0   0

R has a summary() function which lists the minimum, the median, the
maximum, the 1st and 3rd quartiles, and the number of blank cells in
your variable. You can also look at the remaining descriptive statistics
using the mean() command for the mean, sd() command for the standard
deviation, the range() command to view the range of the variable, and
the IQR() command to see the inter-quartile range.

Note that these will give an error message if there is a missing value
(i.e. NA) in your dataset, and you therefore need to specify that you
want the missing values removed from the calculation using the command
na.rm = TRUE. You can look at all of these statistics together using the
list() function.

The output of the list function will assign the different statistics a
number value, in the order in which they were written. For clarity, you
can change these to names.

To view descriptive statistics for MWT1Best, type the command:

``` r
list("Summary" = summary(COPD$MWT1Best), "Mean" = mean(COPD$MWT1Best, na.rm=TRUE), "Standard Deviation" = sd(COPD$MWT1Best, na.rm=TRUE), "Range" = range(COPD$MWT1Best, na.rm=TRUE), "Inter-Quartile Range" = IQR(COPD$MWT1Best, na.rm=TRUE))
```

    ## $Summary
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
    ##   120.0   303.8   420.0   399.1   465.2   699.0       1 
    ## 
    ## $Mean
    ## [1] 399.11
    ## 
    ## $`Standard Deviation`
    ## [1] 106.5501
    ## 
    ## $Range
    ## [1] 120 699
    ## 
    ## $`Inter-Quartile Range`
    ## [1] 161.5

Let’s take a look at the scatterplot of the two variables. The basic
command for this is plot(x,y) and you can rename the axes in the same
way you did in the histogram.

``` r
plot(COPD$FEV1, COPD$MWT1Best, xlab = "FEV1", ylab = "MWT1Best") # note x takes the first variable as x, and the second as y  y default, though you can also indicate it.
```

![](Good-Practice-Steps-Before-Regression---Correlation_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

The scatterplot shows an even distribution over both lung function and
walking distance with no discontinuations in either, or obvious
outliers. So, it is reasonable to calculate the Pearson’s correlation
coefficient to assess a linear association between the two variables.
But we will also have a look at the Spearman’s for comparison.

The basic command for a correlation test is cor.test(x,y) where you can
specify which method you want to use using the command method =
“pearson” or “spearman”. You need to remove missing values, otherwise
you will have an error message. To do this, use the command use =
“complete.obs”

``` r
cor.test(COPD$MWT1, COPD$FEV1, use = "complete.obs" , method = "pearson")
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  COPD$MWT1 and COPD$FEV1
    ## t = 5.0835, df = 97, p-value = 1.803e-06
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  0.2872522 0.6015960
    ## sample estimates:
    ##       cor 
    ## 0.4586551

Pearson’s correlation coefficient is 0.47 and the 95% confidence
interval suggests the population coefficient is likely to lie somewhere
between 0.3 to 0.6. The p value is less than 0.001 so there is very
strong evidence against the null hypothesis of the coefficient being
zero.

The Spearman’s correlation coefficient provides a similar result with
estimated correlation of 0.45.

So, you have examined the raw data and found that it was fine, and that
there was only one missing value. You have discovered moderate
correlation between walking distance and lung function.

Now, you’ll practise this skill for yourself before moving on to
quantifying the relationship by fitting a regression model in R.

``` r
cor.test(COPD$MWT1, COPD$FEV1, use = "complete.obs" , method = "spearman")
```

    ## Warning in cor.test.default(COPD$MWT1, COPD$FEV1, use = "complete.obs", : Cannot
    ## compute exact p-value with ties

    ## 
    ##  Spearman's rank correlation rho
    ## 
    ## data:  COPD$MWT1 and COPD$FEV1
    ## S = 89438, p-value = 3.537e-06
    ## alternative hypothesis: true rho is not equal to 0
    ## sample estimates:
    ##       rho 
    ## 0.4468868

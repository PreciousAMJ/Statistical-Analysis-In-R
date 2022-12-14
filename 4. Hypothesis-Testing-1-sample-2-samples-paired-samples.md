Hypothesis Testing - 1 sample, 2 samples, paired samples.Rmd
================

------------------------------------------------------------------------

## Import DataSet

to open our data set, we can either go to environment -\> import data
set and open it or use the read.csv function;

``` r
Cancer_Data <- read.csv("C:/Users/HP/Documents/Datasets/Cancer Data.csv", header = TRUE, sep = ",")

View(Cancer_Data)
```

------------------------------------------------------------------------

## Chi-squared Test of independence

``` r
cancer <- Cancer_Data$cancer

bmi <- Cancer_Data$bmi

chisq.test(x = bmi, y = cancer)
```

    ## Warning in chisq.test(x = bmi, y = cancer): Chi-squared approximation may be
    ## incorrect

    ## 
    ##  Pearson's Chi-squared test
    ## 
    ## data:  bmi and cancer
    ## X-squared = 66, df = 59, p-value = 0.2478

i.e there’s no relationship between bmi and cancer

we can use group by to view the agg mean of those with and without
cancer

``` r
library("dplyr")
```

    ## Warning: package 'dplyr' was built under R version 4.2.1

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
cancer <- Cancer_Data$cancer

Cancer_Data %>%
  group_by(cancer) %>%
  summarise(mean(bmi))
```

    ## # A tibble: 2 × 2
    ##   cancer `mean(bmi)`
    ##    <int>       <dbl>
    ## 1      0        24.5
    ## 2      1        23.2

note that the means are actually close, at 95% CL there won’t be
significant difference between both means.

------------------------------------------------------------------------

## Two Sample Test (Independent-Samples)

to compare the mean BMI for those with cancer and those without cancer.
The two samples here are the BMI for groups 0 and group 1, and we
compare the mean between the two. So, it’ll kinda group the BMIs into
grps 1 and 0, then find the mean of both grps and perform hypothesis
test on them.

``` r
bmi <- Cancer_Data$bmi # assigning the bmi column to a variable "bmi"

t.test(bmi~cancer)
```

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  bmi by cancer
    ## t = 0.90034, df = 21.878, p-value = 0.3777
    ## alternative hypothesis: true difference in means between group 0 and group 1 is not equal to 0
    ## 95 percent confidence interval:
    ##  -1.735200  4.396204
    ## sample estimates:
    ## mean in group 0 mean in group 1 
    ##         24.5198         23.1893

``` r
 # alternative = grester for right tailed, lesser for left tailed.
```

In general, the command is of the format t.test(y,x), where we think x
predicts y, (x is the predictor, y is the predicted) and where *both x
and y are continuous*, and t.test(y\~x) where *x is binary(two
samples)*. In R statistical functions, the variable(s) to the *left* of
a tilde (\~ symbol) represent the things you’re trying to predict using
the variable(s) to the *right* of the tilde. And we are trying to use
T.test to see how significant the difference in means between the two
groups are (if there is). looking at the P-value (it’s high), it shows
we can’t reject the null hypothesis. So the difference in mean bmi
between those that have cancer and those that don’t have cancer is not
significantly different (i.e = 0). So BMI does not affect cancer.

we can use group by to view the agg mean of those with and without
cancer

``` r
library("dplyr")

cancer <- Cancer_Data$cancer

Cancer_Data %>%
  group_by(cancer) %>%
  summarise(mean(bmi))
```

    ## # A tibble: 2 × 2
    ##   cancer `mean(bmi)`
    ##    <int>       <dbl>
    ## 1      0        24.5
    ## 2      1        23.2

``` r
sample1 = c(300, 315, 320, 311, 314, 309, 300, 308, 305, 303, 305, 301, 303)

sample2 = c(335, 329, 322, 321, 324, 319, 304, 308, 305, 311, 307, 300, 305)

t.test(sample1, sample2) # to see if there's a difference in mean between both samples
```

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  sample1 and sample2
    ## t = -2.1009, df = 19.112, p-value = 0.04914
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -14.73862953  -0.03060124
    ## sample estimates:
    ## mean of x mean of y 
    ##  307.2308  314.6154

``` r
library(BSDA)
```

    ## Warning: package 'BSDA' was built under R version 4.2.1

    ## Loading required package: lattice

    ## 
    ## Attaching package: 'BSDA'

    ## The following object is masked from 'package:datasets':
    ## 
    ##     Orange

``` r
z.test(x = sample1, y = sample2, mu = 0, sigma.x = sd(sample1), sigma.y = sd(sample2), alternative = "two.sided" ) # in 2sample test, mu refers to the mean difference (null says the mean difference is 0)
```

    ## 
    ##  Two-sample z-Test
    ## 
    ## data:  sample1 and sample2
    ## z = -2.1009, p-value = 0.03565
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -14.2738343  -0.4953965
    ## sample estimates:
    ## mean of x mean of y 
    ##  307.2308  314.6154

------------------------------------------------------------------------

## Paired Samples Test(dependent)

is used to compare the means of two samples when each observation in one
sample can be paired with an observation in the other sample. For
example, suppose we want to know whether or not a certain training
program is able to increase the max vertical jump (in inches) of
basketball players. To test this, we may recruit a simple random sample
of 12 college basketball players and measure each of their max vertical
jumps. Then, we may have each player use the training program for one
month and then measure their max vertical jump again at the end of the
month.

The following data shows the max jump height (in inches) before and
after using the training program for each player:

``` r
#define before and after max jump heights
before = c(22, 24, 20, 19, 19, 20, 22, 25, 24, 23, 22, 21)

after = c(23, 25, 20, 24, 18, 22, 23, 28, 24, 25, 24, 20)

t.test(before, after, paired = TRUE, alternative = "two.sided")
```

    ## 
    ##  Paired t-test
    ## 
    ## data:  before and after
    ## t = -2.5289, df = 11, p-value = 0.02803
    ## alternative hypothesis: true mean difference is not equal to 0
    ## 95 percent confidence interval:
    ##  -2.3379151 -0.1620849
    ## sample estimates:
    ## mean difference 
    ##           -1.25

``` r
library(BSDA)
# If the assumed population difference is zero (as stated in the null hypothesis), the paired Z-test reduces to the one sample Z-test. Hence, we will perform one sample Z-test on paired differences.
diff <- before - after

z.test(diff, mu = 0, sigma.x = sd(diff))
```

    ## 
    ##  One-sample z-Test
    ## 
    ## data:  diff
    ## z = -2.5289, p-value = 0.01144
    ## alternative hypothesis: true mean is not equal to 0
    ## 95 percent confidence interval:
    ##  -2.2187818 -0.2812182
    ## sample estimates:
    ## mean of x 
    ##     -1.25

for both tests, the p-value is less than 0.05, thus we’ll reject the
null (there’s a significant difference in mean between both samples,
it’s not 0)

solving it manually

``` r
diff <- before - after

mean_diff <- mean(diff)

score = mean_diff/(sd(diff)/sqrt(12))
score
```

    ## [1] -2.528903

``` r
2*pt(q = score, df = 11, lower.tail = TRUE) # we multiply by 2 because it's a two tailed test
```

    ## [1] 0.02802807

------------------------------------------------------------------------

## One Sample Test

``` r
t.test(bmi, mu = 25)
```

    ## 
    ##  One Sample t-test
    ## 
    ## data:  bmi
    ## t = -1.3061, df = 65, p-value = 0.1961
    ## alternative hypothesis: true mean is not equal to 25
    ## 95 percent confidence interval:
    ##  23.02077 25.41406
    ## sample estimates:
    ## mean of x 
    ##  24.21742

``` r
library(BSDA)
z.test(bmi, mu = 25, sigma.x = sd(bmi)) # the sample (bmi) sd is used in place of the population sd
```

    ## 
    ##  One-sample z-Test
    ## 
    ## data:  bmi
    ## z = -1.3061, p-value = 0.1915
    ## alternative hypothesis: true mean is not equal to 25
    ## 95 percent confidence interval:
    ##  23.04304 25.39179
    ## sample estimates:
    ## mean of x 
    ##  24.21742

So the p-value shows that though the means are different (25 and
24.21742) it’s not significantly different at 95% CI.

``` r
data = c(300, 315, 320, 311, 314, 309, 300, 308, 305, 303, 305, 301, 303)
mean(data)
```

    ## [1] 307.2308

``` r
t.test(data, mu = 310) # to see if the mean of our data is different from 310
```

    ## 
    ##  One Sample t-test
    ## 
    ## data:  data
    ## t = -1.5848, df = 12, p-value = 0.139
    ## alternative hypothesis: true mean is not equal to 310
    ## 95 percent confidence interval:
    ##  303.4236 311.0379
    ## sample estimates:
    ## mean of x 
    ##  307.2308

we can’t reject the null hypothesis because the p value is not less than
0.05

using Z-test method

``` r
library(BSDA)

z.test(data, mu = 310, sigma.x = sd(data), alternative = "two.sided")
```

    ## 
    ##  One-sample z-Test
    ## 
    ## data:  data
    ## z = -1.5848, p-value = 0.113
    ## alternative hypothesis: true mean is not equal to 310
    ## 95 percent confidence interval:
    ##  303.8060 310.6555
    ## sample estimates:
    ## mean of x 
    ##  307.2308

Solving it manually

``` r
score <- (mean(data) - 310) / (sd(data)/sqrt(13))
score
```

    ## [1] -1.584812

``` r
2*pt(q = score , df = 12, lower.tail = TRUE) # we multiply by 2 because it's a two tailed test
```

    ## [1] 0.1389944

``` r
2*pnorm(q = score, lower.tail = TRUE) # for z-test
```

    ## [1] 0.1130091

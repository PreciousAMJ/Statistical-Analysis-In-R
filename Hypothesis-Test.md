Hypothesis Test 2
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

## Two Sample T.test (Independent-Samples)

to compare the mean BMI for those with cancer and those without cancer.
The two samples here are the BMI for groups 0 and group 1, and we
compare the mean between the two. So, it’ll kinda group the BMIs into
grps 1 and 0, then find the mean of both grps and perform hypothesis
test on them.

``` r
bmi <- Cancer_Data$bmi # assigning the bmi column to a variable "bmi"

cancer <- Cancer_Data$cancer

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

In general, the command is of the format t.test(y,x), where we think x
predicts y (x is the predictor, y is the predicted) and where both x and
y are continuous, and t.test(y\~x) where x is binary(two samples). In R
statistical functions, the variable(s) to the *left* of a tilde (\~
symbol) represent the things you’re trying to predict using the
variable(s) to the *right* of the tilde. And we are trying to use T.test
to see how significant the difference in means between the two groups
are (if there is). looking at the P-value (it’s high), it shows we can’t
reject the null hypothesis.

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

------------------------------------------------------------------------

## One Sample T.test

to test if the mean of a sample is equal to a certain mean here, we test
if the mean of the sample(bmi) is equal to 25 (so 25 will be the
population mean and the default CI will be 95%)

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

So the p-value shows that though the means are different (25 and
24.21742) it’s not significantly different at 95% CI.

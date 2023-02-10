One-Way ANOVA in R
================

A one-way ANOVA is used to determine whether or not there is a
statistically significant difference between the means of *three or more
independent groups*.

This type of test is called a one-way ANOVA because we are analyzing how
one predictor variable impacts a response variable.

Note: If we were instead interested in how two predictor variables
impact a response variable, we could conduct a two-way ANOVA.

We can conduct a one-way ANOVA to determine if there is a statistically
significant difference between COPD severity (with 4 groups; mild,
moderate, severe and very severe) and forced vital capacity (FVC,the
amount of air that can be forcibly exhaled from your lungs after taking
the deepest breath possible, as measured by spirometry. This test may
help distinguish obstructive lung diseases, such as asthma and COPD,
from restrictive lung diseases, such as pulmonary fibrosis and
sarcoidosis).

load the dataset

``` r
library(readr)

library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.3.6     ✔ dplyr   1.0.9
    ## ✔ tibble  3.1.8     ✔ stringr 1.4.0
    ## ✔ tidyr   1.2.0     ✔ forcats 0.5.1
    ## ✔ purrr   0.3.4     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
setwd("C:\\Users\\HP\\Documents\\Datasets")

COPD_student_dataset <- read_csv("C:\\Users\\HP\\Documents\\Datasets\\COPD_student_dataset.csv")
```

    ## New names:
    ## Rows: 101 Columns: 24
    ## ── Column specification
    ## ──────────────────────────────────────────────────────── Delimiter: "," chr
    ## (1): COPDSEVERITY dbl (23): ...1, ID, AGE, PackHistory, MWT1, MWT2, MWT1Best,
    ## FEV1, FEV1PRED, ...
    ## ℹ Use `spec()` to retrieve the full column specification for this data. ℹ
    ## Specify the column types or set `show_col_types = FALSE` to quiet this message.
    ## • `` -> `...1`

The variables we’ll use.

``` r
#Age_Group <- as.factor(COPD_student_dataset$AGEquartiles)

COPD_Severity  <- as.factor(COPD_student_dataset$COPDSEVERITY)

Forced_Vital_Capacity <- COPD_student_dataset$FVC

Data = data.frame(COPD_Severity, Forced_Vital_Capacity)
```

### *Exploring the Data*

Before we even fit the one-way ANOVA model, we can gain a better
understanding of the data by finding the mean FVC for each group

``` r
Data %>%
  group_by(COPD_Severity) %>%
  summarise(mean_FVC = mean(Forced_Vital_Capacity))
```

    ## # A tibble: 4 × 2
    ##   COPD_Severity mean_FVC
    ##   <fct>            <dbl>
    ## 1 MILD              3.77
    ## 2 MODERATE          2.97
    ## 3 SEVERE            2.58
    ## 4 VERY SEVERE       1.82

we can also create a boxplot to visualize the distribution of FVC for
each level of severity

``` r
boxplot(Forced_Vital_Capacity ~ COPD_Severity,
data = Data,
main = "FVC distribution by COPD severity level",
xlab = "COPD",
ylab = "FVC",
col = "steelblue",
border = "black")
```

![](One-Way-ANOVA-in-R_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->
From both the table and the boxplot it’s obvious that the FVC value
decreases from mild severity down to very severe.

### *Fitting the One-Way ANOVA Model*

Next, we’ll fit the one-way ANOVA model to our data to see if these
visual differences are actually statistically significant. The general
syntax to fit a one-way ANOVA model in R is as follows:

aov(response variable \~ predictor_variable, data = dataset)

``` r
model <- aov(Forced_Vital_Capacity ~ COPD_Severity, data = Data)

#view the model output
summary(model)
```

    ##               Df Sum Sq Mean Sq F value   Pr(>F)    
    ## COPD_Severity  3  29.48   9.828   14.48 7.21e-08 ***
    ## Residuals     97  65.83   0.679                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

From the model output we can see that the predictor variable
COPD_Severity is statistically significant at the .05 significance
level.

In other words, there is a statistically significant difference between
the mean FVC between the 4 COPD severity levels.

### *Checking the Model Assumptions*

Before we go any further, we should check to see that the assumptions of
our model are met so that the our results from the model are reliable.
In particular, a one-way ANOVA assumes:

1.  Independence – the observations in each group need to be independent
    of each other. Since we used a randomized design (i.e. we assigned
    participants to the exercise programs randomly), this assumption
    should be met so we don’t need to worry too much about this.

2.  Normality – the dependent variable should be approximately normally
    distributed for each level of the predictor variable.

3.  Equal Variance – the variances for each group are equal or
    approximately equal.

One way to check the assumptions of normality and equal variance is to
use the function plot(), which produces four model-checking plots. In
particular, we are most interested in the following two plots:

Residuals vs Fitted – this plot shows the relationship between the
residuals and the fitted values. We can use this plot to roughly gauge
whether or not the variance between the groups is approximately equal.
Q-Q Plot – this plot displays the standardized residuals against the
theoretical quantiles. We can use this plot to roughly gauge whether or
not the normality assumption is met. The following code can be used to
produce these model-checking plots:

``` r
plot(model)
```

![](One-Way-ANOVA-in-R_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->![](One-Way-ANOVA-in-R_files/figure-gfm/unnamed-chunk-6-2.png)<!-- -->![](One-Way-ANOVA-in-R_files/figure-gfm/unnamed-chunk-6-3.png)<!-- -->![](One-Way-ANOVA-in-R_files/figure-gfm/unnamed-chunk-6-4.png)<!-- -->
The Residuals vs Fitted plot above allows us to check our equal
variances assumption. Ideally we’d like to see the residuals be equally
spread out for each level of the fitted values.

To formally test for equal variances, we could Levene’s Test using the
car package:

``` r
#load car package
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
leveneTest(Forced_Vital_Capacity ~ COPD_Severity, data = Data)
```

    ## Levene's Test for Homogeneity of Variance (center = median)
    ##       Df F value  Pr(>F)  
    ## group  3  2.2405 0.08841 .
    ##       97                  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

The P value is 0.08841 which implies that we’ll reject the null
hypothesis that the variances are equal across the 4 groups, i.e the
assumption of equal variance is not meet. Although we could attempt to
transform the data to make sure that our assumptions of normality and
equality of variances are met, at the time being we won’t worry too much
about this.

### *Analyzing Severity Group Differences*

Once we have verified that the model assumptions are met (or reasonably
met), we can then conduct a post hoc test to determine exactly which
Severity groups differ from one another.

For our post hoc test, we will use the function TukeyHSD() to conduct
Tukey’s Test for multiple comparisons:

``` r
#perform Tukey's Test for multiple comparisons
TukeyHSD(model, conf.level=.95) 
```

    ##   Tukey multiple comparisons of means
    ##     95% family-wise confidence level
    ## 
    ## Fit: aov(formula = Forced_Vital_Capacity ~ COPD_Severity, data = Data)
    ## 
    ## $COPD_Severity
    ##                            diff        lwr        upr     p adj
    ## MODERATE-MILD        -0.7987260 -1.3550506 -0.2424014 0.0016692
    ## SEVERE-MILD          -1.1915942 -1.8026680 -0.5805204 0.0000100
    ## VERY SEVERE-MILD     -1.9532609 -2.8372082 -1.0693135 0.0000005
    ## SEVERE-MODERATE      -0.3928682 -0.9216632  0.1359268 0.2174792
    ## VERY SEVERE-MODERATE -1.1545349 -1.9837368 -0.3253329 0.0024464
    ## VERY SEVERE-SEVERE   -0.7616667 -1.6285519  0.1052185 0.1058510

The p-value indicates whether or not there is a statistically
significant difference between each program. We can see from the output
that there is a statistically significant difference between moderate
and mild, severe and mild, very severe and mild and ver severe and
moderate.

We can also visualize the 95% confidence intervals that result from the
Tukey Test by using the plot(TukeyHSD()) function in R:

``` r
#create confidence interval for each comparison
plot(TukeyHSD(model, conf.level=.95), las = 2)
```

![](One-Way-ANOVA-in-R_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->
If your confidence interval for a correlation or regression includes
zero, that means that if you run your experiment again there is a good
chance of finding no correlation in your data

### *Reporting the Results of the One-Way ANOVA*

Lastly, we can report the results of the one-way ANOVA in such a way
that summarizes the findings: A one-way ANOVA was conducted to examine
the relationship between FVC and COPD severity. There was a
statistically significant difference in FVC for the COPD severity
groups, (F(3, 97) = 14.48, p = 7.21e-08 ). Tukey’s HSD post hoc tests
were carried out.

The mean FVC value for the moderate severity group is statistically
lesser than the mean FVC for the mild severity group.

The mean FVC for the severe severity group is statistically lesser than
the mean FVC for the mild severity group.

The mean FVC for the very severe severity group is statisically lesser
than the mean FVC for the mild severity group.

In addition, the mean FVC for the very severe severity group and the
moderate severity group is statistically lesser than the moderate
severity group.

Normality checks and Levene’s test were conducted to verify that the
ANOVA assumptions were met.

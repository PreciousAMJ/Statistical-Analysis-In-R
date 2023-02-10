Chi-Square Test of Independence in R
================

A Chi-Square test of independence can be used to determine if there is
an association between two *categorical variables*.

We’ll perform chi-Square test of independence to determine if there’s
anm association between COPD (chronic obstructive pulmonary disease)
severity and gender.

we’ll import our dataset

``` r
library(readr)

setwd("C:\\Users\\HP\\Documents\\Datasets")

COPD_Dataset <- read_csv("C:\\Users\\HP\\Documents\\Datasets\\COPD_student_dataset.csv")
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

We’ll add the variables we’ll use into a table.

``` r
Data <- table(COPD_Dataset$COPDSEVERITY, COPD_Dataset$gender)
```

For the variable “gender”, 0 means females, 1 means males.

Perform Chi-Square test of independence on our data.

``` r
chisq.test(Data)
```

    ## Warning in chisq.test(Data): Chi-squared approximation may be incorrect

    ## 
    ##  Pearson's Chi-squared test
    ## 
    ## data:  Data
    ## X-squared = 1.8555, df = 3, p-value = 0.6029

### *Interpretation*

Chi-Square Test Statistic: 1.8555

Degrees of freedom: 3 (calculated as \#rows-1 \* \#columns-1)

p-value: 0.6029

Recall that the Chi-Square Test of Independence uses the following null
and alternative hypotheses:

H0: (null hypothesis) The two variables are independent. H1:
(alternative hypothesis) The two variables are not independent. Since
the p-value (0.6029) of the test is not less than 0.05, we fail to
reject the null hypothesis. This means we do not have sufficient
evidence to say that there is an association between gender and COPD
severity.

In other words, gender and COPD severity are independent.

If a warning such as “Chi-squared approximation may be incorrect”
appears, it means that the smallest expected frequencies is lower than
5. To avoid this issue, you can either:

gather some levels (especially those with a small number of
observations) to increase the number of observations in the subgroups,
or use the Fisher’s exact test The *Fisher’s exact test* does not
require the assumption of a minimum of 5 expected counts in the
contingency table. It can be applied in R thanks to the function
fisher.test(). This test is similar to the Chi-square test in terms of
hypothesis and interpretation of the results. Learn more about this test
in this article dedicated to this type of test.

Talking about assumptions, the Chi-square test of independence requires
that the observations are independent. This is usually not tested
formally, but rather verified based on the design of the experiment and
on the good control of experimental conditions. If you are not sure, ask
yourself if one observation is related to another (if one observation
has an impact on another). If not, it is most likely that you have
independent observations.

If you have dependent observations (paired samples), *the McNemar’s or
Cochran’s Q tests should be used instead*. The McNemar’s test is used
when we want to know if there is a significant change in two paired
samples (typically in a study with a measure before and after on the
same subject) when the variables have only two categories. The Cochran’s
Q tests is an extension of the McNemar’s test when we have more than two
related measures.

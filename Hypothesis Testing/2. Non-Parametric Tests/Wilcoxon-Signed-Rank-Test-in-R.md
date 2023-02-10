Wilcoxon Signed-Rank Test in R
================

### *Introduction*

A Wilcoxon signed-rank test is performed when an analyst would like to
test for differences between two related treatments or conditions, but
the assumptions of a paired samples t-test are violated. This can occur
when when difference between repeated measurements are not normally
distributed, or if outliers exist. A Wilcoxon signed-rank is considered
a “within -subject” analysis or “repeated measures” analysis.

Repeated measures can occur over time or space. For example, you may
want to know if students in a class scored better on the final exam than
they did on a midterm exam. However, many students scored dramatically
better on the final exam compared to the previous midterm. This results
in outliers and difference in exam scores that are not normally
distributed. A Wilcoxon signed-rank test would be more appropriate than
a paired samples t-test in this situation. Since the same student is
measured at two separate time points, the measurements are considered
repeated over time.

Repeated measures over space can be a little more difficult to
understand. For example, you may want to check for differences in blood
pressure between measurements taken on the right arm and left arm. Since
the same study subject is measured with both treatment conditions in two
locations, this would be considered a repeated measurement over space.

Like a paired samples t-test, a Wilcoxon signed-rank is performed when
each experimental unit (study subject), receives both available
treatment conditions. Thus, the treatment groups have overlapping
membership and are considered dependent.

Formally, the two-sided null hypothesis is that the *difference* between
pairs follows a symmetric distribution around zero. That is, the
difference between repeated measurements are equally positive and
negative. The alternative hypothesis is that the difference between the
pairs does not follow a symmetric distribution around zero.

Informally, we are testing to see if the median difference between pairs
of observations is equal to zero. Many time analysts will indicate that
we are testing to see if medians differ between repeated measurements
even though this may not be considered formally correct. However for
this reason, many times descriptive statistics regarding median values
are provided when the Wilcoxon signed-rank test is performed.

The two-sided null hypothesis is that mean treatment differences are
equal to zero. The alternative hypothesis is that the mean treatment
difference is not equal to zero.

H0: Paired rank differences are symmetrically distributed around zero
Ha: Paired rank differences are not symmetrically distributed around
zero

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

data <- read_csv("C:\\Users\\HP\\Documents\\Datasets\\CaffineWSR.csv")
```

    ## Rows: 9 Columns: 3
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl (3): Subject, Dose0, Dose13
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

Compute the difference, and add it to the dataframe

``` r
#Compute the difference, and add it to the dataframe
data$Diff <-data$Dose13 - data$Dose0

print(data)
```

    ## # A tibble: 9 × 4
    ##   Subject Dose0 Dose13  Diff
    ##     <dbl> <dbl>  <dbl> <dbl>
    ## 1       1  36.0   37.6  1.5 
    ## 2       2  52.5   59.3  6.83
    ## 3       3  56.6   79.1 22.6 
    ## 4       4  45.2   58.3 13.1 
    ## 5       5  35.2   70.5 35.3 
    ## 6       6  66.4   69.5  3.09
    ## 7       7  40.6   46.5  5.91
    ## 8       8  57.2   66.4  9.2 
    ## 9       9  28.3   36.2  7.86

Create a ‘long or ’tall’ dataset for descriptive statistics

``` r
#Create a 'long or 'tall' dataset for descriptive statistics
data_long<-gather(data, Dose, Minutes, Dose0, Dose13, factor_key=TRUE)

print(data_long)
```

    ## # A tibble: 18 × 4
    ##    Subject  Diff Dose   Minutes
    ##      <dbl> <dbl> <fct>    <dbl>
    ##  1       1  1.5  Dose0     36.0
    ##  2       2  6.83 Dose0     52.5
    ##  3       3 22.6  Dose0     56.6
    ##  4       4 13.1  Dose0     45.2
    ##  5       5 35.3  Dose0     35.2
    ##  6       6  3.09 Dose0     66.4
    ##  7       7  5.91 Dose0     40.6
    ##  8       8  9.2  Dose0     57.2
    ##  9       9  7.86 Dose0     28.3
    ## 10       1  1.5  Dose13    37.6
    ## 11       2  6.83 Dose13    59.3
    ## 12       3 22.6  Dose13    79.1
    ## 13       4 13.1  Dose13    58.3
    ## 14       5 35.3  Dose13    70.5
    ## 15       6  3.09 Dose13    69.5
    ## 16       7  5.91 Dose13    46.5
    ## 17       8  9.2  Dose13    66.4
    ## 18       9  7.86 Dose13    36.2

### *Descriptive Statistics*

Many times, analysts forget to take a good look at their data prior to
performing statistical tests. Descriptive statistics are not only used
to describe the data but also help determine if any inconsistencies are
present. Detailed investigation of descriptive statistics can help
answer the following questions (in addition to many others):

How much missing data do I have? Do I have potential outliers? Are my
standard deviation and standard error values large relative to the mean?
In what range most of my data fall for each treatment?

Produce descriptive statistics by group

``` r
#Produce descriptive statistics by group
data_long %>% select(Minutes, Dose) %>% group_by(Dose) %>% 
  summarise(n = n(), 
            mean = mean(Minutes, na.rm = TRUE), 
            sd = sd(Minutes, na.rm = TRUE),
            stderr = sd/sqrt(n), 
            LCL = mean - qt(1 - (0.05 / 2), n - 1) * stderr,
            UCL = mean + qt(1 - (0.05 / 2), n - 1) * stderr,
            median=median(Minutes, na.rm = TRUE),
            min=min(Minutes, na.rm = TRUE), 
            max=max(Minutes, na.rm = TRUE),
            IQR=IQR(Minutes, na.rm = TRUE))
```

    ## # A tibble: 2 × 11
    ##   Dose       n  mean    sd stderr   LCL   UCL median   min   max   IQR
    ##   <fct>  <int> <dbl> <dbl>  <dbl> <dbl> <dbl>  <dbl> <dbl> <dbl> <dbl>
    ## 1 Dose0      9  46.4  12.5   4.16  36.8  56.0   45.2  28.3  66.4  20.5
    ## 2 Dose13     9  58.1  15.1   5.04  46.5  69.8   59.3  36.2  79.1  23.0

Dose– This column identifies the levels of the treatment variable. n –
This column identifies how many data points (cyclists) are in each dose
category. mean – The mean endurance performance for each dosage. sd –
The endurance performance standard deviation of each dosage. stderr –
The endurance performance standard error of each dosage. LCL, UCL – The
upper and lower confidence intervals of the mean. That is to say, you
can be 95% certain that the true mean falls between the lower and upper
values specified for each treatment group, assuming the data is normally
distributed. median – The median endurance performance for each dosage.
min, max – The minimum and maximum endurance performance observed for
each dosage. IQR – The interquartile range endurance performance for
each dosage level. The interquartile range is the 75th percentile – 25th
percentile.

### *Normality Tests*

Prior to performing a paired t-test or a Wilcoxon signed-rank, it is
important to validate our assumptions to ensure that we are performing
an appropriate and reliable comparison. Testing normality should be
performed on the dosage differences using a Shapiro-Wilk normality test
(or equivalent), and/or a QQ plot for large sample sizes. Many times,
histograms can also be helpful, especially for large sample sizes. In
this example, we will use the shapiro.test function from the stats
package to produce our Shapiro-Wilk normality test for the daily
difference, and the qqPlot function from the qqplotr package to produce
QQ plots.

Perform the Shapiro-Wilk Test for Normality on each group

``` r
shapiro.test(data$Diff)
```

    ## 
    ##  Shapiro-Wilk normality test
    ## 
    ## data:  data$Diff
    ## W = 0.83208, p-value = 0.04712

W – The Shapiro-Wilk (W) test statistics for each test is provided for
each group. p-value – The p-value for each test is provided. A p-value
\< 0.05 would indicate that we should reject the assumption of
normality. Since the Shapiro-Wilk Test p-values = 0.047, we conclude the
data is not normally distributed. Thus, a Wilcoxon signed-rank test is
more appropriate than a paired t-test to evaluate this data.

### *Boxplots to Visually Check for Outliers*

The ggplot2 package provides a box plot of dosage differences. This can
help visually identify outliers.

``` r
#Produce boxplots and visually check for outliers
ggplot(data, aes(x = "", y = Diff)) +
  stat_boxplot(geom ="errorbar", width = 0.5) +
  geom_boxplot(fill = "steelblue") + 
  stat_summary(fun=mean, geom="point", shape=10, size=3.5, color="black") + 
  ggtitle("Boxplot of day 3 - day 1 differences") + 
  theme_bw() + theme(legend.position="none")
```

![](Wilcoxon-Signed-Rank-Test-in-R_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->
The boxplot below shows one potential outlier outside the upper whiskers
of the plot. Rank based tests such as the Wilcoxon signed-rank test are
appropriate when outliers are present. It also shows that the mean(dot
with cross) and the median(the thick black line) are at two separate
points, suggesting that the distribution is not normal.

### *Wilcoxon Signed-Rank Test in R*

So far, we have determined that the differences between dosages levels
is not normally distributed and we do have a major influential outlier.
Our next step is to officially perform a Wilcoxon signed-rank test to
determine if there is a statistically significant difference in
endurance performance between caffeine dosage levels.

``` r
#Perform a Wilcoxon signed rank test
Model <-wilcox.test(data$Dose13, data$Dose0, paired=TRUE, conf.int=TRUE)

print(Model)
```

    ## 
    ##  Wilcoxon signed rank exact test
    ## 
    ## data:  data$Dose13 and data$Dose0
    ## V = 45, p-value = 0.003906
    ## alternative hypothesis: true location shift is not equal to 0
    ## 95 percent confidence interval:
    ##   4.500 21.575
    ## sample estimates:
    ## (pseudo)median 
    ##            9.2

V – This is the Wilcoxon signed-rank test statistic. It is the ratio of
the mean difference to the standard error. p-value – This is the p-value
associated with the Wilcoxon signed-rank test.

That is to say if the P value \< 0.05 (assuming alpha=0.05) then there
is a statistically significant difference in endurance performance
between the 0 and 13 mg caffeine dosage level. For our example, we have
a p-value = 0.003906. Thus, we reject the null hypothesis that the
endurance performance rank differences are symmetrically distributed
around zero and we conclude that a difference between caffeine dosage
levels exist. Practically speaking, you conclude that there are
differences in median endurance performance between caffeine dosage
levels.

By default, this function performs a two-sided Wilcoxon Signed-Rank Test
but you can specify a left-tailed test or right-tailed test by using the
“alternative” argument.

### *Wilcoxon Signed-Rank Test Interpretation and Conclusions*

A p-value = .003906 indicates that, we should reject the null hypothesis
that the endurance performance rank differences are symmetrically
distributed around zero and we conclude that a difference between
caffeine dosage levels exist. Practically speaking, you conclude that
there are differences in median endurance performance between caffeine
dosage levels. Descriptive statistics report that cyclists with no
caffeine dosage reported a median endurance performance of 45.2 while
cyclists with a 13 mg caffeine dosage of 59.3. Thus, cyclist endurance
performance improved when under the effects of caffeine.

### *What to do When Assumptions are Broken or Things Go Wrong*

When the differences between repeated measurements are normally
distributed and do not contain outliers, a paired t-test is preferable
to the Wilcoxon signed rank test since a paired t-test is considered
more statistically powerful.

Additional options include considering permutation/randomization tests,
bootstrap confidence intervals, and transforming the data but each
option will have its own stipulations.

If you need to compare more than two dependent groups, a single factor
repeated measures analysis of variances (ANOVA) or nonparametric
Friedman test would be appropriate.

Furthermore, if you have one between-subject factor and one-within
subject factor to consider simultaneously, then a repeated measure mixed
model ANOVA would be appropriate.

Missing values can severely impact a Wilcoxon signed-rank test because
the entire row of data will generally be excluded. If you have a lot of
missing data, one alternative would be to perform a single factor
repeated measures mixed model ANOVA. This would allow for the
computation of estimated marginal means to compensate for the uneven
replication between groups.

A Wilcoxon signed-rank test is not appropriate if each experimental unit
(subject) only receives one of two available treatments. For example, if
you would like to see if first-year students scored differently on an
exam when compared to second-year students, then each subject only has
one of two potential factor levels. If this is the case, then an
independent samples t-test would be a more appropriate course of action.

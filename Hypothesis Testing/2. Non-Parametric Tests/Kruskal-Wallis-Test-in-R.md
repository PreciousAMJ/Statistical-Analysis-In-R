Kruskal-Wallis Test in R
================

### *Introduction*

A Kruskal-Wallis test is typically performed when an analyst would like
to test for differences between *three or more* groups or conditions.
However, unlike a one-way ANOVA, the response variable of interest is
*not normally distributed*. For example, you may want to know if
first-years students scored differently on an exam when compared to
second-year or third-year students, but the exam scores for *at least
one group are do not follow a normal distribution*. The Kruskal-Wallis
test is often considered a nonparametric alternative to a one-way ANOVA.

A Kruskal-Wallis test is typically performed when each experimental
unit, (study subject) is only assigned one of the available group
conditions. Thus, the group groups do not have overlapping membership
and are considered independent. A Kruskal-Wallis test is considered a
“between-subjects” analysis.

Formally, the null hypothesis is that the population distribution
functions are equal for all groups. The alternative hypothesis is that
*at least one of the distributions function is not equal*.

Informally, we are testing to see if mean ranks differ between groups.
Since \* mean ranks approximate the median under similar
distributions\*, many time analysts will indicate that we are testing
for median differences even though this may not be considered formally
correct. For this reason, many times descriptive statistics regarding
median values are provided when the Kruskal-Wallis test is performed.

### *Kruskal-Wallis Test Assumptions*

The following assumptions must be met in order to run a Mann-Whitney U
test:

1.  group groups are independent of one another. Experimental units only
    receive one group and they do not overlap.
2.  The response variable of interest is ordinal or continuous.
3.  Both samples are random

``` r
library("ggplot2")
library("qqplotr")
```

    ## 
    ## Attaching package: 'qqplotr'

    ## The following objects are masked from 'package:ggplot2':
    ## 
    ##     stat_qq_line, StatQqLine

``` r
library("dplyr")
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
library("DescTools")
library("FSA")
```

    ## ## FSA v0.9.4. See citation('FSA') if used in publication.
    ## ## Run fishR() for related website and fishR('IFAR') for related book.

``` r
library("PMCMRplus")
library("readr")
```

``` r
setwd("C:\\Users\\HP\\Documents\\Datasets")

Diabetes_Data <- read_csv("C:\\Users\\HP\\Documents\\Datasets\\Diabetes Dataset.csv")
```

    ## New names:
    ## Rows: 403 Columns: 24
    ## ── Column specification
    ## ──────────────────────────────────────────────────────── Delimiter: "," chr
    ## (4): location, gender, frame, dm dbl (20): ...1, id, chol, stab.glu, hdl,
    ## ratio, glyhb, age, height, weight, ...
    ## ℹ Use `spec()` to retrieve the full column specification for this data. ℹ
    ## Specify the column types or set `show_col_types = FALSE` to quiet this message.
    ## • `` -> `...1`

``` r
# convert the cholesterol value into three categories
Cholesterol_level <- as.factor(ifelse(Diabetes_Data$chol <= 170, "normal", ifelse(Diabetes_Data$chol > 170 & Diabetes_Data$chol < 240, "borderline", "high")))

Data <- na.omit(data.frame(Diabetes_Data[, "stab.glu"], Cholesterol_level))
```

### *Descriptive Statistics*

Many times, analysts forget to take a good look at their data prior to
performing statistical tests. Descriptive statistics are not only used
to describe the data but also help determine if any inconsistencies are
present. Detailed investigation of descriptive statistics can help
answer the following questions (in addition to many others):

How much missing data do I have? Do I have potential outliers? Are my
standard deviation and standard error values large relative to the mean?
In what range most of my data fall for each group?

Produce descriptive statistics by group

``` r
Data %>% select(stab.glu, Cholesterol_level) %>% group_by(Cholesterol_level) %>% 
  summarise(n=n(), 
            mean=mean(stab.glu, na.rm = TRUE), 
            sd=sd(stab.glu, na.rm = TRUE),
            stderr=sd/sqrt(n),
            LCL = mean - qt(1 - (0.05 / 2), n - 1) * stderr,
            UCL = mean + qt(1 - (0.05 / 2), n - 1) * stderr,
            median=median(stab.glu, na.rm = TRUE),
            min=min(stab.glu, na.rm = TRUE), 
            max=max(stab.glu, na.rm = TRUE),
            IQR=IQR(stab.glu, na.rm = TRUE),
            LCLmed = MedianCI(stab.glu, na.rm=TRUE)[2],
            UCLmed = MedianCI(stab.glu, na.rm=TRUE)[3])
```

    ## # A tibble: 3 × 13
    ##   Cholest…¹     n  mean    sd stderr   LCL   UCL median   min   max   IQR LCLmed
    ##   <fct>     <int> <dbl> <dbl>  <dbl> <dbl> <dbl>  <dbl> <dbl> <dbl> <dbl>  <dbl>
    ## 1 borderli…   251  102.  43.5   2.74  96.8  108.   88      48   341  26       85
    ## 2 high         78  124.  71.6   8.11 108.   140.   92.5    52   369  35.5     89
    ## 3 normal       73  104.  57.3   6.70  90.8  117.   88      58   385  20       83
    ## # … with 1 more variable: UCLmed <dbl>, and abbreviated variable name
    ## #   ¹​Cholesterol_level
    ## # ℹ Use `colnames()` to see all variable names

Cholesterol_level – The levels corresponding to our independent variable
‘stab.glu’. n – The number of observations for each group. mean – The
mean value for each group. sd – The standard deviation of each group.
stderr – The standard error of each group. That is the standard
deviation / sqrt (n). LCL, UCL – The upper and lower confidence
intervals of the mean. That is to say, you can be 95% certain that the
true mean falls between the lower and upper values specified for each
group group assuming a normal distribution. median – The median value
for each group. min, max – The minimum and maximum value for each group.
IQR – The inner quartile range of each group. That is the 75th
percentile – 25th percentile. LCLmed, UCLmed – The 95% confidence
interval for the median.

### *Boxplots*

Side-by-side boxplots are provided by ggplot2, which is useful in
determining the shape of the distribution, outliers etc.

Produce Boxplots and visually check for outliers

``` r
ggplot(Data, aes(x =Cholesterol_level , y =stab.glu, fill = stab.glu)) +
  stat_boxplot(geom ="errorbar", width = 0.5) +
  geom_boxplot(fill = "light blue") + 
  stat_summary(fun=mean, geom="point", shape=10, size=3.5, color="black") + 
  ggtitle("Boxplot of Stab.glu for each Cholesterol_level") + 
  theme_bw() + theme(legend.position="none")
```

![](Kruskal-Wallis-Test-in-R_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->
The ouput shows that the distribution is skewed to the left with lot of
ouliers.It also shows that the mean(dot with cross) and the median(the
thick black line) are at two separate points for the three categories,
suggesting that the distributions are not normal.

### *Normality Tests*

Prior to performing the Kruskal-Wallis test, it is important to evaluate
model assumptions to ensure that we are performing an appropriate and
reliable comparison. If normality is present, a one-way ANOVA would be a
more appropriate test.

Testing normality should be performed using a Shapiro-Wilk normality
test (or equivalent), and/or a QQ plots for large sample sizes. Many
times, histograms can also be helpful.

In this example, we will use the shapiro.test function from the stats
package to test for normality. We’ll also use the groupby function from
the dplyr package, so that we can produce separate analysis for each
cholesterol group.

Test each group for normality

``` r
Data %>%
  group_by(Cholesterol_level) %>%
  summarise(W = shapiro.test(stab.glu)$statistic,
            p.value = shapiro.test(stab.glu)$p.value)
```

    ## # A tibble: 3 × 3
    ##   Cholesterol_level     W  p.value
    ##   <fct>             <dbl>    <dbl>
    ## 1 borderline        0.689 3.01e-21
    ## 2 high              0.706 3.32e-11
    ## 3 normal            0.539 8.61e-14

spray – This column identifies the levels of the treatment variable
along with the mean differences between the levels. W Stat – The
Shapiro-Wilk (W) test statistics for each test is provided for each
group. p-value – The p-value for each test is provided. A p-value \<
0.05 would indicate that we should reject the assumption of normality.
The Shapiro-Wilk Test p-values for all the tests are \< 0.05, which
means we should reject the null hypothesis that the distribution is
normal for all the groups.

### *Kruskal-Wallis Test*

So far, we have determined that the data for each Cholesterol group is
not normally distributed, and we have major influential outliers. As a
result, a Kruskal-Wallis test would be more appropriate than a one-way
ANOVA to test for significant differences between treatment groups. Our
next step is to officially perform a Kruskal-Wallis test to determine
the difference between the cholesterol groups. The “kruskal.test”
function performs this test in R.

Perform the Kruskal-Wallis test

``` r
Model <- kruskal.test(stab.glu ~ Cholesterol_level, data=Data)

print(Model)
```

    ## 
    ##  Kruskal-Wallis rank sum test
    ## 
    ## data:  stab.glu by Cholesterol_level
    ## Kruskal-Wallis chi-squared = 7.4094, df = 2, p-value = 0.02461

chi-squared – This value corresponds to the Kruskal-Wallis chi-square
test statistic. The chi-square statistic is compared to the appropriate
chi-square critical value as denoted by the degrees of freedom.

df – The degrees of freedom associated with the test. This will equal
the number of treatment groups – 1.

p-value – The p-value corresponding to the two-sided test based on the
chi-square distribution.

The p-value for our test is 0.02461, given our alpha =0.05, we would
reject our null hypothesis and conclude that there is a statistically
significant difference in the amoount of stable glucose for each
cholesterol group.

### *Information About Post-hoc Analyses for the Kruskal-Wallis Test*

From our example, a Kruskal-Wallis test p-value = 0.02461 indicates that
there is a significant difference in the mean ranks of the cholesterol
groups. However, it does not provide an indication of which groups are
different without also performing post-hoc tests.

In R, the FSA package can perform Dunn’s multiple comparison procedure
to identify which pairs of groups significantly differ. Alternatively,
the PMCMRplus package can perform the Dwass, Steel, Critchlow-Fligner
multiple comparisons procedure. Although either post-hoc procedure is
viable, Dunn’s is likely somewhat more popular.

``` r
library("FSA")

library("PMCMRplus")
```

Dunn’s Kruskal-Wallis post-hoc test

``` r
posthocs1 <- dunnTest(stab.glu ~ Cholesterol_level, data=Data, method="holm")

print(posthocs1)
```

    ## Dunn (1964) Kruskal-Wallis multiple comparison

    ##   p-values adjusted with the Holm method.

    ##            Comparison          Z    P.unadj      P.adj
    ## 1   borderline - high -2.4854900 0.01293732 0.03881197
    ## 2 borderline - normal  0.5053445 0.61331687 0.61331687
    ## 3       high - normal  2.3911958 0.01679360 0.03358719

Comparison – This column identifies the paired comparison that is being
performed. Z – The z-score corresponding to the paired comparison.

P.unadj – The unadjusted p-value corresponding to the paired comparison.

P.adj – The adjusted p-value using Holm’s method corresponding to the
paired comparison.

Dwass, Steel, Critchlow, Fligner post-hoc test

``` r
posthocs2<-dscfAllPairsTest(stab.glu ~ Cholesterol_level, data=Data)

print(posthocs2)
```

    ## 
    ##  Pairwise comparisons using Dwass-Steele-Critchlow-Fligner all-pairs test

    ## data: stab.glu by Cholesterol_level

    ##        borderline high 
    ## high   0.033      -    
    ## normal 0.862      0.049

    ## 
    ## P value adjustment method: single-step

### *Interpretation*

From our example, a Kruskal-Wallis test p-value = 0.02461 indicates that
there is a significant difference in the mean ranks of the cholesterol
groups.

The post-hoc tests show that ther’s a significant difference betwen
cholesterol groups borderline and high and also for the cholesterol
groups high and normal.

### *What to do When Assumptions are Broken or Things Go Wrong*

The Kruskal-Wallis test is typically used as a last resort. This is
because it is a slightly lower powered test when compared to a one-way
ANOVA.

More modern alternatives to the Kruskal-Wallis test include
permutation/randomization tests, bootstrap confidence intervals, and
transforming the data to achieve normality. However, each option will
have its own stipulations.

A Kruskal-Wallis test is not appropriate if you have repeated
measurements taken on the same experimental unit (subject). For example,
if you have a pre-test, post-test, and follow-up study then each subject
would be measured at three different time points. If this is the case,
then a single factor repeated measures ANOVA or nonparametric Friedman
test may be a more appropriate course of action.

Repeated Measures ANOVA in R
================

A *repeated measures* ANOVA is used to determine whether or not there is
a statistically significant difference between the means of *three or
more* groups in which the same subjects show up in each group. i.e the
groups are dependent on each other.

One-Way ANOVA vs. Repeated Measures ANOVA In a typical one-way ANOVA,
different subjects are used in each group. For example, we might ask
subjects to rate three movies, just like in the example above, but we
use different subjects to rate each movie. i.e the groups are independet
of each other.

Researchers want to know if four different drugs lead to different
reaction times. To test this, they measure the reaction time of five
patients on the four different drugs. Since each patient is measured on
each of the four drugs, we will use a repeated measures ANOVA to
determine if the mean reaction time differs between drugs.

Use the following steps to perform the repeated measures ANOVA in R.

### *Step 1: Enter the data*

First, we’ll create a data frame to hold our data:

``` r
#create data
Data <- data.frame(patient=rep(1:5, each=4),
                 drug=rep(1:4, times=5),
                 response=c(30, 28, 16, 34,
                            14, 18, 10, 22,
                            24, 20, 18, 30,
                            38, 34, 20, 44,
                            26, 28, 14, 30))

#view data
Data
```

    ##    patient drug response
    ## 1        1    1       30
    ## 2        1    2       28
    ## 3        1    3       16
    ## 4        1    4       34
    ## 5        2    1       14
    ## 6        2    2       18
    ## 7        2    3       10
    ## 8        2    4       22
    ## 9        3    1       24
    ## 10       3    2       20
    ## 11       3    3       18
    ## 12       3    4       30
    ## 13       4    1       38
    ## 14       4    2       34
    ## 15       4    3       20
    ## 16       4    4       44
    ## 17       5    1       26
    ## 18       5    2       28
    ## 19       5    3       14
    ## 20       5    4       30

### *Step 2: Perform the repeated measures ANOVA*

Next, we will perform the repeated measures ANOVA using the aov()
function:

``` r
#fit repeated measures ANOVA model
model <- aov(response~factor(drug)+Error(factor(patient)), data = Data)

#view model summary
summary(model)
```

    ## 
    ## Error: factor(patient)
    ##           Df Sum Sq Mean Sq F value Pr(>F)
    ## Residuals  4  680.8   170.2               
    ## 
    ## Error: Within
    ##              Df Sum Sq Mean Sq F value   Pr(>F)    
    ## factor(drug)  3  698.2   232.7   24.76 1.99e-05 ***
    ## Residuals    12  112.8     9.4                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

### *Step 3: Interpret the results*

A repeated measures ANOVA uses the following null and alternative
hypotheses:

The null hypothesis (H0): µ1 = µ2 = µ3 (the population means are all
equal)

The alternative hypothesis: (Ha): at least one population mean is
different from the rest

In this example, the F test-statistic is 24.76 and the corresponding
p-value is 1.99e-05. Since this p-value is less than 0.05, we reject the
null hypothesis and conclude that there is a statistically significant
difference in mean response times between the four drugs.

### *Step 4: Report the results*

Lastly, we will report the results of our repeated measures ANOVA.

Here is an example of how to do so:

A one-way repeated measures ANOVA was conducted on five individuals to
examine the effect that four different drugs had on response time.

Results showed that the type of drug used lead to statistically
significant differences in response time (F(3, 12) = 24.76, p \< 0.001).

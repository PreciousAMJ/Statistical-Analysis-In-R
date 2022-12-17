Simple cox proportional hazards model
================

We’ve worked through the steps involved in the Kaplan-Meier method.
You’ll now learn about the more flexible Cox proportional hazards model.
With this method you will be able to compare the survival of multiple
groups of patients at the same time.

Like the name suggests, the model is formulated around the concept of
hazards. You’ve already seen examples of the hazard function. The hazard
function h(t) is the probability of the event happening at time t, given
that it has not yet happened. In other words, h(t) is the probability of
dying at time t having survived up to time t.

An important concept involved in the calculation of the hazard is the
risk set. Just like the risk of dying (or experiencing some specific
event) changes over time, so the number of patients that are subjected
to that risk change over time as people die or drop out. The risk set at
time t is defined as the set of patients at time t that are at risk of
experiencing the event.

Usually in survival analysis, we are interested in the difference
between survival curves of different groups of patients. Earlier you saw
the log-rank test, which gives a p value for comparing the survival
curves between different groups of patients with a Kaplan-Meier plot.
The p value tells you nothing about the size of the difference between
the survival curves, however. This is done by dividing one hazard by
another to give a hazard ratio. For example, dividing the hazard for
females by the hazard for males gives you a hazard ratio for females
compared with males. It tells you how much more likely female patients
will die than male patients.

I’ll now introduce you to the Cox model and explain how its hazard and
hazard ratios work.

To load our dataset

``` r
Heart_failure_dataset <- read.csv("C:\\Users\\HP\\Documents\\Datasets\\Heart Failure Dataset.csv")

head(Heart_failure_dataset)
```

    ##   id death los age gender cancer cabg crt defib dementia diabetes hypertension
    ## 1  1     0   2  90      2      0    0   0     0        0        0            0
    ## 2  2     0  10  74      1      0    0   0     0        0        0            1
    ## 3  3     0   3  83      2      0    0   0     0        0        0            1
    ## 4  4     0   1  79      1      0    0   0     0        0        1            1
    ## 5  5     0  17  94      2      0    0   0     0        0        1            1
    ## 6  6     0  47  89      1      0    0   0     0        0        0            0
    ##   ihd mental_health arrhythmias copd obesity pvd renal_disease valvular_disease
    ## 1   0             0           1    0       0   0             0                1
    ## 2   1             0           0    0       0   0             1                1
    ## 3   0             0           1    0       0   0             0                0
    ## 4   1             0           0    1       0   0             0                0
    ## 5   0             0           0    0       0   0             0                0
    ## 6   0             0           0    0       0   1             0                1
    ##   metastatic_cancer pacemaker pneumonia prior_appts_attended prior_dnas pci
    ## 1                 0         0         0                    4          0   0
    ## 2                 0         0         1                    9          1   0
    ## 3                 0         0         0                    1          0   0
    ## 4                 0         1         0                    9          2   1
    ## 5                 0         0         0                    3          0   0
    ## 6                 0         0         1                    3          0   0
    ##   stroke senile quintile ethnicgroup fu_time
    ## 1      0      0        2          NA     416
    ## 2      0      0        4           1     648
    ## 3      0      0        3           1     466
    ## 4      1      0        5           1     441
    ## 5      0      0        2           1     371
    ## 6      0      0        3          NA      47

the libraries we’ll be using.

``` r
# install.packages("survminer")

library("survminer")
```

    ## Warning: package 'survminer' was built under R version 4.2.2

    ## Loading required package: ggplot2

    ## Warning: package 'ggplot2' was built under R version 4.2.1

    ## Loading required package: ggpubr

    ## Warning: package 'ggpubr' was built under R version 4.2.2

``` r
library("survival")
```

    ## Warning: package 'survival' was built under R version 4.2.2

    ## 
    ## Attaching package: 'survival'

    ## The following object is masked from 'package:survminer':
    ## 
    ##     myeloma

The variables we’ll be using

``` r
Follow_up_time <- (Heart_failure_dataset$fu_time) # a continuous variable (numeric)

Death <- (Heart_failure_dataset$death) # binary variable (numeric)

Ethnicgroup <- (Heart_failure_dataset$ethnicgroup)

Age <- (Heart_failure_dataset$age)
```

Let’s see the survival rate for age.

``` r
Survival_Object <- Surv(Follow_up_time, Death) # To create a survival object

cox <- coxph(Survival_Object ~ Age)

summary(cox)
```

    ## Call:
    ## coxph(formula = Survival_Object ~ Age)
    ## 
    ##   n= 1000, number of events= 492 
    ## 
    ##         coef exp(coef) se(coef)     z Pr(>|z|)    
    ## Age 0.056005  1.057602 0.005193 10.78   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ##     exp(coef) exp(-coef) lower .95 upper .95
    ## Age     1.058     0.9455     1.047     1.068
    ## 
    ## Concordance= 0.651  (se = 0.013 )
    ## Likelihood ratio test= 138  on 1 df,   p=<2e-16
    ## Wald test            = 116.3  on 1 df,   p=<2e-16
    ## Score (logrank) test = 115.7  on 1 df,   p=<2e-16

Note : coef = log(hazard ratio) exp(coef) = gives us the hazard ratio
itself

The output gives an hazard ratio of 1.058 ,which means a one year
increase in age will lead to a 1.058 times “risk”/chance of the hazard.
i.e a one year increase in age will lead to a 5.8%(1.058-1) *higher*
risk of the hazard.

Let’s see the survival rates between the ethnic groups.

``` r
Survival_Object <- Surv(Follow_up_time, Death)

cox <- coxph(Survival_Object ~ Ethnicgroup)


summary(cox)
```

    ## Call:
    ## coxph(formula = Survival_Object ~ Ethnicgroup)
    ## 
    ##   n= 957, number of events= 471 
    ##    (43 observations deleted due to missingness)
    ## 
    ##                 coef exp(coef) se(coef)      z Pr(>|z|)
    ## Ethnicgroup -0.04555   0.95547  0.05069 -0.899    0.369
    ## 
    ##             exp(coef) exp(-coef) lower .95 upper .95
    ## Ethnicgroup    0.9555      1.047    0.8651     1.055
    ## 
    ## Concordance= 0.514  (se = 0.006 )
    ## Likelihood ratio test= 0.89  on 1 df,   p=0.3
    ## Wald test            = 0.81  on 1 df,   p=0.4
    ## Score (logrank) test = 0.81  on 1 df,   p=0.4

you’ll get just one coefficient for ethnicgroup. This is because unless
you tell it otherwise, R will assume that all your variables are
continuous. Ethnicity is very obviously not a continuous variable, but R
doesn’t know that unless you tell it!

Try this instead:

``` r
Ethnicgroup <- as.factor(Heart_failure_dataset$ethnicgroup) # to convert it into a categorical variable

levels(Ethnicgroup)
```

    ## [1] "1" "2" "3" "9"

``` r
cox <- coxph(Survival_Object ~ Ethnicgroup)

summary(cox)
```

    ## Call:
    ## coxph(formula = Survival_Object ~ Ethnicgroup)
    ## 
    ##   n= 957, number of events= 471 
    ##    (43 observations deleted due to missingness)
    ## 
    ##                  coef exp(coef) se(coef)      z Pr(>|z|)   
    ## Ethnicgroup2 -0.06428   0.93774  0.32000 -0.201  0.84078   
    ## Ethnicgroup3 -1.19586   0.30244  0.41108 -2.909  0.00362 **
    ## Ethnicgroup9  0.07394   1.07674  0.35706  0.207  0.83596   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ##              exp(coef) exp(-coef) lower .95 upper .95
    ## Ethnicgroup2    0.9377     1.0664    0.5008    1.7558
    ## Ethnicgroup3    0.3024     3.3064    0.1351    0.6769
    ## Ethnicgroup9    1.0767     0.9287    0.5348    2.1679
    ## 
    ## Concordance= 0.516  (se = 0.006 )
    ## Likelihood ratio test= 12.99  on 3 df,   p=0.005
    ## Wald test            = 8.55  on 3 df,   p=0.04
    ## Score (logrank) test = 9.61  on 3 df,   p=0.02

Note that ethnicgroup1 is not included in the output, this is because it
is used as the reference group. i.e each result is in reference to
ethnic group1. For example, to interpret the result for ethnicgroup2,
we’ll say we’ll say ethnicgroup2 has 0.9377 risk/chance of having the
hazard ratio compared to ethnicgroup1. Or ethnicgroup2 has
0.0623%(1-0.9377) lowered risk of having the hazard compared to
ethnicgroup1.

Let’s return to those with missing ethnic group. What else could you do
with them? An easy option is simply to make an “unknown” category for
them, which I’ll give the value 8 (you could pick anything not already
in use).

This is how to do this in R. As ethnic group is a categorical variable,
you first need to make an extra category. Then I’ll replace the “NA”s
with “8”:

``` r
levels(Ethnicgroup) <- c(levels(Ethnicgroup), "8") # add category '8' to the ethnic group

Ethnicgroup[is.na(Ethnicgroup)] <- "8" # change NA to "8"
```

Now return the Cox model:

``` r
cox <- coxph(Surv(Follow_up_time, Death) ~ Ethnicgroup, data = Heart_failure_dataset) # take variables straight from g


summary(cox)
```

    ## Call:
    ## coxph(formula = Surv(Follow_up_time, Death) ~ Ethnicgroup, data = Heart_failure_dataset)
    ## 
    ##   n= 1000, number of events= 492 
    ## 
    ##                  coef exp(coef) se(coef)      z Pr(>|z|)   
    ## Ethnicgroup2 -0.06573   0.93638  0.31999 -0.205  0.83725   
    ## Ethnicgroup3 -1.19368   0.30310  0.41107 -2.904  0.00369 **
    ## Ethnicgroup9  0.08160   1.08502  0.35706  0.229  0.81923   
    ## Ethnicgroup8 -0.02353   0.97675  0.22363 -0.105  0.91621   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ##              exp(coef) exp(-coef) lower .95 upper .95
    ## Ethnicgroup2    0.9364     1.0679    0.5001    1.7532
    ## Ethnicgroup3    0.3031     3.2992    0.1354    0.6784
    ## Ethnicgroup9    1.0850     0.9216    0.5389    2.1846
    ## Ethnicgroup8    0.9767     1.0238    0.6301    1.5140
    ## 
    ## Concordance= 0.518  (se = 0.008 )
    ## Likelihood ratio test= 12.95  on 4 df,   p=0.01
    ## Wald test            = 8.53  on 4 df,   p=0.07
    ## Score (logrank) test = 9.58  on 4 df,   p=0.05

A few things have changed, but others haven’t. First, no missing values
were excluded. Second, there are lines of output for ethnic group 8.
People with unknown ethnicity have a very similar hazard (0.98, CI 0.63
to 1.51, p=0.916) to white people. All the results for the other three
categories are very close to what they were before, though not exactly
the same if you look at the third or even fourth decimal place.

With just one categorical predictor in the model, it doesn’t matter
whether you leave out or recode and include missing values of that
predictor. When we come to include multiple predictors later in the
course, however, it’s very possible that your choice could affect the
results. It’s a good idea to try it both ways and check.

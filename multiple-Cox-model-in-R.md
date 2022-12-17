multiple Cox model in R
================

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

``` r
# install.packages("survival")

library("survival")
```

    ## Warning: package 'survival' was built under R version 4.2.2

We’ll find the survival rate accounting for different variables in our
dataset.

``` r
cox <-coxph(Surv(Heart_failure_dataset$fu_time, Heart_failure_dataset$death) ~ Heart_failure_dataset$age + as.factor(Heart_failure_dataset$gender) + as.factor(Heart_failure_dataset$copd) + Heart_failure_dataset$prior_dnas + as.factor(Heart_failure_dataset$ethnicgroup))

summary(cox)
```

    ## Call:
    ## coxph(formula = Surv(Heart_failure_dataset$fu_time, Heart_failure_dataset$death) ~ 
    ##     Heart_failure_dataset$age + as.factor(Heart_failure_dataset$gender) + 
    ##         as.factor(Heart_failure_dataset$copd) + Heart_failure_dataset$prior_dnas + 
    ##         as.factor(Heart_failure_dataset$ethnicgroup))
    ## 
    ##   n= 957, number of events= 471 
    ##    (43 observations deleted due to missingness)
    ## 
    ##                                                    coef exp(coef)  se(coef)
    ## Heart_failure_dataset$age                      0.064765  1.066909  0.005719
    ## as.factor(Heart_failure_dataset$gender)2      -0.277178  0.757920  0.096227
    ## as.factor(Heart_failure_dataset$copd)1         0.085753  1.089537  0.106380
    ## Heart_failure_dataset$prior_dnas               0.166117  1.180712  0.040028
    ## as.factor(Heart_failure_dataset$ethnicgroup)2 -0.300372  0.740543  0.354585
    ## as.factor(Heart_failure_dataset$ethnicgroup)3 -0.794072  0.452000  0.414508
    ## as.factor(Heart_failure_dataset$ethnicgroup)9  0.410506  1.507581  0.360902
    ##                                                    z Pr(>|z|)    
    ## Heart_failure_dataset$age                     11.324  < 2e-16 ***
    ## as.factor(Heart_failure_dataset$gender)2      -2.880  0.00397 ** 
    ## as.factor(Heart_failure_dataset$copd)1         0.806  0.42019    
    ## Heart_failure_dataset$prior_dnas               4.150 3.32e-05 ***
    ## as.factor(Heart_failure_dataset$ethnicgroup)2 -0.847  0.39693    
    ## as.factor(Heart_failure_dataset$ethnicgroup)3 -1.916  0.05540 .  
    ## as.factor(Heart_failure_dataset$ethnicgroup)9  1.137  0.25535    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ##                                               exp(coef) exp(-coef) lower .95
    ## Heart_failure_dataset$age                        1.0669     0.9373    1.0550
    ## as.factor(Heart_failure_dataset$gender)2         0.7579     1.3194    0.6276
    ## as.factor(Heart_failure_dataset$copd)1           1.0895     0.9178    0.8845
    ## Heart_failure_dataset$prior_dnas                 1.1807     0.8469    1.0916
    ## as.factor(Heart_failure_dataset$ethnicgroup)2    0.7405     1.3504    0.3696
    ## as.factor(Heart_failure_dataset$ethnicgroup)3    0.4520     2.2124    0.2006
    ## as.factor(Heart_failure_dataset$ethnicgroup)9    1.5076     0.6633    0.7432
    ##                                               upper .95
    ## Heart_failure_dataset$age                        1.0789
    ## as.factor(Heart_failure_dataset$gender)2         0.9152
    ## as.factor(Heart_failure_dataset$copd)1           1.3421
    ## Heart_failure_dataset$prior_dnas                 1.2771
    ## as.factor(Heart_failure_dataset$ethnicgroup)2    1.4838
    ## as.factor(Heart_failure_dataset$ethnicgroup)3    1.0185
    ## as.factor(Heart_failure_dataset$ethnicgroup)9    3.0583
    ## 
    ## Concordance= 0.671  (se = 0.013 )
    ## Likelihood ratio test= 170.9  on 7 df,   p=<2e-16
    ## Wald test            = 143.6  on 7 df,   p=<2e-16
    ## Score (logrank) test = 141.5  on 7 df,   p=<2e-16

Notice that “gender1” and “ethnicgroup1” do not appear in the above
output. This is because they are the reference categories for their
respective variables. By default, R sets the reference category as the
lowest value for the variable, which is 1. This happens to correspond to
males and to white ethnicity, but you can easily change the reference
category So to interpret for gender for example; having
accounted(adjusted) for other vaiables, the gender2(female) have 0.76
times “risk”/chance of hazard compared to gender1(male). i.e gender2
have 24%(100-76) *lower* “risk” of the hazard compared to males. i.e
female/male = 0.76 , i.e males have more risk of the hazrad than
females. if 1 male has the hazard, 0.76 females has it.

for Age, having accounted(adjusted) for other variables, a one year
increase in age will lead to a 1.07 times “risk”/chance of the hazard.
i.e a one year increase in age will lead to a 7%(107-100) *higher* risk
of the hazard.

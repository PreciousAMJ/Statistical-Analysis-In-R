Backward Elimination
================

``` r
Diabetes_Dataset <- read.csv("C:\\Users\\HP\\Documents\\Datasets\\Diabetes Dataset.csv")

View(Diabetes_Dataset)
```

``` r
dm <- as.factor(Diabetes_Dataset$dm)

Insurance <- as.factor(Diabetes_Dataset$insurance)

FH <- as.factor(Diabetes_Dataset$fh)

Smoking <- as.factor(Diabetes_Dataset$smoking)

Cholesterol <- Diabetes_Dataset$chol

HDL <- Diabetes_Dataset$hdl

Ratio <- Diabetes_Dataset$ratio

Location <- as.factor(Diabetes_Dataset$location)

Age <- Diabetes_Dataset$age

Gender <- as.factor(Diabetes_Dataset$gender)

Frame <- as.factor(Diabetes_Dataset$frame)

Systolic_BP <- Diabetes_Dataset$bp.1s

Diastolic_BP <- Diabetes_Dataset$bp.1d

BMI <- Diabetes_Dataset$weight * 0.453592 /  (Diabetes_Dataset$height * 0.0254)^2
```

In Backward elimination, we incllude all the variables and begin to
remove those that are not significant.

``` r
model <- glm(dm ~ Age + BMI + Cholesterol + HDL + Systolic_BP + Diastolic_BP, family = binomial(link = logit))

summary(model)
```

    ## 
    ## Call:
    ## glm(formula = dm ~ Age + BMI + Cholesterol + HDL + Systolic_BP + 
    ##     Diastolic_BP, family = binomial(link = logit))
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -1.4658  -0.5453  -0.3625  -0.1989   2.8155  
    ## 
    ## Coefficients:
    ##                Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)  -7.4479066  1.5287185  -4.872 1.10e-06 ***
    ## Age           0.0505742  0.0121218   4.172 3.02e-05 ***
    ## BMI           0.0496011  0.0242735   2.043  0.04101 *  
    ## Cholesterol   0.0106330  0.0035028   3.036  0.00240 ** 
    ## HDL          -0.0290599  0.0103633  -2.804  0.00505 ** 
    ## Systolic_BP   0.0053365  0.0089493   0.596  0.55097    
    ## Diastolic_BP -0.0002951  0.0158481  -0.019  0.98515    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 324.38  on 378  degrees of freedom
    ## Residual deviance: 263.80  on 372  degrees of freedom
    ##   (24 observations deleted due to missingness)
    ## AIC: 277.8
    ## 
    ## Number of Fisher Scoring iterations: 5

It’s clear that neither of the BP variables is significantly associated
with the odds of being diagnosed with diabetes in this data set, but the
other four variables were. If you drop the BP variables, you get this:

``` r
model <- glm(dm ~ Age + BMI + Cholesterol + HDL, family = binomial(link = logit))

summary(model)
```

    ## 
    ## Call:
    ## glm(formula = dm ~ Age + BMI + Cholesterol + HDL, family = binomial(link = logit))
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -1.4243  -0.5554  -0.3585  -0.1969   2.8492  
    ## 
    ## Coefficients:
    ##              Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept) -7.112566   1.279586  -5.558 2.72e-08 ***
    ## Age          0.054510   0.010577   5.153 2.56e-07 ***
    ## BMI          0.052218   0.024013   2.175  0.02966 *  
    ## Cholesterol  0.011010   0.003452   3.190  0.00142 ** 
    ## HDL         -0.028668   0.010310  -2.781  0.00543 ** 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 325.70  on 382  degrees of freedom
    ## Residual deviance: 265.11  on 378  degrees of freedom
    ##   (20 observations deleted due to missingness)
    ## AIC: 275.11
    ## 
    ## Number of Fisher Scoring iterations: 5

Have any of the coefficients for the four remaining variables changed?
Not much, which is good. But why is blood pressure not significant here
despite what the literature says? One way to find out is to see if it
correlates with other variables. Here’s the code to do that and the
output.

``` r
cor.test(Systolic_BP, HDL) # it uses pearson by default
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  Systolic_BP and HDL
    ## t = 0.39368, df = 395, p-value = 0.694
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  -0.07877132  0.11799603
    ## sample estimates:
    ##        cor 
    ## 0.01980412

``` r
cor.test(Systolic_BP, BMI)
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  Systolic_BP and BMI
    ## t = 2.2863, df = 391, p-value = 0.02277
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  0.01611718 0.21137656
    ## sample estimates:
    ##       cor 
    ## 0.1148561

``` r
cor.test(Systolic_BP, Cholesterol)
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  Systolic_BP and Cholesterol
    ## t = 4.1276, df = 395, p-value = 4.474e-05
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  0.1070653 0.2958454
    ## sample estimates:
    ##       cor 
    ## 0.2033444

``` r
cor.test(Systolic_BP, Age)
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  Systolic_BP and Age
    ## t = 9.8342, df = 396, p-value < 2.2e-16
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  0.3604404 0.5187477
    ## sample estimates:
    ##       cor 
    ## 0.4430412

So systolic BP correlates weakly (but statistically significantly) with
cholesterol and moderately (and also statistically significantly) with
age. Both of these results are entirely expected from what we know about
physiology.

As an exercise, you can try leaving age out of the model: is systolic BP
significant now?

``` r
model <- glm(dm ~ BMI + Cholesterol + HDL + Systolic_BP + Diastolic_BP, family = binomial(link = logit))

summary(model)
```

    ## 
    ## Call:
    ## glm(formula = dm ~ BMI + Cholesterol + HDL + Systolic_BP + Diastolic_BP, 
    ##     family = binomial(link = logit))
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -1.6686  -0.5932  -0.4237  -0.2890   2.4963  
    ## 
    ## Coefficients:
    ##               Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)  -5.961353   1.403370  -4.248 2.16e-05 ***
    ## BMI           0.038721   0.023238   1.666 0.095657 .  
    ## Cholesterol   0.012763   0.003459   3.690 0.000224 ***
    ## HDL          -0.026745   0.010498  -2.548 0.010848 *  
    ## Systolic_BP   0.024917   0.007649   3.258 0.001123 ** 
    ## Diastolic_BP -0.022280   0.014391  -1.548 0.121578    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 324.38  on 378  degrees of freedom
    ## Residual deviance: 282.72  on 373  degrees of freedom
    ##   (24 observations deleted due to missingness)
    ## AIC: 294.72
    ## 
    ## Number of Fisher Scoring iterations: 5

excluding Age from the model increases the significance of BP. This is
because since Age and BP are correlated, Age already accounted for some
of the variability of BP.

So it,s a good practice to check the how the variables in the dataset
are corellated before deciding which to include in the model.

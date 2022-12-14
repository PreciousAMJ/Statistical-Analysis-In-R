Multiple Logistic Regression
================

Let’s load the dataset.

``` r
Diabetes_Dataset <- read.csv("C:\\Users\\HP\\Documents\\Datasets\\Diabetes Dataset.csv")

View(Diabetes_Dataset)
```

Logistic regression involves a binary outcome variable. we’ll model the
odds of having diabetes based on Age, Gender and BMI

``` r
Age <- Diabetes_Dataset$age

Gender <- as.factor(Diabetes_Dataset$gender)

BMI <- Diabetes_Dataset$weight * 0.453592 /  (Diabetes_Dataset$height * 0.0254)^2

Outcome <- ifelse(Diabetes_Dataset$dm == "yes", 1, 0)
 # so R will recognise this variable(outome variable) as a factor (we want to perform logistic regression)
```

``` r
contrasts(Gender)
```

    ##        male
    ## female    0
    ## male      1

``` r
Model <- glm(Outcome ~ Age + Gender + BMI, family = binomial (link = logit))

summary(Model)
```

    ## 
    ## Call:
    ## glm(formula = Outcome ~ Age + Gender + BMI, family = binomial(link = logit))
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -1.6843  -0.5763  -0.3885  -0.2575   2.6991  
    ## 
    ## Coefficients:
    ##              Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept) -6.892669   1.027388  -6.709 1.96e-11 ***
    ## Age          0.055454   0.009884   5.611 2.02e-08 ***
    ## Gendermale   0.244852   0.322817   0.758  0.44816    
    ## BMI          0.073879   0.023310   3.169  0.00153 ** 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 326.02  on 383  degrees of freedom
    ## Residual deviance: 281.62  on 380  degrees of freedom
    ##   (19 observations deleted due to missingness)
    ## AIC: 289.62
    ## 
    ## Number of Fisher Scoring iterations: 5

Note that the result R gives is the *log odd*. To interpret this we need
to exponentiate the log odd, to get the odd.

For example, to find the odds of having diabetes for a 30 years old
female(note for female, gender = 0) with BMI of 22.50: logodd =
-6.8927 + 0.0555x30 + 0.2449x0 + 0.07388x22.50 logodd = -3.5654

to convert it to odd, we’ll expontiate it

``` r
exp(-3.45654)
```

    ## [1] 0.0315387

to convert it to probability

``` r
probability <- 0.0315387/1+0.0315387

probability
```

    ## [1] 0.0630774

The output gives a probability of 6%

------------------------------------------------------------------------

Multicollinearity in regression analysis occurs when two or more
predictor variables are highly correlated to each other, such that they
do not provide unique or independent information in the regression
model.

If the degree of correlation is high enough between variables, it can
cause problems when fitting and interpreting the regression model.

One of the main goals of regression analysis is to isolate the
relationship between each predictor variable and the response variable.

In particular, when we run a regression analysis, we interpret each
regression coefficient as the mean change in the response variable,
assuming all of the other predictor variables in the model are held
constant.

This means we assume that we’re able to change the values of a given
predictor variable without changing the values of the other predictor
variables.

However, when two or more predictor variables are highly correlated, it
becomes difficult to change one variable without changing another.

We’ll use vif to see if our predictor variables are correlated (to avoid
multicollinearity)

We can detect multicollinearity by using the variance inflation factor
(VIF), which measures the correlation and strength of correlation
between the predictor variables in a regression model.

A general rule of thumb for interpreting VIFs is as follows:

A value of 1 indicates there is no correlation between a given predictor
variable and any other predictor variables in the model. A value between
1 and 5 indicates moderate correlation between a given predictor
variable and other predictor variables in the model, but this is often
not severe enough to require attention. A value greater than 5 indicates
potentially severe correlation between a given predictor variable and
other predictor variables in the model. In this case, the coefficient
estimates and p-values in the regression output are likely unreliable.

``` r
library("car")
```

    ## Warning: package 'car' was built under R version 4.2.2

    ## Loading required package: carData

    ## Warning: package 'carData' was built under R version 4.2.2

``` r
vif(Model)
```

    ##      Age   Gender      BMI 
    ## 1.036631 1.104484 1.142266

The output shows that the predictor variables are not correlated, which
is good for our model.

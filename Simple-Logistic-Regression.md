Simple Logistic Regression
================

Note regression takes the form (y \~ x) where we assume The simplest
model we can fit is one with no predictors whatsoever in it. This is
called the *empty or null model* and is rarely useful. It has the
assumption that everyone has the same odds of having the outcome
(diabetes in our example). The R command to do this would be:

``` r
Diabetes_Dataset <- read.csv("C:/Users/HP/Documents/Datasets/Diabetes Dataset.csv")

Outcome <- ifelse(Diabetes_Dataset$dm == "yes", 1, 0)

View(Diabetes_Dataset)

m <- glm(Outcome ~ 1, family = binomial (link=logit))

summary(m)
```

    ## 
    ## Call:
    ## glm(formula = Outcome ~ 1, family = binomial(link = logit))
    ## 
    ## Deviance Residuals: 
    ##    Min      1Q  Median      3Q     Max  
    ## -0.578  -0.578  -0.578  -0.578   1.935  
    ## 
    ## Coefficients:
    ##             Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)  -1.7047     0.1403  -12.15   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 334.87  on 389  degrees of freedom
    ## Residual deviance: 334.87  on 389  degrees of freedom
    ##   (13 observations deleted due to missingness)
    ## AIC: 336.87
    ## 
    ## Number of Fisher Scoring iterations: 3

The “1” is just R’s way of saying that there’s only an intercept term in
the model. To get the output, though, you need the “summary” command as
well. To do this, you need to make an R object out of the model. I’ve
called this object “m”. We can then summarise it

Note that the intercept R gives is the *log odd* of the outcome. To
interpret this we need to exponentiate the log odd, to get the odd:

``` r
exp(-1.7047)
```

    ## [1] 0.1818269

if we prefer to get the probability rather than the odd, we can use the
relation between odds and probability. prob = odd/1+prob

``` r
Prob = 0.1818269/(1+0.1818269)

Prob
```

    ## [1] 0.1538524

This gives 0.1538524 (15%)

let’s compare this with the raw data:

``` r
table(Outcome)
```

    ## Outcome
    ##   0   1 
    ## 330  60

Using these numbers, the odds of having diabetes is 60/330 = 0.182 and
the probability is 60/(330+60) = 0.15, both exactly the same as from the
model, which is entirely as we had expected (and hoped!).

By the way, it’s worth checking how R has interpreted the binary
“Outcome”. If you run this

``` r
table(Outcome)
```

    ## Outcome
    ##   0   1 
    ## 330  60

``` r
table(m$y) # i.e axis y of m (our model)
```

    ## 
    ##   0   1 
    ## 330  60

…you see that there are 60 1s and 330 0s, which is good because there
were 60 yesses and 330 noes in the “Outcome” vector. It’s important to
know that R is modelling the log odds of Outcome=1 and not the log odds
of Outcome=0!

------------------------------------------------------------------------

Much more useful than the null model above is to see how the chance of
having diabetes depends on one or more predictors. The next simplest
model is one with one predictor. Let’s first look at gender, which in
our data set is binary: male or female.

We’ll predict the odds of having diabetes based on gender (note that the
output will be the log odd, which can be converted to odd by
exponentiating)

We need to let R know that Gender is a factor (in its language), that is
to say, a categorical variable (in our language). That was the point of
the “as.factor”

``` r
Gender <- as.factor(Diabetes_Dataset$gender) # we use as.factor so that R can trat this variable as a categorical variable.

m <- glm(Outcome ~ Gender, family = binomial (link = logit))

summary(m)
```

    ## 
    ## Call:
    ## glm(formula = Outcome ~ Gender, family = binomial(link = logit))
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -0.5915  -0.5915  -0.5683  -0.5683   1.9509  
    ## 
    ## Coefficients:
    ##             Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept) -1.74150    0.18592  -9.367   <2e-16 ***
    ## Gendermale   0.08694    0.28352   0.307    0.759    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 334.87  on 389  degrees of freedom
    ## Residual deviance: 334.78  on 388  degrees of freedom
    ##   (13 observations deleted due to missingness)
    ## AIC: 338.78
    ## 
    ## Number of Fisher Scoring iterations: 4

Note the slope is given as “Gendermale”. so what R is modelling is the
log(gender) = male. for male x is 1 and for female x is 0 This means we
are saying that the log odds of having diabetes differs by gender alone,
so the Rsquared value will be the variability of the Outcome accounted
for by Gender alone.

To include a continuous variable in the model instead, such as age (note
that we are treating age as continuous, just rounded down to the nearest
birthday), then the code is similar:

``` r
Age <- Diabetes_Dataset$age

m <- glm(Outcome ~ Age, family = binomial (link = logit))

summary(m)
```

    ## 
    ## Call:
    ## glm(formula = Outcome ~ Age, family = binomial(link = logit))
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -1.3612  -0.5963  -0.4199  -0.3056   2.4848  
    ## 
    ## Coefficients:
    ##              Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept) -4.404530   0.542828  -8.114 4.90e-16 ***
    ## Age          0.052465   0.009388   5.589 2.29e-08 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 334.87  on 389  degrees of freedom
    ## Residual deviance: 299.41  on 388  degrees of freedom
    ##   (13 observations deleted due to missingness)
    ## AIC: 303.41
    ## 
    ## Number of Fisher Scoring iterations: 5

R will fit age as a continous variable (by default). it’s not specified
as a factor/categorical

Let’s go on and look at the coefficients. This time there are two: the
intercept and one for age. Now, with a predictor (age in this case) in
the model, the intercept is no longer the overall crude log odds but is
instead the log odds of diabetes when age is zero. This follows from the
equation for the model:

Log odds of having diabetes= intercept + (coefficient for age) \* age in
years

= -4.4045 + 0.0525 \* age in years

So how do we interpret the coefficient for age? It’s the increase in log
odds of having diabetes for a one-year increase in age. A linear
relation is assumed between age and the log odds. It’s assumed,
therefore, that the log odds if you’re 25 is 0.0525 higher than if
you’re 24 and that the log odds if you’re 75 is 0.0525 higher than if
you’re 74. One of the nice things about working on the log scale is that
the difference between two log odds and the ratio of two log odds are
the same mathematically:

the log odds if you’re 25 minus the log odds if you’re 24 is 0.0525 (
i.e for 1 unit increase in age)

the log odds if you’re 25 divided by the log odds if you’re 24 is 0.0525

When we exponentiate 0.0525 we get 1.05 (to two decimal places). This is
an odds ratio, which is generally what’s reported when running logistic
regression models (and is generally reported to two decimal places).
It’s the ratio of the odds of having diabetes if you’re 25 divided by
the odds of having diabetes if you’re 24. Or that if you’re 75 divided
by that if you’re 74 etc. It’s the amount by which your odds increases
when you get a year older (1 unit) . So getting older is bad news, at
least in terms of diabetes, which is what we expected.

But wait – we haven’t yet checked to see whether this is statistically
significant or merely compatible with a chance result. The p value for
age is given in the “Pr(\>\|z\|)” column and is really tiny. R also uses
an asterisk system to point out the size of the p values. Age has three
asterisks, meaning close to zero, so it’s not compatible with a chance
result. Age is a statistically significant predictor.

#### Let’s predict the relationship between the outcome of diabetes and gender.

``` r
m <- glm(Outcome ~ Gender, family = binomial (link = logit))

summary(m)
```

    ## 
    ## Call:
    ## glm(formula = Outcome ~ Gender, family = binomial(link = logit))
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -0.5915  -0.5915  -0.5683  -0.5683   1.9509  
    ## 
    ## Coefficients:
    ##             Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept) -1.74150    0.18592  -9.367   <2e-16 ***
    ## Gendermale   0.08694    0.28352   0.307    0.759    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 334.87  on 389  degrees of freedom
    ## Residual deviance: 334.78  on 388  degrees of freedom
    ##   (13 observations deleted due to missingness)
    ## AIC: 338.78
    ## 
    ## Number of Fisher Scoring iterations: 4

In this data set, gender comes labelled as male and female, so the
coefficient for gender here is printed as “gendermale”. If your data set
has gender coded as 1 and 2, for example, then you’ll need to refer to
the documentation for that data set to see what 1 and 2 mean. Here, the
log odds for having diabetes for males is 0.0869 higher than that for
females. This is also the log odds ratio for males compared with
females. Again, if we exponentiate 0.0869, we get the odds ratio for
males compared with females, which is 1.09 (to two decimal places). That
implies higher odds for males, but we need to inspect the p value, which
is 0.759. That’s pretty high and well above the conventional threshold
of 0.05, so chance is a likely explanation for the result and we can
conclude that we don’t have any good evidence of a gender difference in
diabetes odds in this sample.

While in this data set, gender is nicely labelled, it’s a good idea in
general to check how R has entered gender into the model. Do this by
typing:

``` r
contrasts(Gender)
```

    ##        male
    ## female    0
    ## male      1

This confirms that the coefficient given in the output refers to males
because males have a 1 next to them in the above output and females have
a zero. The log odds for females are incorporated into the intercept.

Suppose you didn’t want to compare males relative to females but instead
the reverse? How can you get R to give you the odds ratio so that it’s
the odds for females divided by the odds for males?

R will by default organise values (called levels) of categorical
variables alphabetically. You can check the order like so:

``` r
levels(Gender)
```

    ## [1] "female" "male"

So by default, “female” is the first level. R will automatically set
this as the reference group in statistical analyses, such that the odds
ratios of other groups will be displayed relative to this one.

we can relevel/reorder it using :

``` r
Gender <- relevel(Gender, ref = "male")

# to check the new level:
levels(Gender)
```

    ## [1] "male"   "female"

“Male” is now the first group and will be treated as the reference
group. Let’s rerun the regression:

``` r
m <- glm(Outcome ~ Gender, family = binomial (link = logit))

summary(m)
```

    ## 
    ## Call:
    ## glm(formula = Outcome ~ Gender, family = binomial(link = logit))
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -0.5915  -0.5915  -0.5683  -0.5683   1.9509  
    ## 
    ## Coefficients:
    ##              Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)  -1.65456    0.21404  -7.730 1.08e-14 ***
    ## Genderfemale -0.08694    0.28352  -0.307    0.759    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 334.87  on 389  degrees of freedom
    ## Residual deviance: 334.78  on 388  degrees of freedom
    ##   (13 observations deleted due to missingness)
    ## AIC: 338.78
    ## 
    ## Number of Fisher Scoring iterations: 4

Now “genderfemale” is the coefficient, which represents the log odds of
diabetes of females compared with males. Notice that the estimate is the
same, except negative. This makes sense, because of the rule:

log(A/B) = −log(B/A)

The log odds ratio of having diabetes if you’re male compared with
female (A/B) is -1 \* the log odds of having diabetes if you’re female
compared with male (B/A).

Lastly, it’s useful to know that the glm command produces an R object
with various useful elements, identified using the dollar symbol \$,
that can be manipulated and exported. That’s the reason why we store the
glm output as an object, m. For instance, to see the model’s
coefficients, type:

``` r
m$coefficients
```

    ##  (Intercept) Genderfemale 
    ##  -1.65455835  -0.08693929

As these are log odds ratios and we want the odds ratios, it’s easy to
exponentiate the whole set rather than doing each one manually:

``` r
exp(m$coefficients)
```

    ##  (Intercept) Genderfemale 
    ##    0.1911765    0.9167328

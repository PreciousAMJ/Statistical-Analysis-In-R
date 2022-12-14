Simple Linear Regression Model
================

------------------------------------------------------------------------

## How to fit a regression model in R

You will now learn how to fit a regression model in R

The basic format of a linear regression is:

Y = α + β1\*X

Where:

Y = outcome (i.e. dependent) variable

X = predictor (i.e. independent) variable

α and β are parameters of the regression, with α = intercept (average Y
when X=0), and β = slope of the line (change in Y for a 1 unit increase
in X). Note: α and β are unit specific, so you’ll get different answers
if you use distance in metres and in feet.

------------------------------------------------------------------------

## *Simple Linear Regression For Continous Predictor*

We’ll fit a linear regression to quantify the relationship between
forced expiratory volume 1 (FEV1, which is the amount of air you can
force from your lungs in one second) and 6 minute walk distance
(MWT1Best, which is an assessment to measure the distance a person is
capable of walking on a flat, hard surface in 6 minutes) in COPD
(Chronic Obstructive Pulmonary Disease)patients.

Let’s import our dataset

``` r
COPD_Data <- read.csv("C:\\Users\\HP\\Documents\\Datasets\\COPD_student_dataset.csv") # used \\ instead of \ due to an error

View(COPD_Data)
```

To run a linear regression in R, the function you need to use is lm().
The basic format of this function is:

lm(outcome \~ predictor, data =dataframe)

In order to manipulate and investigate this model,\* you need to store
it as an R object\*, i.e., assign it to a vector. You can do this using
the operator sign (\<-). The format for this is:

new vector name\<-linear regression model

First, we should visualize the data, to see if there’s a linear
relationship between both variables

``` r
MWT1Best <- COPD_Data$MWT1Best

FEV1 <- COPD_Data$FEV1

scatter.smooth(MWT1Best, FEV1, main = "MWT1Best vs. FEV1")
```

![](README_figs/README-unnamed-chunk-3-1.png)<!-- --> The plot shows
that the relationship between the variables is linear, as lung function
increases, walking distance also increase as well.

we can also visualize using histogram and boxplot, to check for outliers
etc. (if an observation is an iutlier, a tiny cirlce will appear in the
boxplot)

``` r
hist(COPD_Data$FEV1)
```

![](README_figs/README-unnamed-chunk-4-1.png)<!-- -->

``` r
boxplot(COPD_Data$FEV1)
```

![](README_figs/README-unnamed-chunk-4-2.png)<!-- --> The plots show
that their are no outliers.

We can also check the strength of the correlation between both variables

``` r
cor_strenght <- cor.test(COPD_Data$FEV1, COPD_Data$MWT1Best) # default is pearson's

cor_strenght
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  COPD_Data$FEV1 and COPD_Data$MWT1Best
    ## t = 5.26, df = 98, p-value = 8.469e-07
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  0.3004898 0.6094629
    ## sample estimates:
    ##       cor 
    ## 0.4692142

the output shows a correlation of 0.47, which is fair.

To fit the linear regression model

``` r
MTWT1Best_FEV1 <- lm(MWT1Best ~ FEV1, data = COPD_Data)

summary(MTWT1Best_FEV1) # to view the output of this model
```

    ## 
    ## Call:
    ## lm(formula = MWT1Best ~ FEV1, data = COPD_Data)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -249.592  -58.227    7.881   63.551  291.612 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   279.92      24.55   11.40  < 2e-16 ***
    ## FEV1           74.11      14.09    5.26 8.47e-07 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 94.57 on 98 degrees of freedom
    ##   (1 observation deleted due to missingness)
    ## Multiple R-squared:  0.2202, Adjusted R-squared:  0.2122 
    ## F-statistic: 27.67 on 1 and 98 DF,  p-value: 8.469e-07

from our model, Walking distance = 279.92 + 74.11\*(lung function). This
means that each 1 unit increase in lung function, walking distane
increase by 74.11 units. And the intercept value of 279.92 tells us the
average expected walking distance when lung funtion is 0.

Pr(\>\|t\|) - This is the p-value associated with the model coefficients
(just FEV1 in this case), it’s less than 0.05 and we can say there’s a
statistically significant association between walking distance and lung
function.

The R-squared tells us the percentage of variation in walking distance
that can be explained by lung function, here it’s 21%

The p-value for the F-statistics is less than 0.05, this means that our
model is statistically significant and there’s a statistically
significant association between walking distance and lung function.

To view 95% interval use the command

``` r
confint(MTWT1Best_FEV1) # will display the intercept and slope at a 95% confidence interval
```

    ##                 2.5 %   97.5 %
    ## (Intercept) 231.19004 328.6456
    ## FEV1         46.15031 102.0710

To check *model assumptions*, you can graphically examine the linear
regression model using the function plot(). This function allows to
check for linearity, homoscedasticity, independence, and normality of
your assumptions. Four plots are generated:

The first is a constant variance plot, which checks for the homogeneity
of the variance and the linear relation. *If you see no pattern* in this
graph, then your assumptions are met. One of the key assumptions of
linear regression is that the residuals of a regression model are
roughly normally distributed and are homoscedastic at each level of the
explanatory variable. If these assumptions are violated, then the
results of our regression model could be misleading or unreliable. As
long as the residuals appear to be randomly and evenly distributed
throughout the chart around the value zero, we can assume that
homoscedasticity is not violated

The second plot is a Q-Q plot, which checks that the residuals follow a
normal distribution. *The points should fall on a line* if the normality
assumption is met. Note that for the Q-Q plot, the points toward the end
may not fall on the line, it’s not an issue, the focus is on the points
in the middle.

The third plot allows to detect *heterogeneity* ( i.e if the variance
increase as the values becomes larger or smaller(variance should be
approximately equal all through)) of the variance.

The fourth plot allows for the detection of points that have a large
impact on the regression coefficients.

You can view all 4 plots in one output by setting a viewing format of 2
by 2 plots, using the command: par(mfrow=c(2,2))

If you want to go back to a viewing format of one plot per page after
that, simply use the command: par(mfrow=c(1,1))

For our regression model, plot(MWT1Best_FEV1) gives the following
output:

``` r
# par(mfrow = c(2,2))

plot(MTWT1Best_FEV1) 
```

![](README_figs/README-unnamed-chunk-8-1.png)<!-- -->![](README_figs/README-unnamed-chunk-8-2.png)<!-- -->![](README_figs/README-unnamed-chunk-8-3.png)<!-- -->![](README_figs/README-unnamed-chunk-8-4.png)<!-- -->
Note that for the first plot, the red line is close to the dashed line
(0), showing linearity. It also indicate that points 1, 9 and 100 are
outliers. Note “fitted” in the graph refer to the predicted value.

------------------------------------------------------------------------

## *Simple linear Regression For Binary Predictor*

We’ll fit a linear regression model using 6 minute walk distance
(MWT1BEST) as the response variable and gender (a binary variable) as
the predictor variable.

First, we need to let R recognise that the variable “gender” is a
categorical one.

``` r
Gender <- as.factor(COPD_Data$gender) # adding "as.factor" helps R to recognise the variable as a categorical variable.

levels(Gender) # to view the various categories
```

    ## [1] "0" "1"

for this dataset, gender0 represent female, and gender1 represent males.

``` r
Model <- lm(MWT1Best ~ Gender, data = COPD_Data)

summary(Model)
```

    ## 
    ## Call:
    ## lm(formula = MWT1Best ~ Gender, data = COPD_Data)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -290.09  -87.96   18.66   74.92  288.91 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   379.58      17.68  21.473   <2e-16 ***
    ## Gender1        30.51      22.10   1.381     0.17    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 106.1 on 98 degrees of freedom
    ##   (1 observation deleted due to missingness)
    ## Multiple R-squared:  0.01908,    Adjusted R-squared:  0.009073 
    ## F-statistic: 1.906 on 1 and 98 DF,  p-value: 0.1705

``` r
plot(Model)
```

![](README_figs/README-unnamed-chunk-10-1.png)<!-- -->![](README_figs/README-unnamed-chunk-10-2.png)<!-- -->![](README_figs/README-unnamed-chunk-10-3.png)<!-- -->![](README_figs/README-unnamed-chunk-10-4.png)<!-- -->
Note that the coefficient in the output is for gender = 1 (male).
Walking distance for males will then be 379.58 + 30.51*1. To get for
gender = 0 (female), 379.58 + 30.51*0 = 379.58 (the intercept)

------------------------------------------------------------------------

## *Simple linear Regression For Categorical Predictor*

we’ll fit a linear regression using 6 minute walk distance (MWt1Best) as
the response variable and chronic obstructive pulmonary disease (COPD)
which is a categorical variable as the predictor variable. First, we
need to let R recognise that the variable “copd” is a categorical one.

``` r
COPD <- as.factor(COPD_Data$copd) # adding "as.factor" helps R to recognise the variable as a categorical variable.

levels(COPD) # to view the various categories
```

    ## [1] "1" "2" "3" "4"

For this dataset, copd levels 1,2,3,4 represent mild, moderate, severe
and very severe respectively.

``` r
Model <- lm(MWT1Best ~ COPD, data = COPD_Data)

summary(Model)
```

    ## 
    ## Call:
    ## lm(formula = MWT1Best ~ COPD, data = COPD_Data)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -248.67  -51.18    0.91   64.23  330.33 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   458.09      20.44  22.410  < 2e-16 ***
    ## COPD2         -51.09      25.43  -2.009  0.04735 *  
    ## COPD3         -89.42      27.82  -3.215  0.00178 ** 
    ## COPD4        -167.21      40.24  -4.156 7.05e-05 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 98.03 on 96 degrees of freedom
    ##   (1 observation deleted due to missingness)
    ## Multiple R-squared:  0.1792, Adjusted R-squared:  0.1535 
    ## F-statistic: 6.984 on 3 and 96 DF,  p-value: 0.0002671

``` r
plot(Model)
```

![](README_figs/README-unnamed-chunk-12-1.png)<!-- -->![](README_figs/README-unnamed-chunk-12-2.png)<!-- -->![](README_figs/README-unnamed-chunk-12-3.png)<!-- -->![](README_figs/README-unnamed-chunk-12-4.png)<!-- -->
NOTE: there are only coefficients for three of the COPD levels,this is
because the coefficients represent the comparison with the fourth level,
which is often referred to as the *reference group*. The linear
regression model automatically uses COPD level 1 (i.e. ‘mild’) as the
reference category. eg, if we find the walking distance for grp2:
walking distance for group2 = 458.09 + -51.09\*(1) (grp2 becomes 1 and
others 0) the result is the walking distance for grp2 in relation to
grp1. for this case, walking distance for group2 is 407 times that of
grp1.

Reference group can be changed using the relevel() function.

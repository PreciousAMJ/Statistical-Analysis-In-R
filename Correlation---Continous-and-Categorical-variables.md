R Notebook
================

## How to Calculate Correlation Between Continous Numerical Variables

We use the Pearson or spearman Correlation Coefficient to calculate the
correlation between continuous numerical variables.

To load the data set we’ll be using

``` r
COPD_Data <- read.csv("C:\\Users\\HP\\Documents\\Datasets\\COPD_student_dataset.csv") # used \\ instead of \ due to an error

View(COPD_Data)
```

The basic command for a correlation test is cor.test(x,y) where you can
specify which method you want to use using the command method =
“pearson” or “spearman”. You need to remove missing values, otherwise
you will have an error message. To do this, use the command use =
“complete.obs”

Let’s view the corellation between walking distance (MWt1) and lung
function (FEV1) from the COPD_Data dataset

``` r
cor.test(COPD_Data$MWT1, COPD_Data$FEV1, use = "complete.obs" , method = "pearson")
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  COPD_Data$MWT1 and COPD_Data$FEV1
    ## t = 5.0835, df = 97, p-value = 1.803e-06
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  0.2872522 0.6015960
    ## sample estimates:
    ##       cor 
    ## 0.4586551

Pearson’s correlation coefficient is 0.46 and the 95% confidence
interval suggests the population coefficient is likely to lie somewhere
between 0.3 to 0.6. The p value is less than 0.001 so there is very
strong evidence against the null hypothesis of the coefficient being
zero.

You have discovered moderate correlation between walking distance and
lung function.

Let’s use the spearman’s correlation method.

``` r
cor.test(COPD_Data$MWT1, COPD_Data$FEV1, use = "complete.obs" , method = "spearman")
```

    ## Warning in cor.test.default(COPD_Data$MWT1, COPD_Data$FEV1, use =
    ## "complete.obs", : Cannot compute exact p-value with ties

    ## 
    ##  Spearman's rank correlation rho
    ## 
    ## data:  COPD_Data$MWT1 and COPD_Data$FEV1
    ## S = 89438, p-value = 3.537e-06
    ## alternative hypothesis: true rho is not equal to 0
    ## sample estimates:
    ##       rho 
    ## 0.4468868

The Spearman’s correlation coefficient provides a similar result with
estimated correlation of 0.45.

------------------------------------------------------------------------

## How to Calculate Correlation Between Categorical Variables

Often we use the Pearson or spearman Correlation Coefficient to
calculate the correlation between continuous numerical variables.
However, we must use a different metric to calculate the correlation
between categorical variables – that is, variables that take on names or
labels

There are three metrics that are commonly used to calculate the
correlation between categorical variables:

1.  Tetrachoric Correlation: Used to calculate the correlation between
    binary categorical variables.

2.  Polychoric Correlation: Used to calculate the correlation between
    ordinal categorical variables.

3.  Cramer’s V: Used to calculate the correlation between nominal
    categorical variables.

### Tetrachoric Correlation

Tetrachoric correlation is used to calculate the correlation between
binary categorical variables. Recall that binary variables are variables
that can only take on one of two possible values.

The value for tetrachoric correlation ranges from -1 to 1 where -1
indicates a strong negative correlation, 0 indicates no correlation, and
1 indicates a strong positive correlation.

We’ll use the Tetracholic corellation method to find the correlation
between Diabetes and Gender in the COPD_Data dataset.

First, let’s create a 2x2 table using both variables.

``` r
table <- table(COPD_Data$Diabetes, COPD_Data$gender)
table
```

    ##    
    ##      0  1
    ##   0 28 52
    ##   1  8 13

we’ll use the tetracholic funtion from the psych library for this

``` r
# install.packages("psych")

library(psych)
```

    ## Warning: package 'psych' was built under R version 4.2.2

``` r
tetrachoric(table)
```

    ## Call: tetrachoric(x = table)
    ## tetrachoric correlation 
    ## [1] -0.047
    ## 
    ##  with tau of 
    ##     0     0 
    ##  0.81 -0.37

The output shows a correlation of -0.05(-5%), which means there’s is a
low association between Gender and the Diabetes.

### Polychoric Correlation

Polychoric correlation is used to calculate the correlation between
ordinal categorical variables. Recall that ordinal variables are
variables whose possible values have a natural order.

The value for polychoric correlation ranges from -1 to 1 where -1
indicates a strong negative correlation, 0 indicates no correlation, and
1 indicates a strong positive correlation.

We’ll find the correlation between the age groups and COPD_Data severity
(1,2,3,4 indicating mild, moderate, severe and very-severe respectively)
from the COPD_Data data set.

``` r
Age_Group <- as.factor(COPD_Data$AGEquartiles) # so R will recognise it as a categorical variable/factor.

COPD_Severity <- as.factor(COPD_Data$copd)
```

we’ll use the polychor function from the polycor library for this

``` r
# install.packages("polycor")

library("polycor")
```

    ## Warning: package 'polycor' was built under R version 4.2.2

    ## 
    ## Attaching package: 'polycor'

    ## The following object is masked from 'package:psych':
    ## 
    ##     polyserial

``` r
polychor(Age_Group, COPD_Severity)
```

    ## [1] 0.02694179

The output shows a correlation of 0.03 (3%), which shows a weak
association between the age groups and COPD_Data severity. i.e the level
of COPD_Data severity is not dependent on the age group.

### Cramer’s V

Cramer’s V is used to calculate the correlation between nominal
categorical variables. Recall that nominal variables are ones that take
on category labels but have no natural ordering.

The value for Cramer’s V ranges from 0 to 1, with 0 indicating no
association between the variables and 1 indicating a strong association
between the variables.

We’ll find the association between Gender and COPD_Data severity. Note
though the COPD_Data severity is actually orderd but it’s nominal

``` r
Gender <- as.factor(COPD_Data$gender)

COPD_Severity <- as.factor(COPD_Data$COPDSEVERITY)
```

We’ll use the cramerV function from the rcompanion library for this.

``` r
# install.packages("rcompanion")

library("rcompanion")
```

    ## Warning: package 'rcompanion' was built under R version 4.2.2

    ## 
    ## Attaching package: 'rcompanion'

    ## The following object is masked from 'package:psych':
    ## 
    ##     phi

``` r
cramerV(Gender, COPD_Severity)
```

    ## Cramer V 
    ##   0.1355

The output 0.136 shows a weak association between Gender and the level
of COPD_Data severity.

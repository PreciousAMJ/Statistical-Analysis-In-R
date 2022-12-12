Cross Tabulation
================

------------------------------------------------------------------------

Load the dataset

``` r
Diabetes_Dataset <- read.csv("C:/Users/HP/Documents/Datasets/Diabetes Dataset.csv")

View(Diabetes_Dataset)

dim(Diabetes_Dataset) # To get the num of rows and columns
```

    ## [1] 403  24

``` r
dimnames(Diabetes_Dataset)[[2]] # There are two parts to the dimnames object: names of the rows, which is generally not useful, and names of the columns, which definitely is. You just want the second of those, hence the “[[2]]” bit. This gives the same output, but just the column names
```

    ##  [1] "X"         "id"        "chol"      "stab.glu"  "hdl"       "ratio"    
    ##  [7] "glyhb"     "location"  "age"       "gender"    "height"    "weight"   
    ## [13] "frame"     "bp.1s"     "bp.1d"     "bp.2s"     "bp.2d"     "waist"    
    ## [19] "hip"       "time.ppn"  "insurance" "fh"        "smoking"   "dm"

To do any analysis, my preference is to make one variable per column
rather than refer directly to the column within the data set every time.
Not all of the columns are very interesting, though, so don’t bother
with “id” for example. We’re not going to use it. When doing this, you
need to tell R which variables are categorical, as it will assume
they’re all continuous by default:

``` r
Cholesterol <- Diabetes_Dataset$chol # cholesterol is continous

Gender <- as.factor(Diabetes_Dataset$gender) # gender is not continous, so we let R recognise it as a category by using the "as.factor" funtion

Outcome <- as.factor(Diabetes_Dataset$dm) # same for Outcome
```

To see how many males and females we have, you can use the “table”
command. It’s worth also getting the total:

``` r
t <- table(Gender) # it'll give the count of the distinct values in the column gender, we store the tabulation for future manipulation
t
```

    ## Gender
    ## female   male 
    ##    234    169

``` r
addmargins(t) # it'll give the overall totals
```

    ## Gender
    ## female   male    Sum 
    ##    234    169    403

Annoyingly, it doesn’t give the percentages of any of the categories, a
really basic analysis task. With the tabulation stored as an R object,
though, we can do this quite simply using the “prop.table” command: The
“round” funtion rounds a number into specified number of digits The
“prop.table” funtion gives the proportion of each distinct
values/category of a table

``` r
prop.table(t) # to get the proportion of each distinct values in the column
```

    ## Gender
    ##    female      male 
    ## 0.5806452 0.4193548

``` r
100*prop.table(t) # to get the proportion in %
```

    ## Gender
    ##   female     male 
    ## 58.06452 41.93548

``` r
round(100*prop.table(t), digits = 1) # gets the % rounded to 1dp
```

    ## Gender
    ## female   male 
    ##   58.1   41.9

“table” *excludes missing values by default*. To see these – and we
ALWAYS want to see these – use an “exclude=NULL” option when making the
variable:

``` r
Outcome2 <- factor(Outcome, exclude=NULL) # make new factor from the old one

table(Outcome2) # display the counts including the missings (NAs)
```

    ## Outcome2
    ##   no  yes <NA> 
    ##  330   60   13

# Let’s continue with other variables

``` r
Height <- Diabetes_Dataset$height

Weight <- Diabetes_Dataset$weight

summary(Height) # note summary command gives us the missing values by default
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
    ##   52.00   63.00   66.00   66.02   69.00   76.00       5

``` r
summary(Weight)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
    ##    99.0   151.0   172.5   177.6   200.0   325.0       1

Note, unlike “table”, “summary” command will include the null values

------------------------------------------------------------------------

## How to calculate body mass index (BMI) from height and weight

As this is a US data set, height is in inches and weight is in pounds.
Neither height nor weight are particularly useful by themselves,
however, so it’s common to combine them into the body mass index (BMI),
which is weight divided by the square of height; both measures need to
be in SI units, i.e. kilograms and metres, so we need to convert:

``` r
Height.si <- Height*0.0254

Weight.si <- Weight*0.453592

BMI <- Weight.si/Height.si^2

summary(BMI)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
    ##   15.20   24.13   27.80   28.79   32.24   55.79       6

## How to make a categorical variable from a continuous one

For display purposes and also because thresholds are used in clinical
decision-making, it’s useful to categorise continuous variables even
though this loses information. For instance, it’s very common to
categorise BMI. One reason is so that public health agencies can track
the numbers of people and proportion of the population who are obese,
for example. Tracking the mean or median BMI for the population won’t
tell you this. There are several ways to categorise BMI in R. Here’s one
way. Let’s say we want groups for underweight \[\<18.5\], normal
\[18.5-25\], overweight \[\>25\] and obese \[\>30\]; a morbidly obese
category also exists but let’s ignore that here.

``` r
BMI_Categorised <- ifelse(BMI < 18.5, "Underweight",
                          ifelse(BMI >=18.5 & BMI <= 25, "Normal",
                                 ifelse(BMI > 25 & BMI <= 30, "Overweight",
                                        ifelse(BMI > 30, "Obese", NA))))
#Note the way the ifelse are nested - the "else" (no) part of a ifelse is another ifelse. 
```

Let’s use “table” to see the distinct values of each of the category

``` r
table(BMI_Categorised, exclude = NULL)
```

    ## BMI_Categorised
    ##      Normal       Obese  Overweight Underweight        <NA> 
    ##         113         152         123           9           6

Create a cross tabulation to view the diabetes outcome for each BMI
category

``` r
Outcome_by_BMI <- table(BMI_Categorised, Outcome2, exclude = NULL)

Outcome_by_BMI
```

    ##                Outcome2
    ## BMI_Categorised  no yes <NA>
    ##     Normal      100   9    4
    ##     Obese       118  29    5
    ##     Overweight   99  20    4
    ##     Underweight   9   0    0
    ##     <NA>          4   2    0

In the function prop.table(), using the argument margin = 1 we can
specify that the table gives us the *row* proportion, i.e. the
proportion of each column (diabetes status) in each row (BMI category).
If we entered margin = 2, this would display the inverse, i.e. the
proportion of each row in each column.

``` r
prop.table(Outcome_by_BMI, margin = 1) # to get the proportion of the outcome for each bmi category
```

    ##                Outcome2
    ## BMI_Categorised         no        yes       <NA>
    ##     Normal      0.88495575 0.07964602 0.03539823
    ##     Obese       0.77631579 0.19078947 0.03289474
    ##     Overweight  0.80487805 0.16260163 0.03252033
    ##     Underweight 1.00000000 0.00000000 0.00000000
    ##     <NA>        0.66666667 0.33333333 0.00000000

``` r
100 * prop.table(Outcome_by_BMI, margin = 1)
```

    ##                Outcome2
    ## BMI_Categorised         no        yes       <NA>
    ##     Normal       88.495575   7.964602   3.539823
    ##     Obese        77.631579  19.078947   3.289474
    ##     Overweight   80.487805  16.260163   3.252033
    ##     Underweight 100.000000   0.000000   0.000000
    ##     <NA>         66.666667  33.333333   0.000000

``` r
round(100 * prop.table(Outcome_by_BMI, margin = 1), digits = 1)
```

    ##                Outcome2
    ## BMI_Categorised    no   yes  <NA>
    ##     Normal       88.5   8.0   3.5
    ##     Obese        77.6  19.1   3.3
    ##     Overweight   80.5  16.3   3.3
    ##     Underweight 100.0   0.0   0.0
    ##     <NA>         66.7  33.3   0.0

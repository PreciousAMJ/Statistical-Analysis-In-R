Hypothesis Testing- Z score, Pvalue, Z test, T test
================

To find Z-score in R for an array, we use the normal Z-score formula;
z_scores \<- (data-mean(data))/sd(data)

So the Z-score can then be compared with the z-score of an alpha value,
in hypothesis testing.

eg to know the percentage of the population/sample that’s higher than it
(right-tailed) or lower than it (left-tailed) using the pnorm function
(for z score), or pt function (for z-sscore).

## P-value for a T or Z score

To find the P-value of a particular T_score

``` r
pt(q=0.025, df=15, lower.tail=TRUE) # i.e percentage of the population lower than the value of that z_score
```

    ## [1] 0.5098077

Note that the % of the population lower than a number is same as the
percentage of the population gretaer than the -ve of that number. This
is not really important in R, because of the lower.tail = TRUE or FASLE
option. but in language like python it’s ( python by default calculate
for the % of the population greater than the number) so to get the
percentage lower, you’ll add minus.

Suppose we want to find the p-value associated with a t-score of 1.24
and df = 22 in a two-tailed hypothesis test.

``` r
2*pt(q=1.24, df=22, lower.tail=FALSE)
```

    ## [1] 0.228039

Suppose we want to find the p-value associated with a z-score of 1.87 in
a right-tailed hypothesis test.

``` r
pnorm(q=1.87, lower.tail=FALSE)
```

    ## [1] 0.03074191

for two tailed we’ll multiply 2.

we can also find for an array:

``` r
data <- c(6, 7, 7, 12, 13, 13, 15, 16, 19, 22)
data
```

    ##  [1]  6  7  7 12 13 13 15 16 19 22

``` r
z_score <- c((data - mean(data))/sd(data))
z_score
```

    ##  [1] -1.3228757 -1.1338934 -1.1338934 -0.1889822  0.0000000  0.0000000
    ##  [7]  0.3779645  0.5669467  1.1338934  1.7008401

``` r
# to find the p value for all the z_scores in that array
pnorm(z_score, lower.tail = TRUE) # here we find the percentage of the population that's less than (lower.tail = TRUE) the value of each z-score
```

    ##  [1] 0.09293837 0.12841963 0.12841963 0.42505337 0.50000000 0.50000000
    ##  [7] 0.64727151 0.71462481 0.87158037 0.95551349

``` r
# to find the p value for all the t_scores in that array
pt(z_score, df = 9, lower.tail = TRUE) # for t-score
```

    ##  [1] 0.1092505 0.1430691 0.1430691 0.4271499 0.5000000 0.5000000 0.6428914
    ##  [8] 0.7076945 0.8569309 0.9384071

notice the z and t scores are close

|                                                                                                                                                                                                                                                                                               |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| \## Z-score For An Alpha Value                                                                                                                                                                                                                                                                |
| Z-score for a critical value in R; p is the critical level, lower tail means the left side(-ve side, for left-tailed test), if it’s false, that’s right side for right-tailed test. lower.tail TRUE or FORCE can go for 2-tailed test, you just put -ve and positive values where appropriate |
| `r qnorm(p=.05/2, lower.tail=FALSE)`                                                                                                                                                                                                                                                          |
| `## [1] 1.959964` then we compare this Z-score with the calculated Z-score to know if the null hypoyhesis will be accepted or rejected (in a Z-test)                                                                                                                                          |

\##T-score For An Alpha Value

T-score for critical value in R; p is the critical level. df is the
degrees of freedom. lower tail means the left side(-ve side, for
left-tailed test), if it’s false, that’s right side for right-tailed
test. lower.tail TRUE or FORCE can go for 2-tailed test, you just put
-ve and positive values where appropriate

``` r
qt(p=0.05/2, df=65, lower.tail=FALSE)
```

    ## [1] 1.997138

then we compare this T-score with the calculated T-score to know if the
null hypoyhesis will be accepted or rejected (in a T-test)

For a two-tailed test, you can do for any of the tails, just add the -ve
and +ve signs where necessary

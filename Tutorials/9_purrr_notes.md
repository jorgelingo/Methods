---
title: "Purrr tutorial"
author: "Jorge Valdés Kroff"
date: "November 14, 2018"
output: 
  html_document:
    keep_md: yes
--- 
# Introduction

This is a simple tutorial based on Chapter 17 from *R for Data Science* by Hadley Wickham and Garrett Grolemund. 
First, load in the `tidyverse` package.


```r
suppressMessages(library(tidyverse))
```

The `purrr` package is designed with a set of `map()` functions that are a friendlier version of *for* loops. So it's a good idea to get a basic sense of what *for* loops do. 
We can create a very simple **tibble** with 4 columns of values.


```r
df <- tibble(
  a = rnorm(10),
  b = rnorm(10),
  c = rnorm(10),
  d = rnorm(10)
)
print(df) #your values will look different than mine
```

```
## # A tibble: 10 x 4
##          a       b       c       d
##      <dbl>   <dbl>   <dbl>   <dbl>
##  1 -0.998   0.562  -0.672  -2.09  
##  2 -1.80    0.0863 -0.326  -0.296 
##  3  0.617   0.265   1.15    0.606 
##  4  0.940  -1.56   -0.448  -0.609 
##  5 -0.981  -2.15    0.266  -0.971 
##  6  0.204  -0.104   1.03    0.0753
##  7 -0.655   0.679  -0.417  -1.29  
##  8 -0.465  -0.313  -0.940   1.36  
##  9  0.718  -0.855  -0.0293 -0.202 
## 10  0.0750 -0.970  -1.17    2.53
```

We may want to compute the `mean` for each column. This would be fine to do for each individual column.


```r
mean(df$a)
```

```
## [1] -0.234484
```

```r
mean(df$b)
```

```
## [1] -0.4362017
```

```r
mean(df$c)
```

```
## [1] -0.1556901
```

```r
mean(df$d)
```

```
## [1] -0.08861142
```

But we are repeating the same action multiple times. We can use a *for* loop to iterate this action for us. A *for* loop requires 3 pieces:

  * An *output* vector where the results will be stored
  * A *sequence*, `i`, which determines what is to be looped over
  * The *body*, which is where the main action happens
  
Let's replace the above repeated code with a *for* loop.


```r
results <- vector("double", ncol(df)) #create a vector that is of class "double", that is the length of the number of columns in **df**
#now create the for loop
for (i in seq_along(df)) { #here is the sequence; `seq_along() is a "safe" version of 1:length()
  results[[i]] <- mean(df[[i]]) #here is the body
}
results
```

```
## [1] -0.23448399 -0.43620175 -0.15569013 -0.08861142
```

There are several variations on *for* loops, including what you loop over (e.g., indices, names, and elements), whether you know the length of the output (store individual results in list, then string together), whether you know the length of the sequence (use a *while* loop). 


```r
#If you don't know the ouptput length (if you are randomly generating or sampling something), then you could save the results of each "run" to a list, then combine after the loop is done:
means <- c(0, 5, 10) #create a vector of means
list.output <- vector("list", length(means)) #output
for (i in seq_along(means)) { #sequence
  n <- sample(100, 1) #from 0:100, choose one random number
  list.output[[i]] <- rnorm(n, means[[i]]) #generate a random normal distribution of n numbers, with mean from sequence
}
str(list.output) #check out structure of output
```

```
## List of 3
##  $ : num [1:22] -1.024 0.645 -0.396 1.078 0.232 ...
##  $ : num [1:80] 3.33 5.6 5.74 4.87 4.43 ...
##  $ : num [1:93] 10.72 9.68 9.07 10.05 9.75 ...
```

```r
output <- unlist(list.output) #combine together
print(output)
```

```
##   [1] -1.02383501  0.64453858 -0.39640240  1.07806003  0.23184243
##   [6] -1.17140565 -0.81882862 -3.01232903 -1.49636674  0.71777748
##  [11]  1.01999988  0.33322627 -1.86301368  0.61208329 -0.13724988
##  [16]  0.54197331 -0.23527762 -0.35877484  0.87768105  1.42881242
##  [21]  0.43808117  0.03326921  3.32535623  5.59895680  5.73675680
##  [26]  4.86766104  4.43374366  6.95050449  6.00695116  4.91123932
##  [31]  6.12024203  5.93217389  5.91611093  5.37174798  6.01505346
##  [36]  6.04103010  5.42035324  2.52239540  4.84976347  7.91422043
##  [41]  4.84546470  6.08090868  4.56146558  4.33256848  5.32874599
##  [46]  4.12070068  5.41931933  5.39727774  3.60617053  6.39213006
##  [51]  5.17457220  5.09486836  3.22133607  4.86147899  5.50893296
##  [56]  5.00108078  5.34011670  5.31735193  6.14614091  4.63293878
##  [61]  5.94697445  6.08064981  5.92957031  4.33334851  4.89855475
##  [66]  3.41282879  4.74124776  5.59610452  5.14519950  3.37243312
##  [71]  6.39384959  5.18820563  5.03770636  6.46340174  6.37316354
##  [76]  5.58535343  5.54562886  4.34438612  4.08506420  6.98872644
##  [81]  5.07836939  5.61208739  4.84954173  4.41000510  5.61911499
##  [86]  4.81604408  3.33503731  4.86845848  4.72862042  5.11344004
##  [91]  6.44168287  5.23797391  5.95786609  5.84769555  2.58969097
##  [96]  4.23416474  5.14011838  4.52939218  5.39826688  4.04596726
## [101]  4.30947676  3.69631223 10.71670552  9.67724953  9.06590425
## [106] 10.05389724  9.74999258  9.66202018  9.70137821 10.32462129
## [111]  9.00062378 10.58000955 10.21306436  8.69789068  8.51530138
## [116] 10.09812539 11.90756510 10.62118704  8.64550036 10.81367722
## [121] 10.64062786  9.18023240 11.08646057  9.44928952  9.58726124
## [126]  9.94154329 10.59055407  7.20293750  8.98702214  9.48812497
## [131]  8.54299354  9.69916822  9.44161908 10.08764282  9.93987134
## [136] 10.40199230 10.49864960 10.59048390 10.22835677 11.18357063
## [141]  9.20915677 10.38027704  9.85671597  8.56007342 10.40826816
## [146]  9.75354563 10.65781062 11.19391415 10.90732863 11.80821287
## [151]  9.35300776 12.15794182 10.11571753 10.18789619 10.18344153
## [156] 10.39437245  9.52430208  9.91620864  7.78613991  9.81361085
## [161]  9.35182048  9.80137212 11.88516022 11.12205425 10.07630599
## [166]  8.97163903  8.87875216  9.84731691  8.71925689  9.61463768
## [171]  9.53754140 12.82172087 10.56108251 12.55565442 12.75791613
## [176] 10.83389223 10.04375676  9.60977207 12.15932273  9.64083839
## [181]  9.88809868 10.20334623 10.28927407  9.70275919  7.78102516
## [186]  8.03566115 10.43743650 10.54228745  9.95439617 10.28257029
## [191] 10.15086529 11.75029231  9.80656356 10.61272868 10.98571830
```

*while* loops only run while some condition is met. 


```r
#A simple while loop that shows us how many tries it takes to get three heads in a row
flip <- function() sample(c("T", "H"), 1) #sample from "T"|"H" only once
flips <- 0 
nheads <- 0 #these are both outputs

while (nheads < 3) { #sequence: continue looping while the number of flips results in "heads" 
  if (flip() == "H") { #another conditional: if the result of `flip()` is "H", then do something
    nheads <- nheads + 1 #add 1 to the nheads vector
  } else { #else do this
    nheads <- 0 #reset to 0
  }
  flips <- flips + 1 #add 1 to flips as we iterate through the prior conditional
} #the while loop will stop once the first conditional is met
flips #print the number of flips needed; your number will be different
```

```
## [1] 10
```

*for* loops can be useful under certain situtations, but they are really opaque. The `map*()` functions were created as a simpler way do the same thing. The function name specifies the output:

  * `map()` makes lists
  * `map_lgl()` makes a logical vector
  * `map_int()` makes an integer vector
  * `map_dbl()` makes a double vector
  * `map_chr()` makes a character vector

Each function takes a vector as input (1st argument), applies a function to each piece (2nd argument), then returns a new vector that is the same length and has the same names. The type of vector depends on the suffix of the function. Using the **df** that we created earlier, let's redo the computation for means for each column as well as some other descriptives.


```r
map_dbl(df, mean)
```

```
##           a           b           c           d 
## -0.23448399 -0.43620175 -0.15569013 -0.08861142
```

```r
map_dbl(df, median)
```

```
##          a          b          c          d 
## -0.1949301 -0.2087079 -0.3717649 -0.2487687
```

```r
map_dbl(df, sd)
```

```
##         a         b         c         d 
## 0.8891860 0.9307796 0.7748810 1.3339100
```

This is pretty simple to understand compared to *for* loops and is less code intensive (less required). It gets even easier to read when combined with the pipe operator.


```r
df %>% 
  map_dbl(mean)
```

```
##           a           b           c           d 
## -0.23448399 -0.43620175 -0.15569013 -0.08861142
```

You can also pass on additional arguments in `map*()` functions, which can be useful


```r
df %>% 
  map_dbl(mean, trim = 0.5)
```

```
##          a          b          c          d 
## -0.1949301 -0.2087079 -0.3717649 -0.2487687
```

Now we can apply the same principle to iterating more interesting functions over some grouping variable in a dataset. 


```r
head(mtcars)
```

```
##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1
```

```r
str(mtcars)
```

```
## 'data.frame':	32 obs. of  11 variables:
##  $ mpg : num  21 21 22.8 21.4 18.7 18.1 14.3 24.4 22.8 19.2 ...
##  $ cyl : num  6 6 4 6 8 6 8 4 4 6 ...
##  $ disp: num  160 160 108 258 360 ...
##  $ hp  : num  110 110 93 110 175 105 245 62 95 123 ...
##  $ drat: num  3.9 3.9 3.85 3.08 3.15 2.76 3.21 3.69 3.92 3.92 ...
##  $ wt  : num  2.62 2.88 2.32 3.21 3.44 ...
##  $ qsec: num  16.5 17 18.6 19.4 17 ...
##  $ vs  : num  0 0 1 1 0 1 0 1 1 1 ...
##  $ am  : num  1 1 1 0 0 0 0 0 0 0 ...
##  $ gear: num  4 4 4 3 3 3 3 4 4 4 ...
##  $ carb: num  4 4 1 1 2 1 4 2 2 4 ...
```

```r
summary(mtcars)
```

```
##       mpg             cyl             disp             hp       
##  Min.   :10.40   Min.   :4.000   Min.   : 71.1   Min.   : 52.0  
##  1st Qu.:15.43   1st Qu.:4.000   1st Qu.:120.8   1st Qu.: 96.5  
##  Median :19.20   Median :6.000   Median :196.3   Median :123.0  
##  Mean   :20.09   Mean   :6.188   Mean   :230.7   Mean   :146.7  
##  3rd Qu.:22.80   3rd Qu.:8.000   3rd Qu.:326.0   3rd Qu.:180.0  
##  Max.   :33.90   Max.   :8.000   Max.   :472.0   Max.   :335.0  
##       drat             wt             qsec             vs        
##  Min.   :2.760   Min.   :1.513   Min.   :14.50   Min.   :0.0000  
##  1st Qu.:3.080   1st Qu.:2.581   1st Qu.:16.89   1st Qu.:0.0000  
##  Median :3.695   Median :3.325   Median :17.71   Median :0.0000  
##  Mean   :3.597   Mean   :3.217   Mean   :17.85   Mean   :0.4375  
##  3rd Qu.:3.920   3rd Qu.:3.610   3rd Qu.:18.90   3rd Qu.:1.0000  
##  Max.   :4.930   Max.   :5.424   Max.   :22.90   Max.   :1.0000  
##        am              gear            carb      
##  Min.   :0.0000   Min.   :3.000   Min.   :1.000  
##  1st Qu.:0.0000   1st Qu.:3.000   1st Qu.:2.000  
##  Median :0.0000   Median :4.000   Median :2.000  
##  Mean   :0.4062   Mean   :3.688   Mean   :2.812  
##  3rd Qu.:1.0000   3rd Qu.:4.000   3rd Qu.:4.000  
##  Max.   :1.0000   Max.   :5.000   Max.   :8.000
```

```r
unique(mtcars$cyl)
```

```
## [1] 6 4 8
```

notice that there are 3 unique values in $cyl, so we can run a separate linear regression on each value for cylinder.


```r
models <- mtcars %>% 
  split(.$cyl) %>% 
  map(~lm(mpg ~ wt, data = .))
summary(models)
```

```
##   Length Class Mode
## 4 12     lm    list
## 6 12     lm    list
## 8 12     lm    list
```

Notice that we created a list that contains the outputs in each element of the list.


```r
summary(models[[1]]) #to access each element, use [[]]
```

```
## 
## Call:
## lm(formula = mpg ~ wt, data = .)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -4.1513 -1.9795 -0.6272  1.9299  5.2523 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   39.571      4.347   9.104 7.77e-06 ***
## wt            -5.647      1.850  -3.052   0.0137 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 3.332 on 9 degrees of freedom
## Multiple R-squared:  0.5086,	Adjusted R-squared:  0.454 
## F-statistic: 9.316 on 1 and 9 DF,  p-value: 0.01374
```

```r
summary(models[[2]])
```

```
## 
## Call:
## lm(formula = mpg ~ wt, data = .)
## 
## Residuals:
##      Mazda RX4  Mazda RX4 Wag Hornet 4 Drive        Valiant       Merc 280 
##        -0.1250         0.5840         1.9292        -0.6897         0.3547 
##      Merc 280C   Ferrari Dino 
##        -1.0453        -1.0080 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)   
## (Intercept)   28.409      4.184   6.789  0.00105 **
## wt            -2.780      1.335  -2.083  0.09176 . 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1.165 on 5 degrees of freedom
## Multiple R-squared:  0.4645,	Adjusted R-squared:  0.3574 
## F-statistic: 4.337 on 1 and 5 DF,  p-value: 0.09176
```

```r
summary(models[[3]])
```

```
## 
## Call:
## lm(formula = mpg ~ wt, data = .)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -2.1491 -1.4664 -0.8458  1.5711  3.7619 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  23.8680     3.0055   7.942 4.05e-06 ***
## wt           -2.1924     0.7392  -2.966   0.0118 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.024 on 12 degrees of freedom
## Multiple R-squared:  0.423,	Adjusted R-squared:  0.3749 
## F-statistic: 8.796 on 1 and 12 DF,  p-value: 0.01179
```

Or you can iterate here as well!


```r
models %>% 
  map(summary)
```

```
## $`4`
## 
## Call:
## lm(formula = mpg ~ wt, data = .)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -4.1513 -1.9795 -0.6272  1.9299  5.2523 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   39.571      4.347   9.104 7.77e-06 ***
## wt            -5.647      1.850  -3.052   0.0137 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 3.332 on 9 degrees of freedom
## Multiple R-squared:  0.5086,	Adjusted R-squared:  0.454 
## F-statistic: 9.316 on 1 and 9 DF,  p-value: 0.01374
## 
## 
## $`6`
## 
## Call:
## lm(formula = mpg ~ wt, data = .)
## 
## Residuals:
##      Mazda RX4  Mazda RX4 Wag Hornet 4 Drive        Valiant       Merc 280 
##        -0.1250         0.5840         1.9292        -0.6897         0.3547 
##      Merc 280C   Ferrari Dino 
##        -1.0453        -1.0080 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)   
## (Intercept)   28.409      4.184   6.789  0.00105 **
## wt            -2.780      1.335  -2.083  0.09176 . 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1.165 on 5 degrees of freedom
## Multiple R-squared:  0.4645,	Adjusted R-squared:  0.3574 
## F-statistic: 4.337 on 1 and 5 DF,  p-value: 0.09176
## 
## 
## $`8`
## 
## Call:
## lm(formula = mpg ~ wt, data = .)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -2.1491 -1.4664 -0.8458  1.5711  3.7619 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  23.8680     3.0055   7.942 4.05e-06 ***
## wt           -2.1924     0.7392  -2.966   0.0118 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.024 on 12 degrees of freedom
## Multiple R-squared:  0.423,	Adjusted R-squared:  0.3749 
## F-statistic: 8.796 on 1 and 12 DF,  p-value: 0.01179
```

You can also pull out specific information from your summary.


```r
models %>% 
  map(summary) %>% 
  map_dbl("r.squared")
```

```
##         4         6         8 
## 0.5086326 0.4645102 0.4229655
```

```r
models %>% 
  map(summary) %>% 
  map("residuals")
```

```
## $`4`
##     Datsun 710      Merc 240D       Merc 230       Fiat 128    Honda Civic 
##    -3.67009741     2.84281457     1.01693356     5.25225956    -0.05125022 
## Toyota Corolla  Toyota Corona      Fiat X1-9  Porsche 914-2   Lotus Europa 
##     4.69109534    -4.15127874    -1.34420213    -1.48656195    -0.62724679 
##     Volvo 142E 
##    -2.47246579 
## 
## $`6`
##      Mazda RX4  Mazda RX4 Wag Hornet 4 Drive        Valiant       Merc 280 
##     -0.1249670      0.5839601      1.9291961     -0.6896780      0.3547199 
##      Merc 280C   Ferrari Dino 
##     -1.0452801     -1.0079511 
## 
## $`8`
##   Hornet Sportabout          Duster 360          Merc 450SE 
##           2.3739574          -1.7410257           1.4551933 
##          Merc 450SL         Merc 450SLC  Cadillac Fleetwood 
##           1.6097644          -0.3806137          -1.9577300 
## Lincoln Continental   Chrysler Imperial    Dodge Challenger 
##          -1.5762458           2.5505516          -0.6506476 
##         AMC Javelin          Camaro Z28    Pontiac Firebird 
##          -1.1370048          -2.1490674           3.7618948 
##      Ford Pantera L       Maserati Bora 
##          -1.1180008          -1.0410257
```

We can also map over multiple arguments using `map2()` and `pmap()`. Let's generate a new df with 5 random normals centered around different means.


```r
mu <- list(5,10,25,50,100)
mu.df <- mu %>% 
  map(rnorm, n = 10) 
str(mu.df)
```

```
## List of 5
##  $ : num [1:10] 5.96 6.73 4.27 5.87 5.96 ...
##  $ : num [1:10] 8.88 9.93 9.5 10.55 11.7 ...
##  $ : num [1:10] 26 25.5 25.3 25.1 24.6 ...
##  $ : num [1:10] 50.5 49.7 48.4 50 49.7 ...
##  $ : num [1:10] 99.7 100.5 101.8 100 98.9 ...
```

What if we also wanted to vary sd? We could just use `map2()` which iterates over 2 vectors in parallel.


```r
sigma <- list(1, 3, 5, 2, .5)
mu.sd.df <- map2(mu, sigma, rnorm, n = 10)
str(mu.sd.df)
```

```
## List of 5
##  $ : num [1:10] 6.23 6.15 6.97 5.99 5.6 ...
##  $ : num [1:10] 7.12 7.03 6.06 5.65 9.3 ...
##  $ : num [1:10] 26.8 25.4 30.3 28.8 19.7 ...
##  $ : num [1:10] 50.6 46.5 47 47.6 51.7 ...
##  $ : num [1:10] 100.3 100.9 99.5 100.7 100.3 ...
```
If you want to iterate over 3 or more arguments, then you can use `pmap()`. Let's vary the mean, sd, and the number of samples. `pmap()` takes a list of arguments.


```r
m <- list(1,3,5,7,9)
args1 <- list(m, mu, sigma)
args1 %>% 
  pmap(rnorm) %>% 
  str()
```

```
## List of 5
##  $ : num 5.92
##  $ : num [1:3] 7.99 12.39 9.3
##  $ : num [1:5] 21.2 22.8 30.2 17.6 30.1
##  $ : num [1:7] 51 47.8 50.1 47.8 49.2 ...
##  $ : num [1:9] 100.7 100 99.9 100.2 100.5 ...
```

```r
#arguments can be named as well to help clarify code
args2 <- list(mean = mu, sd = sigma, n = m)
args2 %>% 
  pmap(rnorm) %>% 
  str()
```

```
## List of 5
##  $ : num 4.38
##  $ : num [1:3] 9.7 8.92 11.01
##  $ : num [1:5] 17.6 24 27.8 18.4 28.9
##  $ : num [1:7] 50.3 46.6 48.9 48.4 47.8 ...
##  $ : num [1:9] 100 100.3 99.3 100 99.7 ...
```

```r
head(args1)
```

```
## [[1]]
## [[1]][[1]]
## [1] 1
## 
## [[1]][[2]]
## [1] 3
## 
## [[1]][[3]]
## [1] 5
## 
## [[1]][[4]]
## [1] 7
## 
## [[1]][[5]]
## [1] 9
## 
## 
## [[2]]
## [[2]][[1]]
## [1] 5
## 
## [[2]][[2]]
## [1] 10
## 
## [[2]][[3]]
## [1] 25
## 
## [[2]][[4]]
## [1] 50
## 
## [[2]][[5]]
## [1] 100
## 
## 
## [[3]]
## [[3]][[1]]
## [1] 1
## 
## [[3]][[2]]
## [1] 3
## 
## [[3]][[3]]
## [1] 5
## 
## [[3]][[4]]
## [1] 2
## 
## [[3]][[5]]
## [1] 0.5
```

```r
head(args2)
```

```
## $mean
## $mean[[1]]
## [1] 5
## 
## $mean[[2]]
## [1] 10
## 
## $mean[[3]]
## [1] 25
## 
## $mean[[4]]
## [1] 50
## 
## $mean[[5]]
## [1] 100
## 
## 
## $sd
## $sd[[1]]
## [1] 1
## 
## $sd[[2]]
## [1] 3
## 
## $sd[[3]]
## [1] 5
## 
## $sd[[4]]
## [1] 2
## 
## $sd[[5]]
## [1] 0.5
## 
## 
## $n
## $n[[1]]
## [1] 1
## 
## $n[[2]]
## [1] 3
## 
## $n[[3]]
## [1] 5
## 
## $n[[4]]
## [1] 7
## 
## $n[[5]]
## [1] 9
```

It may be an even better idea to save arguments to a data frame/tribble to keep things clean.


```r
params <- tibble(
  mean = unlist(mu),
  sd = unlist(sigma),
  n = unlist(m)
)
params
```

```
## # A tibble: 5 x 3
##    mean    sd     n
##   <dbl> <dbl> <dbl>
## 1     5   1       1
## 2    10   3       3
## 3    25   5       5
## 4    50   2       7
## 5   100   0.5     9
```

```r
params %>% 
  pmap(rnorm)
```

```
## [[1]]
## [1] 5.622602
## 
## [[2]]
## [1]  6.614666 12.018172  8.423588
## 
## [[3]]
## [1] 23.39542 20.31885 28.86876 26.60555 34.21429
## 
## [[4]]
## [1] 46.89838 53.89069 51.97102 51.89037 51.85242 50.75493 51.57053
## 
## [[5]]
## [1] 100.29427 100.74827  99.83460  99.77740 100.75862  99.63892  99.73319
## [8]  99.79789  99.52779
```

One last useful function is `walk()`. This is similar to `map()` but is used when you just want the action to iterate and don't necessarily care about the return value. This is helpful for plotting and saving items to file. Let's start with a simple example.


```r
x <- list(1, "a", 3)
x %>% 
  walk(print)
```

```
## [1] 1
## [1] "a"
## [1] 3
```

Let's generate a set of plots for each of the simple linear regression models we created earlier and save to a newly created "images" folder saved wherever you are now.


```r
#dir.create("images")
plots <- mtcars %>% 
  split(.$cyl) %>% 
  map(~ggplot(., aes(mpg, wt)) + geom_point())
paths <- stringr::str_c(names(plots), ".pdf") #generate strings of names

pwalk(list(paths, plots), ggsave, path = "Images/")
```

```
## Saving 7 x 5 in image
## Saving 7 x 5 in image
## Saving 7 x 5 in image
```


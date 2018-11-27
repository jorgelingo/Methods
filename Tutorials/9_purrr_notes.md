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
##          a      b       c      d
##      <dbl>  <dbl>   <dbl>  <dbl>
##  1 -0.947  -1.16  -0.898   0.755
##  2 -0.0105 -0.144 -0.701  -0.159
##  3 -0.196  -1.75  -0.943   0.870
##  4 -1.33    0.375  0.929  -0.542
##  5  0.349   1.13  -0.579   0.443
##  6  0.290   1.68  -1.10   -0.327
##  7 -0.677  -1.23   0.268   0.979
##  8 -0.265   2.19  -1.05   -1.49 
##  9  0.153   2.15   0.0868  0.372
## 10 -1.53   -1.03  -0.238   0.329
```

We may want to compute the `mean` for each column. This would be fine to do for each individual column.


```r
mean(df$a)
```

```
## [1] -0.4164798
```

```r
mean(df$b)
```

```
## [1] 0.2191901
```

```r
mean(df$c)
```

```
## [1] -0.4223373
```

```r
mean(df$d)
```

```
## [1] 0.1233153
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
## [1] -0.4164798  0.2191901 -0.4223373  0.1233153
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
##  $ : num [1:64] 1.782 0.124 0.758 -0.913 0.402 ...
##  $ : num [1:7] 5.21 4.34 5.85 5.18 4.13 ...
##  $ : num [1:76] 10.04 10.27 9.66 10.67 9.39 ...
```

```r
output <- unlist(list.output) #combine together
print(output)
```

```
##   [1]  1.78243605  0.12365888  0.75755775 -0.91285644  0.40181132
##   [6]  0.14834383  1.60546457  0.08019467 -0.94246136  0.31256410
##  [11]  0.55405952 -1.20841697  1.39008340  0.20511320 -1.71297179
##  [16]  0.55210548  0.98716200  0.08314807 -1.05593838 -1.99871673
##  [21]  1.57710550  0.22965609  0.77198590  1.14538687 -0.63110020
##  [26]  0.34676825 -0.36809771 -0.13284421  0.08406078  0.16760110
##  [31] -1.68091153 -0.33020352  1.23762066  0.33533362  0.22153448
##  [36]  0.06638130 -0.87967035 -1.35066504  2.56766183  0.22170807
##  [41]  1.19635356  0.73534641 -0.07631535  0.04468284  0.45027752
##  [46] -0.28673338 -1.21213549 -0.57778324 -1.59777727  0.45291461
##  [51] -1.56916378 -0.48256751  0.41575308 -0.70227807  1.56716803
##  [56] -1.31757471  0.02328944 -1.35401964  1.69850071  0.36445800
##  [61]  0.54705133 -0.93667869 -0.79358415 -0.14022846  5.21203305
##  [66]  4.34317321  5.85210761  5.18045488  4.12802998  4.27342339
##  [71]  4.64123101 10.03801353 10.26728300  9.66237176 10.66661766
##  [76]  9.38521745 11.17994698  8.97618366 10.17589031  8.61864853
##  [81]  8.26290010  8.69602160 10.30102372  7.97943079  8.74541334
##  [86] 10.35169573 10.14741978 11.57142151  9.87667696 10.18086663
##  [91]  9.50534980  9.80279219 10.24968979  8.41806283 10.59400778
##  [96] 11.50610680  8.64983836 10.51308966  9.22671737  9.43914163
## [101]  9.89561069 10.78761929  9.12322070 12.13384953 10.31038221
## [106]  9.66505751  9.12171840 12.15425071  9.70477369  9.37444184
## [111] 10.59649618  9.75826670 10.47469235  9.65150740 11.63164298
## [116] 11.77768950 10.53641637  8.68623738  9.48784296  9.73234966
## [121] 11.43788439 10.80841450  8.24728398  9.32459025  9.82749418
## [126]  8.36697117  8.78162506  8.95999688 11.19557009 11.34562589
## [131]  9.16987439 10.52930479  9.61052711 10.54977320  8.32225655
## [136]  8.09686510  9.27013504  9.87521400  9.23444723 10.50825371
## [141]  9.94398971  9.11049328 10.11534519 10.51520024  8.21367470
## [146] 10.29419528 10.65451891
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
## [1] 3
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
##          a          b          c          d 
## -0.4164798  0.2191901 -0.4223373  0.1233153
```

```r
map_dbl(df, median)
```

```
##          a          b          c          d 
## -0.2307253  0.1154158 -0.6400018  0.3504480
```

```r
map_dbl(df, sd)
```

```
##         a         b         c         d 
## 0.6725684 1.4973359 0.6705874 0.7614359
```

This is pretty simple to understand compared to *for* loops and is less code intensive (less required). It gets even easier to read when combined with the pipe operator.


```r
df %>% 
  map_dbl(mean)
```

```
##          a          b          c          d 
## -0.4164798  0.2191901 -0.4223373  0.1233153
```

You can also pass on additional arguments in `map*()` functions, which can be useful


```r
df %>% 
  map_dbl(mean, trim = 0.5)
```

```
##          a          b          c          d 
## -0.2307253  0.1154158 -0.6400018  0.3504480
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
##  $ : num [1:10] 3.99 3.04 3.26 5.11 3.35 ...
##  $ : num [1:10] 7.9 10.27 9.35 10.19 11.7 ...
##  $ : num [1:10] 23.4 23.1 24.4 25.2 23.7 ...
##  $ : num [1:10] 50.5 48.1 49.5 49.9 48.8 ...
##  $ : num [1:10] 100.3 99.5 100.2 99.4 98.2 ...
```

What if we also wanted to vary sd? We could just use `map2()` which iterates over 2 vectors in parallel.


```r
sigma <- list(1, 3, 5, 2, .5)
mu.sd.df <- map2(mu, sigma, rnorm, n = 10)
str(mu.sd.df)
```

```
## List of 5
##  $ : num [1:10] 4.39 5.15 5.25 5.85 5.19 ...
##  $ : num [1:10] 5.69 15.64 13.56 9.45 11.04 ...
##  $ : num [1:10] 22.1 25.8 19.9 23.7 14 ...
##  $ : num [1:10] 51.8 49.4 50.4 52.5 53.9 ...
##  $ : num [1:10] 99.1 100.7 99.5 100.6 100.1 ...
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
##  $ : num 4.97
##  $ : num [1:3] 8.1 10.61 5.71
##  $ : num [1:5] 29.9 36.7 21.2 27.9 20
##  $ : num [1:7] 48.7 50.2 51 52.9 48.4 ...
##  $ : num [1:9] 100.8 100.2 100.2 99.7 99.6 ...
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
##  $ : num 4.06
##  $ : num [1:3] 9.69 10.6 4.05
##  $ : num [1:5] 23.2 21.9 23.4 15 23.8
##  $ : num [1:7] 46.6 48.5 49.4 50.9 50 ...
##  $ : num [1:9] 99.5 100.1 99.8 100.8 99.8 ...
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
## [1] 4.95922
## 
## [[2]]
## [1] 9.574396 8.053715 4.820682
## 
## [[3]]
## [1] 19.45811 25.98377 29.64689 27.60200 30.93926
## 
## [[4]]
## [1] 50.82143 48.02567 47.53421 50.24934 51.05979 52.01367 50.32298
## 
## [[5]]
## [1]  98.95439  99.77792 100.15649  99.75290  99.77758 100.74639  99.76024
## [8]  99.76460 100.19940
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


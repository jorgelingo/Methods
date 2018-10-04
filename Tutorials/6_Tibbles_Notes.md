---
title: "6_Tibbles"
author: "Jorge Valdes"
date: "10/3/2018"
output: 
  html_document: 
    keep_md: yes
---

# Introduction
This week we will work with tibbles and we will begin to demonstrate some simple statistical tests that we can conduct with R. Stats tests will necessarily need to be something that you refer to on your own as this is not a course on statistics. 

## Tibbles and data importing
Tibbles are the specific type of data frame that are used with the `tidyverse`. They have some properties that are preferable to `data.frame`s, which are base R's version of organizing data tables. One advantage is that tibbles by default only print the first 10 rows of data, so that we don't overwhelm what is printed in the Console. 

Up to now, we have mainly be using `read.csv()` to import a data frame and then changing into a tibble by using `as_tibble()`.


```r
library(tidyverse)
```

```
## ── Attaching packages ────────────────────── tidyverse 1.2.1 ──
```

```
## ✔ ggplot2 2.2.1     ✔ purrr   0.2.4
## ✔ tibble  1.4.2     ✔ dplyr   0.7.4
## ✔ tidyr   0.8.0     ✔ stringr 1.3.1
## ✔ readr   1.1.1     ✔ forcats 0.3.0
```

```
## ── Conflicts ───────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
```

```r
sample <- read.csv("data/sample_data.csv")
head(sample)
```

```
##   RECORDING_SESSION_LABEL aux_type response_accuracy count  prop Group
## 1                   118cc        E           correct    14 87.50  late
## 2                   118cc        E         incorrect     2 12.50  late
## 3                   118cc        H           correct    15 93.75  late
## 4                   118cc        H         incorrect     1  6.25  late
## 5                    11cc        E           correct    13 81.25  late
## 6                    11cc        E         incorrect     3 18.75  late
```

```r
sample2 <- as_tibble(sample)
sample2
```

```
## # A tibble: 52 x 6
##    RECORDING_SESSION_LABEL aux_type response_accuracy count  prop Group
##    <fct>                   <fct>    <fct>             <int> <dbl> <fct>
##  1 118cc                   E        correct              14 87.5  late 
##  2 118cc                   E        incorrect             2 12.5  late 
##  3 118cc                   H        correct              15 93.8  late 
##  4 118cc                   H        incorrect             1  6.25 late 
##  5 11cc                    E        correct              13 81.2  late 
##  6 11cc                    E        incorrect             3 18.8  late 
##  7 11cc                    H        correct              14 87.5  late 
##  8 11cc                    H        incorrect             2 12.5  late 
##  9 139cc                   E        correct              14 87.5  late 
## 10 139cc                   E        incorrect             2 12.5  late 
## # ... with 42 more rows
```

We can import data as a tibble by using `read_csv()` as a command.

```r
sample3 <- read_csv("data/sample_data.csv")
```

```
## Parsed with column specification:
## cols(
##   RECORDING_SESSION_LABEL = col_character(),
##   aux_type = col_character(),
##   response_accuracy = col_character(),
##   count = col_integer(),
##   prop = col_double(),
##   Group = col_character()
## )
```

```r
sample3
```

```
## # A tibble: 52 x 6
##    RECORDING_SESSION_LABEL aux_type response_accuracy count  prop Group
##    <chr>                   <chr>    <chr>             <int> <dbl> <chr>
##  1 118cc                   E        correct              14 87.5  late 
##  2 118cc                   E        incorrect             2 12.5  late 
##  3 118cc                   H        correct              15 93.8  late 
##  4 118cc                   H        incorrect             1  6.25 late 
##  5 11cc                    E        correct              13 81.2  late 
##  6 11cc                    E        incorrect             3 18.8  late 
##  7 11cc                    H        correct              14 87.5  late 
##  8 11cc                    H        incorrect             2 12.5  late 
##  9 139cc                   E        correct              14 87.5  late 
## 10 139cc                   E        incorrect             2 12.5  late 
## # ... with 42 more rows
```

If you need to import a tab-delimited .txt file, you can use `read_tsv()` and if you need more flexibility, you can generally use `read_delim()`.

One nice feature of data importing with `read_csv()` is that R will tell you how it understands the column classes. Note: there are some differences with how tibbles treat text columns but it is not a major problem. If you need to change how R is default understanding a variable class, you can use `parse_*()` functions. There are several kinds:

  * `parse_logical()`
  * `parse_integer()`
  * `parse_double()`
  * `parse_character()`
  * `parse_factor()`

Currently, our variable column "aux_type" is encoded as a character, so if I ask R to give me a summary, we won't see how many of each level we have:


```r
summary(sample3)
```

```
##  RECORDING_SESSION_LABEL   aux_type         response_accuracy 
##  Length:52               Length:52          Length:52         
##  Class :character        Class :character   Class :character  
##  Mode  :character        Mode  :character   Mode  :character  
##                                                               
##                                                               
##                                                               
##      count            prop           Group          
##  Min.   : 0.00   Min.   :  0.00   Length:52         
##  1st Qu.: 1.75   1st Qu.: 10.94   Class :character  
##  Median : 8.00   Median : 50.00   Mode  :character  
##  Mean   : 8.00   Mean   : 50.00                     
##  3rd Qu.:14.25   3rd Qu.: 89.06                     
##  Max.   :16.00   Max.   :100.00
```

We can give the number of levels that we have in the column and ask R to check whether we have the right kinds of levels. This is especially helpful for catching typos when coding variables.


```r
aux <- c("E","H")
parse_factor(sample3$aux_type, levels = aux)
```

```
##  [1] E E H H E E H H E E H H E E H H E E H H E E H H E E H H E E H H E E H
## [36] H E E H H E E H H E E H H E E H H
## Levels: E H
```

We can also save directly to the same column to change the variable to a factor and then try summary again.


```r
sample3$aux_type <- parse_factor(sample3$aux_type, levels = aux)
summary(sample3)
```

```
##  RECORDING_SESSION_LABEL aux_type response_accuracy      count      
##  Length:52               E:26     Length:52          Min.   : 0.00  
##  Class :character        H:26     Class :character   1st Qu.: 1.75  
##  Mode  :character                 Mode  :character   Median : 8.00  
##                                                      Mean   : 8.00  
##                                                      3rd Qu.:14.25  
##                                                      Max.   :16.00  
##       prop           Group          
##  Min.   :  0.00   Length:52         
##  1st Qu.: 10.94   Class :character  
##  Median : 50.00   Mode  :character  
##  Mean   : 50.00                     
##  3rd Qu.: 89.06                     
##  Max.   :100.00
```

We've already seen some issues with character encoding. We are assuming that data files are saved with UTF-8 encoding but this is not always the case. If this is not the case, then we may have to do some reading to figure out what kind of encoding is used. 


```r
reading <- read_csv("data/L2CS_Word8_Ps11_20.csv")
```

```
## Parsed with column specification:
## cols(
##   .default = col_character(),
##   RECORDING_SESSION_LABEL = col_integer(),
##   TRIAL_INDEX = col_integer(),
##   CURRENT_FIX_ADJUSTED = col_logical(),
##   CURRENT_FIX_END = col_integer(),
##   CURRENT_FIX_IS_RT_END = col_logical(),
##   CURRENT_FIX_MSG_COUNT = col_integer(),
##   CURRENT_FIX_PUPIL = col_integer(),
##   CURRENT_FIX_START = col_integer(),
##   CURRENT_FIX_X = col_double(),
##   CURRENT_FIX_X_RESOLUTION = col_double(),
##   CURRENT_FIX_Y = col_double(),
##   CURRENT_FIX_Y_RESOLUTION = col_double(),
##   IP_END_EVENT_MATCHED = col_logical(),
##   IP_END_TIME = col_integer(),
##   IP_START_EVENT_MATCHED = col_logical(),
##   IP_START_TIME = col_integer(),
##   PREVIOUS_FIX_ANGLE = col_double(),
##   PREVIOUS_FIX_DISTANCE = col_double(),
##   PREVIOUS_FIX_DURATION = col_integer(),
##   PREVIOUS_FIX_END = col_integer()
##   # ... with 32 more columns
## )
```

```
## See spec(...) for full column specifications.
```

```r
reading2 <-  reading %>% 
  select(file:TOTAL_DURATION)
reading2
```

```
## # A tibble: 692 x 11
##    file   grammaticality practice question   response_accura… sentence    
##    <chr>  <chr>          <chr>    <chr>      <chr>            <chr>       
##  1 File … A              1        Is the se… correct          Sandra y Mi…
##  2 File … G              1        Is the se… correct          El director…
##  3 File … G              1        Is the se… incorrect        El investig…
##  4 File … A              1        Is the se… correct          El abogado …
##  5 File … G              1        Is the se… correct          El doctor l…
##  6 File … A              2        Is the se… incorrect        Lidia y sus…
##  7 File … A              2        Is the se… incorrect        Rosa y Fran…
##  8 File … G              2        Is the se… correct          Ana había p…
##  9 File … G              2        Is the se… correct          Chris y Sar…
## 10 File … G              4        Is the se… correct          La secretar…
## # ... with 682 more rows, and 5 more variables: sentence_type <chr>,
## #   switch_type <chr>, GAZE_DURATION <int>,
## #   REGRESSION_PATH_DURATION <int>, TOTAL_DURATION <int>
```

```r
head(reading2$sentence)
```

```
## [1] "Sandra y Miriam había preparado la sopa de vegetales before their roommate arrived."         
## [2] "El director de la escuela notó que los estudiantes had broken the bookshelf in the library." 
## [3] "El investigador estará cuestionando a los sospechosos about the events leading to the crime."
## [4] "El abogado estaba revisando el documentos mientras his assistant answered phone calls."      
## [5] "El doctor le está diciendo al paciente que he should stay in bed for another week."          
## [6] "Lidia y sus amiga estaban comprando the books for the chemistry class."
```

```r
head(parse_character(reading2$sentence, locale = locale(encoding = "Latin1")))
```

```
## [1] "Sandra y Miriam habÃ­a preparado la sopa de vegetales before their roommate arrived."         
## [2] "El director de la escuela notÃ³ que los estudiantes had broken the bookshelf in the library." 
## [3] "El investigador estarÃ¡ cuestionando a los sospechosos about the events leading to the crime."
## [4] "El abogado estaba revisando el documentos mientras his assistant answered phone calls."       
## [5] "El doctor le estÃ¡ diciendo al paciente que he should stay in bed for another week."          
## [6] "Lidia y sus amiga estaban comprando the books for the chemistry class."
```

# Inferential Statistics

Let's now get into practicing some very basic stats. We'll work with the sample dataset again.


```r
sample3
```

```
## # A tibble: 52 x 6
##    RECORDING_SESSION_LABEL aux_type response_accuracy count  prop Group
##    <chr>                   <fct>    <chr>             <int> <dbl> <chr>
##  1 118cc                   E        correct              14 87.5  late 
##  2 118cc                   E        incorrect             2 12.5  late 
##  3 118cc                   H        correct              15 93.8  late 
##  4 118cc                   H        incorrect             1  6.25 late 
##  5 11cc                    E        correct              13 81.2  late 
##  6 11cc                    E        incorrect             3 18.8  late 
##  7 11cc                    H        correct              14 87.5  late 
##  8 11cc                    H        incorrect             2 12.5  late 
##  9 139cc                   E        correct              14 87.5  late 
## 10 139cc                   E        incorrect             2 12.5  late 
## # ... with 42 more rows
```

Let's say that we only wanted to work with the "correct" rows and wanted to compare the mean correct items between "estar" and "haber". This means that we are testing two means from the same group of people, so we will need to conduct a **paired t-test**. 

First, let's limit to only correct items.


```r
sample_correct <- sample3 %>% 
  filter(response_accuracy == "correct")
sample_correct
```

```
## # A tibble: 26 x 6
##    RECORDING_SESSION_LABEL aux_type response_accuracy count  prop Group
##    <chr>                   <fct>    <chr>             <int> <dbl> <chr>
##  1 118cc                   E        correct              14  87.5 late 
##  2 118cc                   H        correct              15  93.8 late 
##  3 11cc                    E        correct              13  81.2 late 
##  4 11cc                    H        correct              14  87.5 late 
##  5 139cc                   E        correct              14  87.5 late 
##  6 139cc                   H        correct              15  93.8 late 
##  7 14cc                    E        correct              10  62.5 late 
##  8 14cc                    H        correct              13  81.2 late 
##  9 15cc                    E        correct              14  87.5 late 
## 10 15cc                    H        correct              15  93.8 late 
## # ... with 16 more rows
```

Now, let's take a look at the documentation for t-tests.

```r
?t.test()
```

There are several default values. The main one that we want to override is the paired argument, which is currently set to FALSE. We want to change that to TRUE. Notice that there are 2 ways to specify t-tests. where each vector is specified in separate columns or using the "formula" method.


```r
t.test(sample_correct$count[sample_correct$aux_type == "E"], sample_correct$count[sample_correct$aux_type == "H"], paired = TRUE)
```

```
## 
## 	Paired t-test
## 
## data:  sample_correct$count[sample_correct$aux_type == "E"] and sample_correct$count[sample_correct$aux_type == "H"]
## t = -1.2888, df = 12, p-value = 0.2218
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -1.4487679  0.3718448
## sample estimates:
## mean of the differences 
##              -0.5384615
```

```r
#formula method
t.test(count ~ aux_type, data = sample_correct, paired = TRUE)
```

```
## 
## 	Paired t-test
## 
## data:  count by aux_type
## t = -1.2888, df = 12, p-value = 0.2218
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -1.4487679  0.3718448
## sample estimates:
## mean of the differences 
##              -0.5384615
```

```r
##just to see the difference, what happens if we don't changed paired argument?
t.test(count ~ aux_type, data = sample_correct)
```

```
## 
## 	Welch Two Sample t-test
## 
## data:  count by aux_type
## t = -0.9218, df = 17.989, p-value = 0.3688
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -1.7657523  0.6888292
## sample estimates:
## mean in group E mean in group H 
##        14.00000        14.53846
```

The below chunk is on demonstrating where means in the statistical outputs come from. In the case of 2-samples t-tests: it is the mean for each group. In the case of paired t-tests, then the mean is the difference between the two groups. 


```r
sample2
```

```
## # A tibble: 52 x 6
##    RECORDING_SESSION_LABEL aux_type response_accuracy count  prop Group
##    <fct>                   <fct>    <fct>             <int> <dbl> <fct>
##  1 118cc                   E        correct              14 87.5  late 
##  2 118cc                   E        incorrect             2 12.5  late 
##  3 118cc                   H        correct              15 93.8  late 
##  4 118cc                   H        incorrect             1  6.25 late 
##  5 11cc                    E        correct              13 81.2  late 
##  6 11cc                    E        incorrect             3 18.8  late 
##  7 11cc                    H        correct              14 87.5  late 
##  8 11cc                    H        incorrect             2 12.5  late 
##  9 139cc                   E        correct              14 87.5  late 
## 10 139cc                   E        incorrect             2 12.5  late 
## # ... with 42 more rows
```

```r
mean(sample2$count[sample2$aux_type == "E" & sample2$response_accuracy == "correct"])
```

```
## [1] 14
```

```r
mean(sample2$count[sample2$aux_type == "H"& sample2$response_accuracy == "correct"])
```

```
## [1] 14.53846
```


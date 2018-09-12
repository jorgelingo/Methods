---
title: "3_dplyr_notes"
author: "Jorge Valdes"
date: "9/12/2018"
output: 
  html_document: 
    keep_md: yes
---

# Introduction

These notes are primarily built from Chapter 3 in _R for Data Science_ as well as some great slides found [here][https://ismayc.github.io/talks/ness-infer/slide_deck.html#1].

Last week we worked on data visualization, and we learned that `ggplot2` works with a _grammar of graphics_ where we first specify a data source (and potentially global aesthetics) and then "layer" each geometric object, facetting, statistical transormation, or other options. As long as we have our working directory set to our class folder, then we should be able to generate the following graph once we have imported our data (I'm going to hide the ggplot code to test whether you can reproduce this graph).


```r
library(tidyverse)
```

```
## ── Attaching packages ─────────────────────────────────────────────────── tidyverse 1.2.1 ──
```

```
## ✔ ggplot2 2.2.1     ✔ purrr   0.2.4
## ✔ tibble  1.4.2     ✔ dplyr   0.7.4
## ✔ tidyr   0.8.0     ✔ stringr 1.3.1
## ✔ readr   1.1.1     ✔ forcats 0.3.0
```

```
## ── Conflicts ────────────────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
```

```r
cs_data <- read.csv("data/CSLex_Subset.csv")
head(cs_data)
```

```
##   Subject Condition   Gender    Type Birth Time.100 Target Distractor
## 1      15         1 Feminine Control Latin       50    364        233
## 2      15         1 Feminine Control Latin      150    336        267
## 3      15         1 Feminine Control Latin      250    507         70
## 4      15         1 Feminine Control Latin      350    597        182
## 5      15         1 Feminine Control Latin      450    615        286
## 6      15         1 Feminine Control Latin      550    657        263
##   Other Saccade Track_Loss Bins
## 1   174     161         68 1000
## 2   207     177         13 1000
## 3   228     101         94 1000
## 4    84     137          0 1000
## 5    24      75          0 1000
## 6     0      80          0 1000
```

And now a graph:

```
## `geom_smooth()` using method = 'loess'
```

![](3_dplyrNotes_files/figure-html/graph-1.png)<!-- -->

The code for the above plot is:


```r
ggplot(data = cs_data, aes(x = Time.100, y = Target, color = Gender)) + #data source and global aesthetics
  geom_point() + #add scatterplot
  geom_smooth(aes(linetype = Type)) + #add predictor and linetype aesthetic
  facet_wrap(~Birth) #facet by Birth
```

However, the data had to be in the right format in order for the graph command to work. Rarely is this the case. We might have to:

* filter/sort our data
* change the order of rows
* pick certain variables to include
* create new variables
* summarize our values
* collapse multiple columns into one or split one into multiple columns

All of the above steps are common transformations. This week we'll focus on the first 5 (the 6th we'll work on in a different week) using hte `dplyr` package, which is included in the `tidyverse`. The above actions can be rewritten as `dplyr` functions:

* `filter()` picks observations by their values
* `arrange()` reorders rows
* `select()` picks variables (columns) by their names
* `mutate()` can create new variables 
* `summarize()` summarizes multiple values into singular ones

Often times, we will want to combine these actions with a grouping function, which in `tidyverse` is the `group_by()` command. Think of these functions as verbs with a similar argument structure. First, you'll **specify a dataset**, then you will **perform the action on a set of arguments**, and the action will **return a new dataset**. These functions can be chained together. 

I consider this package to be a **core** feature of the power of the `tidyverse` and R Studio in helping young researchers become independent. Think of filtering, sorting, and pivot tables (all functions in Excel) on steroids. 

## `filter()`

During our first week, we learned two ways of subsetting data: 1) the indexing method and 2) the `subset()` function. This will be a newer, simpler way to do it. First, just to make our code output easier to manage, we are going to coerce our data frame into a _tibble_. Remember this is a special kind of data frame that is used in the `tidyverse`. We will learn much more about tibbles in a couple of weeks but for now, it's usefult to know that it only prints the first 10 rows of data instead of the entire dataset. 


```r
cs_lex <- as_tibble(cs_data)
cs_lex
```

```
## # A tibble: 1,344 x 12
##    Subject Condition Gender  Type   Birth Time.100 Target Distractor Other
##      <int>     <int> <fct>   <fct>  <fct>    <int>  <int>      <int> <int>
##  1      15         1 Femini… Contr… Latin       50    364        233   174
##  2      15         1 Femini… Contr… Latin      150    336        267   207
##  3      15         1 Femini… Contr… Latin      250    507         70   228
##  4      15         1 Femini… Contr… Latin      350    597        182    84
##  5      15         1 Femini… Contr… Latin      450    615        286    24
##  6      15         1 Femini… Contr… Latin      550    657        263     0
##  7      15         1 Femini… Contr… Latin      650    719         29    46
##  8      15         1 Femini… Contr… Latin      750    802          0   100
##  9      15         2 Femini… Match  Latin       50    295        269   280
## 10      15         2 Femini… Match  Latin      150    400        283   281
## # ... with 1,334 more rows, and 3 more variables: Saccade <int>,
## #   Track_Loss <int>, Bins <int>
```

With `filter()`, the first argument is the dataset and the next arguments are the conditionals by which you want to filter the data. We can for example only look at participants born in the US.


```r
filter(cs_lex, Birth == "US")
```

```
## # A tibble: 480 x 12
##    Subject Condition Gender  Type   Birth Time.100 Target Distractor Other
##      <int>     <int> <fct>   <fct>  <fct>    <int>  <int>      <int> <int>
##  1      54         1 Femini… Contr… US          50    371        455   121
##  2      54         1 Femini… Contr… US         150    400        400   200
##  3      54         1 Femini… Contr… US         250    538        241    60
##  4      54         1 Femini… Contr… US         350    762         82    63
##  5      54         1 Femini… Contr… US         450    771         93    41
##  6      54         1 Femini… Contr… US         550    896          0     0
##  7      54         1 Femini… Contr… US         650    978          0     0
##  8      54         1 Femini… Contr… US         750    973          0     0
##  9      54         2 Femini… Match  US          50    100        630    76
## 10      54         2 Femini… Match  US         150    237        560    64
## # ... with 470 more rows, and 3 more variables: Saccade <int>,
## #   Track_Loss <int>, Bins <int>
```

We can combine and filter by US birth as well as only feminine conditions.


```r
filter(cs_lex, Birth == "US", Gender == "Feminine")
```

```
## # A tibble: 240 x 12
##    Subject Condition Gender  Type   Birth Time.100 Target Distractor Other
##      <int>     <int> <fct>   <fct>  <fct>    <int>  <int>      <int> <int>
##  1      54         1 Femini… Contr… US          50    371        455   121
##  2      54         1 Femini… Contr… US         150    400        400   200
##  3      54         1 Femini… Contr… US         250    538        241    60
##  4      54         1 Femini… Contr… US         350    762         82    63
##  5      54         1 Femini… Contr… US         450    771         93    41
##  6      54         1 Femini… Contr… US         550    896          0     0
##  7      54         1 Femini… Contr… US         650    978          0     0
##  8      54         1 Femini… Contr… US         750    973          0     0
##  9      54         2 Femini… Match  US          50    100        630    76
## 10      54         2 Femini… Match  US         150    237        560    64
## # ... with 230 more rows, and 3 more variables: Saccade <int>,
## #   Track_Loss <int>, Bins <int>
```

### Comparisons
This function makes life easy if we want to select specific values but sometimes we need to add different kinds of comparison operators. Just remember that some of the comparisons require numbers (`>, <, <=, >=`), and always remember: **`==` is for values where `=` is for arguments**. We also have a special symbol for _not equals_: `!=`. 


```r
filter(cs_lex, Time.100 > 350)
```

```
## # A tibble: 672 x 12
##    Subject Condition Gender   Type  Birth Time.100 Target Distractor Other
##      <int>     <int> <fct>    <fct> <fct>    <int>  <int>      <int> <int>
##  1      15         1 Feminine Cont… Latin      450    615        286    24
##  2      15         1 Feminine Cont… Latin      550    657        263     0
##  3      15         1 Feminine Cont… Latin      650    719         29    46
##  4      15         1 Feminine Cont… Latin      750    802          0   100
##  5      15         2 Feminine Match Latin      450    283         81   445
##  6      15         2 Feminine Match Latin      550    372         34   438
##  7      15         2 Feminine Match Latin      650    562          0   321
##  8      15         2 Feminine Match Latin      750    636          0   259
##  9      15         4 Masculi… Cont… Latin      450    300        250   400
## 10      15         4 Masculi… Cont… Latin      550    339         71   433
## # ... with 662 more rows, and 3 more variables: Saccade <int>,
## #   Track_Loss <int>, Bins <int>
```

```r
filter(cs_lex, Gender != "Feminine")
```

```
## # A tibble: 672 x 12
##    Subject Condition Gender   Type  Birth Time.100 Target Distractor Other
##      <int>     <int> <fct>    <fct> <fct>    <int>  <int>      <int> <int>
##  1      15         4 Masculi… Cont… Latin       50    232        333   175
##  2      15         4 Masculi… Cont… Latin      150    400        358   205
##  3      15         4 Masculi… Cont… Latin      250    376        299   126
##  4      15         4 Masculi… Cont… Latin      350    286        347   246
##  5      15         4 Masculi… Cont… Latin      450    300        250   400
##  6      15         4 Masculi… Cont… Latin      550    339         71   433
##  7      15         4 Masculi… Cont… Latin      650    547         91   280
##  8      15         4 Masculi… Cont… Latin      750    729        100    76
##  9      15         5 Masculi… Match Latin       50    223        617     0
## 10      15         5 Masculi… Match Latin      150    332        548     0
## # ... with 662 more rows, and 3 more variables: Saccade <int>,
## #   Track_Loss <int>, Bins <int>
```

### Logical Operators
There are several **Boolean** operators that we can use to combine with our conditions. If we only use the comma to separate conditions, then the operation is implicity "and". This figure from the chapter is really helpful for visualizing the logical operators. 

![Figure 5.1][Images/Figure5_1.png]



---
title: "R Notebook"
author: "Jorge Valdés Kroff"
date: "8/29/2018"
output: 
  html_document: 
    keep_md: yes
---

This is an [R Markdown](http://rmarkdown.rstudio.com) document. When you execute code within the document, the results appear beneath the code. 

Try executing this chunk by clicking the *Run* button within the chunk or by placing your cursor inside it and pressing *Cmd+Shift+Enter*. 


```r
plot(cars)
```

![](1_MarkdownNotes_files/figure-html/unnamed-chunk-1-1.png)<!-- -->

Add a new chunk by clicking the *Insert Chunk* button on the toolbar or by pressing *Cmd/CTRL+Option/ALT+I*.

When you save the notebook, an HTML file containing the code and output will be saved alongside it (If using an R notebook, click the *Preview* button or press *Cmd/CTRL+Shift+K* to preview the HTML file.The preview shows you a rendered HTML copy of the contents of the editor. Consequently, unlike *Knit*, *Preview* does not run any R code chunks. Instead, the output of the chunk when it was last run in the editor is displayed.)

#Tutorial
Here are some text formatting aspects that can be done within the text portion of the notebook:

##Headings
# 1st Level Header
## 2nd Level Header
### 3rd Level Header

##Formatting
*italic* or _italic_
**bold** or __bold__
`code`
superscript^2^ 
subscript~2~

##Lists
* Bulleted Item 1
* Item 2
  + Item 2a
  + Item 2b
  
1. Numbered Item 1
1. Item 2. (numbering is automatic)

##Links and images
For links:
<http://docs.ggplot2.org>

[ggplot2 documentation](https:/docs.ggplot2.org)

![Valparaiso](Images/Paseo21mayo.jpg)

##Tables

First | Second
------ | ------
a | b
c | d

##Other
You can write in-line equations using $\LaTeX$ formatting: $SEM = \sigma/\sqrt n$. 

Create a horizontal rule:

***

Create a block quote:

> Important stuff was said



##Inserting chunks
Use **CMD/CTRL+OPTION/ALT+i** to insert a code chunk:

```r
a <- 3
b <- 4
c <- a*b
c
```

```
## [1] 12
```
You can give an R chunk a name by adding above within `{}` after the **r**.
Use **CMD/CTRL+SHIFT+ENTER** to run code chunk. Results will appear after chunk in notebooks!

##Basic Shortcuts Summary
These are in my opinion, the most important shortcut keys to remember in R:
* **OPTION/ALT+-** creates the assignment operator `<-`
* **CMD/CTRL+SHIFT+m** creates the pipe operator `%>%` very important for `tidyverse`
* **CMD/CTRL+OPTION/ALT+i** inserts a code chunk
* **CMD/CTRL+SHIFT+ENTER** executes all code within a chunk


```r
library(tidyverse) #to load in tidyverse library
```

```
## ── Attaching packages ─────────────────────────────── tidyverse 1.2.1 ──
```

```
## ✔ ggplot2 2.2.1     ✔ purrr   0.2.4
## ✔ tibble  1.4.2     ✔ dplyr   0.7.4
## ✔ tidyr   0.8.0     ✔ stringr 1.3.1
## ✔ readr   1.1.1     ✔ forcats 0.3.0
```

```
## ── Conflicts ────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
```

```r
#create a tibble of fake data
fake_data <- tibble(
  Subject = rep(seq(1:10),2), #repeat a sequence from 1 to 10, twice
  Condition = c(rep("easy", 10), rep("hard", 10)), 
  Fake_RT = c(rnorm(10, mean = 250, sd = 2.5), rnorm(10, mean = 294, sd = 2.5))
)
print(fake_data)
```

```
## # A tibble: 20 x 3
##    Subject Condition Fake_RT
##      <int> <chr>       <dbl>
##  1       1 easy         251.
##  2       2 easy         251.
##  3       3 easy         250.
##  4       4 easy         247.
##  5       5 easy         245.
##  6       6 easy         251.
##  7       7 easy         246.
##  8       8 easy         249.
##  9       9 easy         254.
## 10      10 easy         248.
## 11       1 hard         293.
## 12       2 hard         299.
## 13       3 hard         291.
## 14       4 hard         293.
## 15       5 hard         292.
## 16       6 hard         297.
## 17       7 hard         301.
## 18       8 hard         295.
## 19       9 hard         293.
## 20      10 hard         289.
```
Now let's do something very simple with `tidyverse` in a code chunk. Let's summarize the *mean* and *sd* for our fake data by the levels of **Condition**:

```r
group_fake_data <- fake_data %>% 
  group_by(Condition) %>% 
  summarize(meanRT = mean(Fake_RT), sdRT = sd(Fake_RT))
print(group_fake_data)
```

```
## # A tibble: 2 x 3
##   Condition meanRT  sdRT
##   <chr>      <dbl> <dbl>
## 1 easy        249.  2.81
## 2 hard        294.  3.70
```
And let's end by running a paired t-test, since there are two data points per Participant, one for the *easy* condition and one for the *hard* condition.

```r
#formula method for paired t-test
t.test(Fake_RT ~ Condition, data = fake_data, paired = TRUE)
```

```
## 
## 	Paired t-test
## 
## data:  Fake_RT by Condition
## t = -30.544, df = 9, p-value = 2.116e-10
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -48.32464 -41.66012
## sample estimates:
## mean of the differences 
##               -44.99238
```

```r
#indexing method for paired t-test
t.test(fake_data$Fake_RT[fake_data$Condition == "easy"], fake_data$Fake_RT[fake_data$Condition == "hard"], paired = TRUE)
```

```
## 
## 	Paired t-test
## 
## data:  fake_data$Fake_RT[fake_data$Condition == "easy"] and fake_data$Fake_RT[fake_data$Condition == "hard"]
## t = -30.544, df = 9, p-value = 2.116e-10
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -48.32464 -41.66012
## sample estimates:
## mean of the differences 
##               -44.99238
```
You see that in both instances of running the `t.test()` that we got the same results. Because all of our dependent variable is contained in one column, I would recommend using the *formula syntax* method. The *indexing* method is more helpful when you have your dependent variables split across two columns. 



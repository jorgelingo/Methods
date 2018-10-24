---
title: "8_StringsandFactors"
author: "Jorge Valdes"
date: "10/24/2018"
output: 
  html_document:
    keep_md: yes
---

# Introduction

The notes for this week are built from Chapters 11 and 12 from the physical copy of the _R for Data Science_ book by Wickham and Grolemund. This week we will be working on pattern matching, a very useful skill that comes in very handy, especially when a dataset has typos, hidden spaces, case mismatches, etc. It does, however, require some getting used to. Let's being by initializing our R session. In particular, we are going to be working on a set of character strings that have some sort of pattern or systematicity in them, which are referred to as _regular expressions_ or _regexps_ for short. Make sure to set your working directory and load in the `tidyverse` library. Additionally, we'll be working with the `stringr` package which needs to be loaded separately.


```r
suppressMessages(library(tidyverse))
library(stringr)
```

### Creating Strings

Character strings are enclosed in quotes. We've seen examples of strings already with textual responses or even when we refer to the name of a column header. For the most part, we can enclose character strings without any problems. 


```r
ex1 <- "how do you do?"
ex2 <- "good luck!"
ex1
```

```
## [1] "how do you do?"
```

```r
ex2
```

```
## [1] "good luck!"
```

For a couple of special characters, they will need to be represented in a different way, mainly using the "\". New lines are represented as "\n" and tabs are represented as "\t". To create a vector of characters, we would need to concatenate:


```r
string <- c("a", "b", "c")
string
```

```
## [1] "a" "b" "c"
```

```r
string[3]
```

```
## [1] "c"
```

## Stringr package

Commands within the `stringr` package begin with `str_`, making them easier to remember than base R commands. For example, we can count the number of characters in each element of a character vector with `str_length()`. 


```r
str_length(c("Happy Birthday", "¿Cuántos años tienes?", "g"))
```

```
## [1] 14 21  1
```

If you want to combine several strings into one string, you can use `str_c()`. This would be particularly useful if you had to concatenate codes for a Goldvarb analysis. 


```r
str_c("a", "b", "c", "_", "x", "y", "z")
```

```
## [1] "abc_xyz"
```

If you need a separator between elements, then this can be specified in the `sep = ` argument. 


```r
str_c("H", "a", "p", "p", "y", sep = ".")
```

```
## [1] "H.a.p.p.y"
```

We can also subset strings, specifying by position which elements we want, with `start` and `end` arguments. positive numbers count from the left edge, negative numbers from the right edge. 


```r
colors <- c("red", "blue", "green")
str_sub(colors, 1, 2)
```

```
## [1] "re" "bl" "gr"
```

```r
str_sub(colors, -2,-1)
```

```
## [1] "ed" "ue" "en"
```

##Matching Patterns

This is the important part! With matching, we will be able to extract certain types of patterns, sub, etc. There are some special characters involved that specify how specific of a match we want. These are hard to remember at first but with practice they get better. We'll visualize the matches that we are complting using the `str_view()` and `str_view_all()` functions. 


```r
fruits <- c("apple", "banana", "pear")
fruits
```

```
## [1] "apple"  "banana" "pear"
```

```r
#match "an"
str_view(fruits, "an")
```

<!--html_preserve--><div id="htmlwidget-75f15b1ade432080518b" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-75f15b1ade432080518b">{"x":{"html":"<ul>\n  <li>apple<\/li>\n  <li>b<span class='match'>an<\/span>ana<\/li>\n  <li>pear<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

Notice this is an exact match and it matches the first instance of "an". If we use a "." it searches for any character except a new line. 


```r
str_view(fruits, ".a.")
```

<!--html_preserve--><div id="htmlwidget-71edeb7da6fd93e6d4ae" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-71edeb7da6fd93e6d4ae">{"x":{"html":"<ul>\n  <li>apple<\/li>\n  <li><span class='match'>ban<\/span>ana<\/li>\n  <li>p<span class='match'>ear<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

There are special ways to handle special characters--but let's just stick to the basics for now. 

We can also specify edges to search from. These are ferred to as anchors. To indicate the left edge, use "^", for the right edge, use "$". 


```r
str_view(fruits, "^a")
```

<!--html_preserve--><div id="htmlwidget-d942c33cd226242a0baf" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-d942c33cd226242a0baf">{"x":{"html":"<ul>\n  <li><span class='match'>a<\/span>pple<\/li>\n  <li>banana<\/li>\n  <li>pear<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
Notice that this looks for an a from the beginning of a string. 


```r
str_view(fruits, "a$")
```

<!--html_preserve--><div id="htmlwidget-e09cbaa18b17e59fca39" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-e09cbaa18b17e59fca39">{"x":{"html":"<ul>\n  <li>apple<\/li>\n  <li>banan<span class='match'>a<\/span><\/li>\n  <li>pear<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
and note that this searches for an "a" from the end of the string. 

We can combine both for more specificity.


```r
desserts <- c("apple pie", "apple", "apple tart")
str_view(desserts, "^apple$")
```

<!--html_preserve--><div id="htmlwidget-a572d4e5d9a77b5a1275" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-a572d4e5d9a77b5a1275">{"x":{"html":"<ul>\n  <li>apple pie<\/li>\n  <li><span class='match'>apple<\/span><\/li>\n  <li>apple tart<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

###More specials

Here are some additional special matches. 

  * \d matches any digit
  * \s matches any whitespace, including space, tab, newline
  * [abc] matches a, b, or c
  * [^abc] matches anything but a, b, or c
  
We can use the "or" operator (the vertical pipe, "|") to select between options. 


```r
str_view(c("grey", "gray", "groy"), "gr(e|a)y")
```

<!--html_preserve--><div id="htmlwidget-e452794d0fdfc0b723cd" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-e452794d0fdfc0b723cd">{"x":{"html":"<ul>\n  <li><span class='match'>grey<\/span><\/li>\n  <li><span class='match'>gray<\/span><\/li>\n  <li>groy<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

##Repetition
We can also specify how many times a pattern can match with the following special characers.

  * ?: 0 or 1
  * +: 1 or more
  * *: 0 or more (often referred to as wildcard character)
  
Treat these matches as literal. Following an example from the book:


```r
x <- "1888 is the longest year in Roman numberals: MDCCCLXXXVIII"
x
```

```
## [1] "1888 is the longest year in Roman numberals: MDCCCLXXXVIII"
```

matching 0 or 1 "CCs":

```r
str_view(x, "CC?")
```

<!--html_preserve--><div id="htmlwidget-a8ea4c24af526ee1efb2" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-a8ea4c24af526ee1efb2">{"x":{"html":"<ul>\n  <li>1888 is the longest year in Roman numberals: MD<span class='match'>CC<\/span>CLXXXVIII<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
We found a "CC"" without a repetition so that matches the 0 criterion.


```r
str_view(x, "CC+")
```

<!--html_preserve--><div id="htmlwidget-cf727453bac9657b1914" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-cf727453bac9657b1914">{"x":{"html":"<ul>\n  <li>1888 is the longest year in Roman numberals: MD<span class='match'>CCC<\/span>LXXXVIII<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
Here, the match specifies a repetition of at least once, so the answer is now "CCC".


```r
str_view(x, "C[LX]+")
```

<!--html_preserve--><div id="htmlwidget-1450da85fec8804a94d3" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-1450da85fec8804a94d3">{"x":{"html":"<ul>\n  <li>1888 is the longest year in Roman numberals: MDCC<span class='match'>CLXXX<\/span>VIII<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
Here, we are looking for a sequence that begins with C, must contain an L and X, then the X must repeat at least once but can be more, so final answer is "CLXXX". These symbols can be used within patterns and parentheses can be used to group things together. 


```r
str_view(c("color","colour", "collar"), "colou?r")
```

<!--html_preserve--><div id="htmlwidget-f654a0674b186b82ff3b" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-f654a0674b186b82ff3b">{"x":{"html":"<ul>\n  <li><span class='match'>color<\/span><\/li>\n  <li><span class='match'>colour<\/span><\/li>\n  <li>collar<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

```r
str_view("banana", "ba(na)+")
```

<!--html_preserve--><div id="htmlwidget-cb813e2a436e41d93e6c" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-cb813e2a436e41d93e6c">{"x":{"html":"<ul>\n  <li><span class='match'>banana<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

We can specify precise repetition matches with curly braces {}

  * {n}: exactly n
  * {n,}: no or more
  * {,m}: at most m
  * {n,m}: between n and m
  

```r
str_view(x,"C{2}")
```

<!--html_preserve--><div id="htmlwidget-abc59d90bb068958d99a" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-abc59d90bb068958d99a">{"x":{"html":"<ul>\n  <li>1888 is the longest year in Roman numberals: MD<span class='match'>CC<\/span>CLXXXVIII<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
search for "C" matched exactly twice. 


```r
str_view(x, "C{2,}")
```

<!--html_preserve--><div id="htmlwidget-7f232a50acb3c02eb97b" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-7f232a50acb3c02eb97b">{"x":{"html":"<ul>\n  <li>1888 is the longest year in Roman numberals: MD<span class='match'>CCC<\/span>LXXXVIII<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
match "C" at least twice

## Match detection
We've covered some basics on _regexps_. There are definitely more advanced topics, but now we'll focus on what sorts of things we can do with _regexps_. To find matches and provide a logical vector, we can use `str_detect()`:


```r
str_detect(fruits, "e")
```

```
## [1]  TRUE FALSE  TRUE
```
i.e., does our string sequence contain an "e"?

Because the logical class is treated as a binary variable, TRUE == 1 and FALSE == 0, so we can sum and calculate means. 


```r
#words is a dataset with 1000 common words
#how many start with t? 

sum(str_detect(words, "^t"))
```

```
## [1] 65
```

```r
#what proportion of words end with a vowel?
mean(str_detect(words, "[aeiou]$"))
```

```
## [1] 0.2765306
```

So we can use `str_detect()` as a means to subset specific characteristics--imagine, how helpful this would with the database exercise that you conducted!


```r
str_subset(words, "x$") # search for all words ending in x
```

```
## [1] "box" "sex" "six" "tax"
```

If instead, the words are in a column of a data frame, then we can directly use `str_detect()` with `filter()`. 


```r
df <- tibble(word = words, i = seq_along(word))

df %>% 
  filter(str_detect(words, "x$"))
```

```
## # A tibble: 4 x 2
##   word      i
##   <chr> <int>
## 1 box     108
## 2 sex     747
## 3 six     772
## 4 tax     841
```

`str_count` instead tells you how many matches there are--something that can be useful for descriptives. 


```r
str_count(fruits, "a")
```

```
## [1] 1 3 1
```

```r
#on average how many vowels per word?
mean(str_count(words, "[aeiou]"))
```

```
## [1] 1.991837
```

And we can use this with `mutate` in a data frame.


```r
df %>% 
  mutate(
    vowels = str_count(word, "[aeiou]"),
    consonants = str_count(word, "[^aeiou]")
  )
```

```
## # A tibble: 980 x 4
##    word         i vowels consonants
##    <chr>    <int>  <int>      <int>
##  1 a            1      1          0
##  2 able         2      2          2
##  3 about        3      3          2
##  4 absolute     4      4          4
##  5 accept       5      2          4
##  6 account      6      3          4
##  7 achieve      7      4          3
##  8 across       8      2          4
##  9 act          9      1          2
## 10 active      10      3          3
## # ... with 970 more rows
```

*See exercises on p. 211*

## Extracting matches

We are going to use a more complex dataset (like a corpus) to show examples with match extraction. 


```r
length(sentences)
```

```
## [1] 720
```

```r
head(sentences)
```

```
## [1] "The birch canoe slid on the smooth planks." 
## [2] "Glue the sheet to the dark blue background."
## [3] "It's easy to tell the depth of a well."     
## [4] "These days a chicken leg is a rare dish."   
## [5] "Rice is often served in round bowls."       
## [6] "The juice of lemons makes fine punch."
```

We can search for any sentences with colors in them. First, let's create a vector of color words.


```r
colors <- c("red", "orange", "yellow", "green", "blue", "purple")
#to create a regular expression, we can collapse the vector into a singular expression with vertical pipes to indicate "or"
color_match <- str_c(colors, collapse = "|")
color_match
```

```
## [1] "red|orange|yellow|green|blue|purple"
```



```r
has_color <- str_subset(sentences, color_match)
matches <- str_extract(has_color, color_match)
head(matches)
```

```
## [1] "blue" "blue" "red"  "red"  "red"  "blue"
```

This will only extract the first match. To get all matches, we'll need to use `str_extract_all()`, which returns a list (a more complex data structure).


```r
more <- sentences[str_count(sentences, color_match) > 1]
str_view_all(more, color_match)
```

<!--html_preserve--><div id="htmlwidget-bce6d4727fd86458e424" style="width:960px;height:100%;" class="str_view html-widget"></div>
<script type="application/json" data-for="htmlwidget-bce6d4727fd86458e424">{"x":{"html":"<ul>\n  <li>It is hard to erase <span class='match'>blue<\/span> or <span class='match'>red<\/span> ink.<\/li>\n  <li>The <span class='match'>green<\/span> light in the brown box flicke<span class='match'>red<\/span>.<\/li>\n  <li>The sky in the west is tinged with <span class='match'>orange<\/span> <span class='match'>red<\/span>.<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

```r
str_extract_all(more, color_match)
```

```
## [[1]]
## [1] "blue" "red" 
## 
## [[2]]
## [1] "green" "red"  
## 
## [[3]]
## [1] "orange" "red"
```

### Grouped matches
We can also group matches. This is helpful when maybe you only know one piece of your pattern. In this case, we'll extract nouns from the sentences data set but selecting words that follow "a" or "the". To do this, we'll need to figure out a creative way to select the next word. 


```r
noun <- "(a|the) ([^ ]+)"

has_noun <- sentences %>% 
  str_subset(noun) %>% 
  head(10)

has_noun %>% 
  str_extract(noun)
```

```
##  [1] "the smooth" "the sheet"  "the depth"  "a chicken"  "the parked"
##  [6] "the sun"    "the huge"   "the ball"   "the woman"  "a helps"
```

### replacement
I use this a lot whenever I notice any typos in my dataset. `str_replace()` will replace the first element. `str_replace_all()` will replace all matching elements. 


```r
x <- c("apple", "pear", "banana")
str_replace(x, "[aeiou]", "-")
```

```
## [1] "-pple"  "p-ar"   "b-nana"
```

```r
str_replace_all(x, "[aeiou]", "-")
```

```
## [1] "-ppl-"  "p--r"   "b-n-n-"
```

We can also do multiple replacements by enclosing with `c()`:


```r
z <- c("1 house", "2 cars", "3 people")
str_replace_all(z, c("1" = "one", "2" = "two", "3" = "three"))
```

```
## [1] "one house"    "two cars"     "three people"
```

We can use `str_split()` to split strings into smaller elements, like the text-to-columns feature in Excel. 


```r
sentences %>% 
  head(5) %>% 
  str_split(" ")
```

```
## [[1]]
## [1] "The"     "birch"   "canoe"   "slid"    "on"      "the"     "smooth" 
## [8] "planks."
## 
## [[2]]
## [1] "Glue"        "the"         "sheet"       "to"          "the"        
## [6] "dark"        "blue"        "background."
## 
## [[3]]
## [1] "It's"  "easy"  "to"    "tell"  "the"   "depth" "of"    "a"     "well."
## 
## [[4]]
## [1] "These"   "days"    "a"       "chicken" "leg"     "is"      "a"      
## [8] "rare"    "dish."  
## 
## [[5]]
## [1] "Rice"   "is"     "often"  "served" "in"     "round"  "bowls."
```


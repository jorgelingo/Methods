---
title: "R Introduction Notes"
author: "Jorge Valdes"
date: "8/22/2018"
output:
  html_document:
    keep_md: yes
  pdf_document: default
---



## Introduction
By now you should have installed both **R** and **R Studio**. Why do we need both? **R** is a programming language (think, something like an Operating System, OS) and comes with a basic graphical interface, but it's quite challenging to use because it is very bare bones. Consequently, several *integrated development environments* (IDEs) have been developed that make it easier to interact with **R**--**R Studio** is one such IDE. In this case, you can think of the IDE like the device that you personally use to run your OS.  Let's start by opening the **R Studio** application. You should see something like this:

![RStudio_opening](Images/Rstudio_beginning.png)

You can think of the **R Studio** as divided into 4 quadrants although currently there are only 3 areas that are visible. The left hand space is currently taken up by the *Console*. This is where you can enter R commands directly. You can enter any commands at the cursor:

> \>

### Console
First, and foremost, R can be used as a calculator. Go ahead and enter simple arithmetic operations in the Console.


```r
2+6
```

```
## [1] 8
```

```r
12-5
```

```
## [1] 7
```

```r
3*8
```

```
## [1] 24
```

```r
44/7
```

```
## [1] 6.285714
```

```r
2^3
```

```
## [1] 8
```

We can also enter some basic commands into the Console.


```r
sqrt(4)
```

```
## [1] 2
```

```r
sum(2,6,8)
```

```
## [1] 16
```

```r
rnorm(10)
```

```
##  [1] -0.20926103  0.03624583  0.93121676 -0.16292529 -0.22294079
##  [6]  0.84661801  1.17901967  0.78530403 -2.00554983  0.31077098
```

Notice that with the last command, we got multiple values in our output, and consequently, the values inside of the brackets have changed. 

* What do the values in the bracket mean?
* What is the `rnorm()` command doing?

Whenever working with unknown commands (or needing to look up how the command is used), you can access the help menu. There are several ways to do this.

* type `?rnorm()`
* navigate to the help tab in the lower right quadrant and type in the command name
* Google it!

The help files are a little daunting at first, but they contain some important information. In this case, we see that `rnorm()` is a part of a family of commands that work with the *normal distribution* (a statistical concept that we'll cover later, but think 'bell curve'). For `rnorm()`, we see specifically the following **Usage** documentation:

> `rnorm(n, mean = 0, sd = 1)`

How can this be if we only entered one value with our command? Look down further under the **Arguments** documentation and find `n`, `mean`, and `sd`. What do you see? What did we input?

The remainder of the non-specified arguments have *default* arguments, which we did not have to input. These default values are a *mean* of 0 and a *standard deviation* of 1 (we'll cover these statistical concepts later, but think 'central point' and width of bell curve). We can change these default arguments by specifying new ones.


```r
rnorm(10, mean = 1000, sd = 300)
```

```
##  [1]  819.3036 1360.5991  472.3931 1021.0689 1202.2296 1200.3562  848.2317
##  [8] 1153.2032 1038.2028 1312.2092
```

Notice that these values are much larger than the original values because of how we changed the arguments. 

Look up the help files of `rep()` and `seq()` and try to create a sequence from 1 to 5 that repeats 3 times.

## Environment

In the upper right-hand quadrant you see an Environment pane that is currently empty. This is where you will see any *data sets* and *variables* that you create (or import). Let's build off of the simple arithmetic operations that we created earlier and do some simple algebraic operations. To assign something to a variable, we want to get into the habit of using the *assignor* operator, which looks like a left-pointing arrow `<-` (there are other ways to accomplish the same thing, but I want you to get into the habit of doing it this way to prevent any confusion down the road). There is a keyboard shortcut for this opeartor: `ALT/OPTION + -`. You should get in the habit of using keyboard shortcuts, especially for any processes that you repeat frequently. To access the shortcuts reference page, type `ALT/OPTION + SHIFT + K`. 

First, let's just assign some simple variables:

```r
a <- 3
b <- 6
```
Notice what happened in the **Environment** pane. We now have our variables listed (as well as their values). 

Compare the following two operations:

```r
a*b
```

```
## [1] 18
```

```r
c <- a*b
```

Notice that you can either ccontinue to get output from some operation on variables or you can store that output to a new variable, in which case, you would need to call that variable to see its value.

```r
c
```

```
## [1] 18
```

We can also store multiple values to variables and call our variables any (meaningful) name that we want.

```r
seq1 <- rnorm(10)
seq2 <- rnorm(10, mean = 20, sd = 2)
seq3 <- rnorm(7, mean = 30, sd = 4)
```
Notice how the variables are stored in the **Environment** pane.
Again, we can perform simple operations on sequences of variables as long as it's legit to do so. 

```r
seq2 - seq1
```

```
##  [1] 22.99285 22.96456 19.28526 18.16679 24.95087 15.39667 20.00083
##  [8] 22.91581 19.35752 22.39958
```

```r
seq2/4
```

```
##  [1] 5.469013 5.014504 5.035284 4.719880 5.533096 4.092985 5.042888
##  [8] 5.368894 5.066184 5.398631
```

```r
seq3 - seq2
```

```
## Warning in seq3 - seq2: longer object length is not a multiple of shorter
## object length
```

```
##  [1]  1.245436 11.679691  7.615803 11.298968  5.886715 14.982148  7.351361
##  [8]  1.645913 11.472972  6.162416
```
Notice that we get a warning message in the last operation. What do we do with warnings and errors? Don't ignore them! They can be helpful even if they take a while to get used to. If all else fails, copy the error message into google and see what pops up!

Let's learn one other useful function for variables. One combining multiple values, you will need to *concatenate* these values. We use the `c()` command to do so. 

```r
values <- c(2,7,8,3,2,9,7)
#try doing so without the c() command
#values2 <- (2,7,8,3,2,9,7)
```

Let's combine this all together and perform vector arithmetic. 


```r
weight <- c(60, 72, 57, 90, 95, 72)
height <- c(1.75, 1.80, 1.65, 1.90, 1.74, 1.91)

bmi <- weight/height^2
bmi
```

```
## [1] 19.59184 22.22222 20.93664 24.93075 31.37799 19.73630
```

We haven't significantly talked about 2 other panes. One is the **Editor** pane, which is where we will be writing our scripts and notebooks. This will become relevant later. Then, in pane where we found the Help files contains other useful functions. I call it the **Kitchen Sink** pane. Besides the help files, any plots (graphs) that we build will show up here as well as a navigation pane for file directories and for packages (we'll discuss this more later in the semester). 

## Importing Data, Indexing, and Subsetting

The secret to importing data is:

* knowing where you are (*working directory*)
* knowing where your data file is (*file path*)
* knowing the file extension of your data

Later on, we'll be using some R Studio functionality that makes handling working directories much easier, but you can see where your current working directory is pointed towards at the top of the Console pane. 

In my current directory, I have a folder called *Data* that has a `.csv` data file called "sample_data.csv". Let's import that data set into R Studio.


```r
sample <- read.csv("data/sample_data.csv")
```
What happened in the **Environment** console? What happens when you click on *sample*?

One of the hardest learning curves of working with R is that you have to get comfortable not looking at your data all of the time. There are some very helpful functions that help make you get familiar with the structure of your data file and gives you quick descriptives. 


```r
head(sample) #prints first 6 rows
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
tail(sample) #prints last 6 rows
```

```
##    RECORDING_SESSION_LABEL aux_type response_accuracy count   prop Group
## 47                     5cc        H           correct    16 100.00  late
## 48                     5cc        H         incorrect     0   0.00  late
## 49                    65cc        E           correct    15  93.75  late
## 50                    65cc        E         incorrect     1   6.25  late
## 51                    65cc        H           correct    14  87.50  late
## 52                    65cc        H         incorrect     2  12.50  late
```

```r
str(sample) #gives structure of data columns
```

```
## 'data.frame':	52 obs. of  6 variables:
##  $ RECORDING_SESSION_LABEL: Factor w/ 13 levels "118cc","11cc",..: 1 1 1 1 2 2 2 2 3 3 ...
##  $ aux_type               : Factor w/ 2 levels "E","H": 1 1 2 2 1 1 2 2 1 1 ...
##  $ response_accuracy      : Factor w/ 2 levels "correct","incorrect": 1 2 1 2 1 2 1 2 1 2 ...
##  $ count                  : int  14 2 15 1 13 3 14 2 14 2 ...
##  $ prop                   : num  87.5 12.5 93.75 6.25 81.25 ...
##  $ Group                  : Factor w/ 1 level "late": 1 1 1 1 1 1 1 1 1 1 ...
```

```r
summary(sample) #gives summary for each column
```

```
##  RECORDING_SESSION_LABEL aux_type response_accuracy     count      
##  118cc  : 4              E:26     correct  :26      Min.   : 0.00  
##  11cc   : 4              H:26     incorrect:26      1st Qu.: 1.75  
##  139cc  : 4                                         Median : 8.00  
##  14cc   : 4                                         Mean   : 8.00  
##  15cc   : 4                                         3rd Qu.:14.25  
##  16cc   : 4                                         Max.   :16.00  
##  (Other):28                                                        
##       prop         Group   
##  Min.   :  0.00   late:52  
##  1st Qu.: 10.94            
##  Median : 50.00            
##  Mean   : 50.00            
##  3rd Qu.: 89.06            
##  Max.   :100.00            
## 
```
The `str()` function is extremely valuable because it lets you knw what type of column R thinks you have. We'll work a lot more with this concept in 2 weeks but for the time being, it's sufficient to know that we have the following columns:

* *factor*: these are systematic, categorical variables (like Sex, Race, Political Party)
* *integer*: these are whole numbers
* *numeric/double*: these are numbers with decimal values
* *logical*: binary variable with TRUE/FALSE or 0/1 outcome levels
* *character*: character strings or text

What do we have in our sample data set?

To navigate or select specific values in your data set, we will be using *indexing* and *subsetting*. One method of indexing is to specify row and column values within single brackets '[]'.


```r
sample[6,]
```

```
##   RECORDING_SESSION_LABEL aux_type response_accuracy count  prop Group
## 6                    11cc        E         incorrect     3 18.75  late
```

```r
sample[,3]
```

```
##  [1] correct   incorrect correct   incorrect correct   incorrect correct  
##  [8] incorrect correct   incorrect correct   incorrect correct   incorrect
## [15] correct   incorrect correct   incorrect correct   incorrect correct  
## [22] incorrect correct   incorrect correct   incorrect correct   incorrect
## [29] correct   incorrect correct   incorrect correct   incorrect correct  
## [36] incorrect correct   incorrect correct   incorrect correct   incorrect
## [43] correct   incorrect correct   incorrect correct   incorrect correct  
## [50] incorrect correct   incorrect
## Levels: correct incorrect
```
The first command selects the 6th row and the second command selects the 3rd column. So remember the order: RC, rows, then columns:

![RC Cola](Images/rc_cola.jpg)

We can combine both together to get a single cell.

```r
sample[6,3]
```

```
## [1] incorrect
## Levels: correct incorrect
```
This is the value for the 6th row and 3rd column. 

When referencing colums, we can also use the '$' to index column names (which makes things a little more transparent).

```r
sample$response_accuracy
```

```
##  [1] correct   incorrect correct   incorrect correct   incorrect correct  
##  [8] incorrect correct   incorrect correct   incorrect correct   incorrect
## [15] correct   incorrect correct   incorrect correct   incorrect correct  
## [22] incorrect correct   incorrect correct   incorrect correct   incorrect
## [29] correct   incorrect correct   incorrect correct   incorrect correct  
## [36] incorrect correct   incorrect correct   incorrect correct   incorrect
## [43] correct   incorrect correct   incorrect correct   incorrect correct  
## [50] incorrect correct   incorrect
## Levels: correct incorrect
```
This part may not seem useful on its own but what makes indexing powerful is that you can also combine it with conditional statements, which allows you to subset your data. Let's say that we only want to see trials in which participants answered correctly. we can use the following to show only rows that are correct.

```r
sample[sample$response_accuracy == "correct",]
```

```
##    RECORDING_SESSION_LABEL aux_type response_accuracy count   prop Group
## 1                    118cc        E           correct    14  87.50  late
## 3                    118cc        H           correct    15  93.75  late
## 5                     11cc        E           correct    13  81.25  late
## 7                     11cc        H           correct    14  87.50  late
## 9                    139cc        E           correct    14  87.50  late
## 11                   139cc        H           correct    15  93.75  late
## 13                    14cc        E           correct    10  62.50  late
## 15                    14cc        H           correct    13  81.25  late
## 17                    15cc        E           correct    14  87.50  late
## 19                    15cc        H           correct    15  93.75  late
## 21                    16cc        E           correct    16 100.00  late
## 23                    16cc        H           correct    15  93.75  late
## 25                    25cc        E           correct    11  68.75  late
## 27                    25cc        H           correct    13  81.25  late
## 29                    27cc        E           correct    16 100.00  late
## 31                    27cc        H           correct    15  93.75  late
## 33                    29cc        E           correct    15  93.75  late
## 35                    29cc        H           correct    14  87.50  late
## 37                     3cc        E           correct    15  93.75  late
## 39                     3cc        H           correct    14  87.50  late
## 41                     4cc        E           correct    16 100.00  late
## 43                     4cc        H           correct    16 100.00  late
## 45                     5cc        E           correct    13  81.25  late
## 47                     5cc        H           correct    16 100.00  late
## 49                    65cc        E           correct    15  93.75  late
## 51                    65cc        H           correct    14  87.50  late
```

Notice that the conditional is in the bracket and that we are still using the comma. essentially, the syntax reads: give me all rows where response_accuracy equals "correct". Notice that we had to use 2 equal signs. What happens if you only write what is inside of the brackets without the comma?

An even more typical operation for us would be to get the mean proportion of correct answers for the entire experiment. 

```r
mean(sample$prop[sample$response_accuracy == "correct"])
```

```
## [1] 89.18269
```
We have just asked for the mean of the values in the prop (Proportions) column conditioned by including only rows where response_accuracy was correct. We don't need the comma in the brackets this time because since we are only working on a column (sample$prop) there is no other column that we can reference within the brackets. 

Now try to find the mean count of incorrect trials for all participants. 

The conditional statements within the brackets can also be combined. Let's say that we want the mean proportion of correct responses only for E trials (E refers to an auxiliary type which was a by-design manipulation in the experiment).

```r
mean(sample$prop[sample$response_accuracy == "correct" & sample$aux_type == "E"])
```

```
## [1] 87.5
```

Notice that we are combining two conditionals with an '&' operator. There are several other *Boolean* operators that we can use, but we'll work on those later. 

Let's read up on the `subset()` function.

```r
?subset()
```
Take a look at the **Examples** at the help page. Can you figure out how to subset the data to only those participants who scored better than 80% on correct trials? 

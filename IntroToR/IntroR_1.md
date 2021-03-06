**Navigation - [Home](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/README.md) - [Intro to R](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/IntroToR/IntroR_1.md) - [Web Services & APIs](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/WebServices/WebServices.md) - [Basic Search](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/BasicSearches/BasicSearches.md) - [Pollen Objects](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/PollenObjects/PollenObjects.md) - [Manipulating Pollen](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/ManipulatingPollen/manipulating.pollen.md)**

-----------------

Introduction to R
========================================================

### Palynological Databases: Hands-on Computer Workshop, AASP 46th Meeting, San Francisco

This lab is designed to give you a gentle introduction to R. It will cover basic functionality of R, including variables and functions, together with some examples using descriptive statistics and plotting. If you have not already used R, it is recommended that you work through the first two sections of this document before starting to work with the RNeotoma package.

We will be using a file of pollen data for this lab: _percSite.csv_

Data in R
---------------
One of the main ways in which R differs from spreadsheet software is that it uses a _workspace_ in the memory of your computer, and the data you use and create is stored there as a _variable_: an _object_ with a _name_ and a _value_. This first section will introduce the different types of variable and how to manipulate them.

### Variables in R
The basic form of a variable in R comes in two parts, the variable name (e.g. x) and the value that
is attributed to it (e.g. 5). The assignment operators (`=` and `<-`) are used to associate a given value with a variable. For example `x <- 5` gives the value 5 to a variable called `x` and creates it if it does not exist. Once a variable is created, the value is not fixed and may be modified at anytime, and also may be used in subsequent operations. Note that as you create variables, nothing will appear on the screen. To see the value(s) assigned to any variable, simply type its name at the prompt. Try entering the following commands at the command prompt:


```r
x <- 5
x
sqrt(x)
x <- 9
x
sqrt(9)
```


### Data modes in R
R can deal with many different types or modes of data, but some of the most common are:
- Numeric, e.g. 5 or 6e-5  
- Character  e.g. 'San Francisco' or 'London'
- Factor e.g. 'male' or 'female'
- Logic	True/False

### Data structures in R
In addition to the data types, R has many standard structures (or objects) for storing data. These include:
- Single variables (0D): e.g. the population of Australia
- Vectors (1D, 1 mode): e.g. the population size of each of the 20 largest cities of the world
- Matrices (2D, 1 mode): a gridded climate field, a raster image
- Arrays (nD, 1 mode): a stack of raster images for different time periods
- Data frames (2D, multiple modes): a population survey - where many different types may be recorded for the same observation
- Lists: A collection of other data objects


Manipulating variables in R
---------------------------------------
### Reading data from files
R can use many different file types, but csv files are recommended as the easiest way to transfer between R and Excel. Start by changing your working directory to the directory holding the two files listed above. Then get a list of files as follows (note the use of the `pattern` parameter to get only certain files):

```r
list.files("./", pattern = ".csv")
```

```
## [1] "cities.csv"   "iris.csv"     "percSite.csv" "test.csv"
```

Now read in a file

```r
pollen <- read.csv("percSite.csv")
```

And check to see the variable `pollen` that has been created in the R workspace (note that variable `x` we created earlier is also there).

```r
ls()
```

```
## [1] "pollen" "x"
```


R will store data read in a dataframe. To print out the contents of any variable, simply type the name of that variable at the command prompt. Other useful commands are `class()` to see what data class a variable is, and `names()` to get a list of the column headers. The function `str()` reports the structure of the data set, describing the column names and the type of data stored in them.

```r
pollen
class(pollen)
names(pollen)
str(pollen)
```


In order to access the values in a dataframe or other R object, there are two main methods: column notation and indexing. Column notation uses the dollar sign ($) after the name of dataframe, with the column name appended (note that R replaces any white spaces in the column names with '.'). For example:

```r
pollen$Betula
pollen$Pinus.subg..Strobus
```

Indexing uses a coordinate system to access variables. These are given in brackets after the name of the object, and take as many indexes as there are dimensions of the data object. So for a 1D vector, we require one, for a 2D matrix or data frame, we require two, etc. For matrices and data frames, the indices are [row,column]. If either of these are left blank, then all rows (or columns) are selected:

```r
pollen[, 1:4]  # Columns 1 to 4
pollen[1:10, ]  # First 10 rows
pollen[1:20, 1:2]  # Indices can be combined
pollen$Betula[1:25]  # Note that indices can be combined
pollen$Betula[3]  # 3rd element
pollen$Betula[-3]  # All but 3rd element
```


Logical operators $<, <=, >, >=, ==, !=$ can be used to select parts of the data set by value. This is very useful if you only want to analyze part of your dataset, or split your dataset into groups:

```r
pollen$Betula[pollen$agecal < 5000]  # All Betula counts with an age < 5ka BP
pollen$Betula[pollen$Pinus.subg..Strobus > 70]  # All Betula counts when Pine > 70%
```

### Writing data to files
Variables created in R may also written out to csv files using the `write.csv()` function.

```r
x <- pollen$Betula[pollen$agecal < 5000]
write.csv(x, "test.csv")
```

This will write a csv file called _test.csv_ in your working directory, containing the values in the vector `x`, created from the original data frame . Note that R uses the variable name as a column header and adds a column with line numbers. You can remove the line numbers by adding the parameter `row.names=FALSE` to the `write.csv()` function. 

### R Workspace
In general, R stores all the variables you have created in system memory. To see these variables at any time, type:

```r
ls()
```

Alternatively, look in the 'Environment' tab in the top-right hand window in RStudio.
The function `save.image()` allows you to save the contents of the workspace into
file called _.RData_ in the current working directory.

```r
save.image()
```


Variables can be removed using the `rm()` function.

```r
rm(x)
rm(list = ls())
```

This last command removes all variables from the workspace. Of course, if you have
already saved them using the `save.image()`, you can restore the workspace
with `load(".RData")`. 

```r
load(".RData")
```


Functions in R
----------------
Functions typically are comprised of the name of the function (`sqrt()` for taking square roots) and a set of parentheses. The parentheses are used to pass data to the function as well as setting parameters to change the behavior of the function.

```r
sqrt(5)
```


Note that we can use the assignment operator to save the output from a function, allowing you to use this in subsequent functions and analyses. 

```r
y = sqrt(5)
round(y)
```

To save time and code, functions can be combined:

```r
round(sqrt(5))
```


The `seq()` function produces a series of numbers on a regular step. By default, it require 3 parameters, the starting number, the ending number and the step.

```r
seq(from = 0, to = 20, by = 2)
```

If you include the parameter names, as in this example, the order does not matter. The parameter names can be omitted if you keep to the specified order of parameters. So this will give you the equivalent results.

```r
seq(0, 20, 2)
```

To find out what these parameters are, what they are called and what values they take, use the `help()` function or the `?` operator, e.g.:

```r
help(seq)
`?`(seq)
```

This will open a window with the help file for that function. If you do not know the name of a function, there is a search function `help.search()`, or use the help browser `help.start()`, browse to packages or use the search engine.

Some basic statistical functions
--------------------------------
We will now create some new vectors in R containing Betula and Pine percentages and the age vector, in order to use these with functions. Note the use of the assignment operator `<-`. You can also use the equal sign  (`=`) here and elsewhere in these examples.

```r
bet <- pollen$Betula
pin <- pollen$Pinus.subg..Strobus
age <- pollen$agecal
```


R has a large number of inbuilt functions. This section is designed to simply introduce you to the some basic functions for describing data.
### Functions to describe the central tendancy:

```r
mean(bet)
median(bet)
```


### Functions to describe the dispersion:

```r
sd(bet)  ## Standard deviation
var(bet)  ## Variance
min(age)  ## Minimum
max(age)  ## Maximum
range(age)  ## Min and Max
quantile(age)  ## Quantile (50% by default)
```

Note that `quantile()` takes a parameter that allows you to choose the quantile to be calculated, e.g. `quantile(age, c(0.1,0.9))`, will calculate the 10th and 90th percentile. I highly recommend that you read the help pages for these functions to understand what they do and how they can be modified. 

### Some other useful functions:

```r
sum(bet)
summary(bet)
```


R is object oriented, and so many functions will adapt to different data types. For example, the summary() function will provide a different output for a vector and a data frame:

```r
summary(bet)  ## Summary of numeric vector
summary(pollen)  ## Summary of data frame
```


Bivariate statistics
--------------------

### Functions to assess the relationship between pairs of variables.
Standard functions include calculation of the covariance and correlation:

```r
cov(bet, pin)
cor(bet, pin)
```

The correlation function gives Pearson's coefficient by default. Look and try to identify strong positive and negative correlations (i.e. values closer to one). We can replace Pearson's method with a robust method, Spearmans rank correlation, by including the parameter `method`:

```r
cor(bet, pin, method = "spearman")
```

See the help for `cor()` for other parameters. The correlations obtained can be tested using the `cor.test()` function. Is there a significant correlation between Betula and Pine percentages? Is this to be expected?

```r
cor.test(bet, pin, method = "spearman")  # Significance?
```

```
## Warning: Cannot compute exact p-value with ties
```

```
## 
## 	Spearman's rank correlation rho
## 
## data:  bet and pin
## S = 21795, p-value = 1.578e-15
## alternative hypothesis: true rho is not equal to 0
## sample estimates:
##     rho 
## -0.8986
```

The output gives you both the correlation coefficient (rho) and the _p_-value from a test against a _t_-distribution, indicating a significant negative correlation between these two sets of values.

Plot functions
-----------------
#### Enumerative plots
The simplest type of plot is an index plot, which simply plots values in the order they are recorded in the input vector. These are useful for examining the basic data structure and identifying errors and outliers. `plot()` is a generic plotting command and will adapt to different data types. The parameter `type='p'` gives the plot type, here using points. Other options are `'l'` for lines, `'h'` for histogram lines, `'s'` for a stepped plot and `'b'` for both line and points. See `help(plot)` for more options and other parameters. 

```r
plot(bet, type = "p")
```


### Summary plots
Summary plots attempt to describe the distribution of the data, giving some ideas about which values are most common and which are most rare. Histograms are commonly used for this method, values are 'binned' into a set of classes, and the histogram represents the frequency of occurrences in that bin. Bins are defined with the `breaks` parameter, which may be set to a constant number in which case the data range is split into that many bins, or as a sequence of numbers defining the intervals between bins. In this latter case, we can make use of the `seq()` function from earlier. 

```r
hist(bet, breaks = 20, main = "Histogram of Betula percentages")
hist(bet, breaks = seq(0, 40, 1), main = "Histogram of Betula percentages")
```

An alternative to histograms are boxplots, which show information about the data quartiles. Here the box represents the interquartile data (25-75% of the data), the thick bar is the median, and the 'whiskers' show the data range.

```r
boxplot(bet)
```

If we use the boxplot function with the full pollen data frame, it will produce boxplots for all taxa:

```r
boxplot(pollen[, 5:73])
```

To make this a little more useful, we can take only a subset of pollen taxa, and plot them with a square-root transformation. This uses the indexing idea we covered earlier:

```r
taxaID = c(5, 9, 24, 27, 31, 32, 34, 38, 47, 48)
names(pollen)[taxaID]
boxplot(sqrt(pollen[, taxaID]))
```


### Bivariate plots
Bivariate plots are designed to show the relationship between two variables, and how this may vary. The simplest form is the scatter plot. We use the `plot()` function again, but now we give it two variables (x and y). We can use this to vosualize the relationship between Betula and Pine shown in the correlation analysis

```r
plot(bet, pin)
```

Alternatively, we can plot pollen percentages against time (the third column of the data frame). Note the extra parameters to add better axis labels (`xlab`,`ylab`) and title (`main`), and the use of the `type` parameter to make this a line plot, rather than the default scatterplot.

```r
plot(age, bet, type = "l", xlab = "Yrs BP", ylab = "Percent", main = "Betula pollen")
```


Programming in R
----------------
### Scripting
As you do more in R, it is easier to use a script to record your commands, rather than entering them all at the command line. This is particularly true of commands that may span multiple lines as, if you make a mistake, it will be necessary to reenter each line. In both Windows and Mac OSX, R comes with an in-built scripting editor. Open a new script from the file menu, and copy the following commands to it to plot percentage values of Betula and Picea (note that as we have not made a vector of Picea values, we have to use the values in the data frame `pollen`).

```r
plot(age, bet, type = "l", col = 2, lwd = 2, ylim = c(0, 40))
lines(age, pollen$Picea, col = 3, lwd = 2)
legend("topleft", legend = c("Betula", "Picea"), fill = c(2, 3))
```

![plot of chunk unnamed-chunk-34](figure/unnamed-chunk-34.png) 

To run these commands, you can copy-paste them to the command window, or you can run the script directly. To do this, save the script from the file menu, giving it the name _plotPollen.r_. Now the script can be run using the `source()` command. Try extending this script to add another taxon to the plot with another `lines()` function.

```r
source("plotPollen.r")
```

And R will read through the script, executing each command in turn. Now if there are any mistakes or changes to be made, you can simply edit the script and re-run it.

### Programming flow
R contains the usual commands to control the flow of a program (`if()`, `for()`, `while()`). You can write a simple for loop as follows:

```r
for (i in 1:10) {
    print(i)
}
```

This runs a loop ten times, each time printing out the value of the iterator \texttt{i}. Note the format of the loop: the details of the loop are given in parentheses, and the commands to execute at each iteration are given between the curly brackets.

A slightly more complex example is given in the script \emph{cointoss.r}.

```r
source("cointoss.r")
```

This uses a for loop to simulate 100 coin tosses. In each iteration, a random number (between 0 and 1) is created, if this is over 0.5, this is taken to be 'heads' and a counter is increased by 1. Finally, the script prints out the number of 'heads' and the number of tails. The script uses a number of new functions: `runif()`, which selects a number from a uniform random distribution between 0 and 1, and `paste()` which pastes together character strings and variables for screen output.

Try to extend the script by increasing the number of loops, and by calculating the proportion of heads from the total number of loops at the end. 

### Creating your own functions
Once you have some experience with R, it is fairly easy to create your own functions. This is particularly useful if you need to repeat a set of analyses several times. The following code creates a function to calculate the standard deviation. 

```r
mySD <- function(x) {
    sqrt(var(x))
}
```

Once created, this exists as an object in the R workspace. Type \texttt{ls()} to see this. It can now be used in the same way as other functions

```r
mySD(bet)  # compare to sd(sl)
sd(bet)
```


-----------------

**Navigation - [Home](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/README.md) - [Intro to R](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/IntroToR/IntroR_1.md) - [Web Services & APIs](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/WebServices/WebServices.md) - [Basic Search](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/BasicSearches/BasicSearches.md) - [Pollen Objects](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/PollenObjects/PollenObjects.md) - [Manipulating Pollen](https://github.com/SimonGoring/Neotoma-Workshop_Oct2013/blob/master/ManipulatingPollen/manipulating.pollen.md)**

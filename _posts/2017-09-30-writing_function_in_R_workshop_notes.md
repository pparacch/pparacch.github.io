---
layout: post
date: 2017-09-30
title: "'Writing Functions in R', Notes (EARL London 2017)"
description: "How to write functions for fun and profit in R #R #function #goodpractices #MVP #incrementaldesign"
---

Attended the [EARL](https://earlconf.com/london/) (Enterprise Applications of the R Language) 2017 Conference in London this September (12th - 14th September) and, I had the opportunity to be a participant in the "Writing functions for Fun and Profit" workshop by Jenny Bryan (RStudio).

> "With Jenny Bryan If there's a bit of R code that you copy and paste repeatedly, package it in a function! User-written functions are a great way to increase your effectiveness in many contexts: scripts, R Markdown documents, R notebooks, Shiny apps, and, of course, R packages. Compared with highly repetitive code, functions can increase code quality, while also reducing programmer aggravation. Many principles of design and process work well across all those domains. This workshop should be useful to those new to writing functions, as well as those more experienced, e.g., ready to start writing packages. We will finish off with some coverage of functional programming and how to use it for iteration in R."

It was a __great experience__ with some _interesting_ __learnings__ and __insights__ that I have tried to capture in the notes below.

## Workshop Notes

Functions are quite important when performing exploratory analysis (interactive/ scripting) and, at the same time, they are a fundamental concept when building packages. Functions are necessary when you perform the same functionality over and over and allows to

- improve repeatability & maintainability, and
- have more control on the quality of your code.

### Dataset

The dataset used for the workshop is `gapminder::gapminder` (in the `gapminder` package). The gapminder dataset includes data on life expectancy, GDP per capita, and population by country. It represents an effort to collect social data from many countries and it is essentially used for teaching (cause of its simplicity).

Each row is a combination of social data (see below) by `country`, `continent` and `year`

- `lifeExp`, life expectation
- `pop`, population
- `gdpPercap`, the GDP per capita.

The plot below gives a glimpse on how the GDP per capita has been changing over time in the 5 continents...

```{r}
library(gapminder)
library(ggplot2)

p <- ggplot(data = gapminder, mapping = aes(x = year, y = gdpPercap))
p + geom_line(color="gray70", aes(group = country)) +
  geom_smooth(size = 1.1, method = "loess", se = FALSE) +
  scale_y_log10(labels=scales::dollar) +
  facet_wrap(~ continent, ncol = 5) +
  labs(x = "Year",
       y = "GDP per capita",
       title = "GDP per capita on Five Continents")

```

### The art of writing functions

Imagine that you want to write a function that returns (max - min) in the provided vector.

The __first step__ is to __figure out how to perform the specific task__ (the hows. There are many possible ways to perform the required operations, e.g.

```{r}
# Idea
# Use the summary function
x <- summary(gapminder$lifeExp)
x["Max."] - x["Min."]

# Idea
# Use the max and min functions
max(gapminder$lifeExp) - min(gapminder$lifeExp)

# Idea
# Use the range function
y <- range(gapminder$lifeExp)
y[2] - y[1]
```

__When starting writing a function__ we should __experiment with working high-level code__ in order __to have a better overview of the hows__ and then ask __"What is the MVP (Minimum Viable Product) that should be created?"__.  

The mentality is to __build incrementally__. Start with something simple (even if limited) and then improve it based on the actual needs, __do not try to implement the perfect function at once__.

Let's say that our MVP is the function below...

```{r}
## This is our first step - the MVP, simple and to the point
## Our MVP expects numeric vectors and use the min/ max functions
mmm <- function(x){ max(x) - min(x)}
```

Once you have the __MVP__ starts to explore its usage, e.g.
- How does it work with the expected inputs (numeric vectors)? Is it working as expected?
  - With integers?
  - With doubles?
- How does it work with unexpected inputs? What does it happen?
  - With strings?
  - With lists?
  - With dataframes?

```{r}
# Informal Testing
# Start to explore the function - how does it work with different input
# Expected inputs
#   Integer vector
mmm(1:10)
#   Double vector (should get same result as when experimenting )
mmm(gapminder$lifeExp)
# Start to feed more specific inputs - where things could start to get wrong
mmm(runif(100))

# UnExpected inputs
# Start passing really weird staff - explore
#   Passing a string
mmm("hi there")
#   Passing a Dataframe (strings and numeric)
mmm(gapminder)
#   Passing a Dataframe (numeric only)
#   Surprising Result -> Works but completely wrong
mmm(gapminder[c("lifeExp", "gdpPercap", "pop")])
#   Passing boolean
mmm(c(T,F,T,F))
```

The first lesson learned, exploring the usage of the MVP, is to __introduce assertions on the formal arguments__ and manage the exceptions (the `stopif` function can be used for this purpose).

```{r}
# Improvement - check validity of arguments
mmm <- function(x){
  # x must be a numeric vector
  stopifnot(is.numeric(x))
  return(max(x) - min(x))
}
```

If we start to explore the function as before (see below), we can see that unexpected behaviour has disappeared but the returned error message is not very meaningful for the user.

```{r}
# Informal Testing
# How does it work with different input
# Expected inputs - still working as before
mmm(1:20)
mmm(gapminder$lifeExp)

# UnExpected inputs
# should FAIL now (no unpredictable behaviours)
#   Passing a string
mmm("hi there")
#   Passing a Dataframe (strings and numeric)
mmm(gapminder)
#   Passing a Dataframe (numeric only)
#   Surprising Result -> Works but completely wrong
mmm(gapminder[c("lifeExp", "gdpPercap", "pop")])
#   Passing boolean
mmm(c(T,F,T,F))
```

We can see now that the error provided to the user is not meaningful. So the next improvement is __how can the shown error message be improved__? The `if-stop` pattern is a wonderful solution for managing errors and provide meaningful error messages back to the users, providing more valuable information about the issue. __Note__ using `.call = FALSE` simplifies the error message, a recommended practice when building packages.

```{r}
# Improvement - meaningful error message to the user
mmm <- function(x){
  if(!is.numeric(x)){
    # Add a custom error message
    stop("The passed parameter is not a numeric vector.")
  }
  return(max(x) - min(x))
}
```

```{r}
# Informal Testing
# How does it work with different input
# Expected inputs - still working as before
mmm(1:20)
mmm(gapminder$lifeExp)

# UnExpected inputs -
# should FAIL now (no unpredictable behaviours)
# provide a meaningful error to the user
#   Passing a string
mmm("hi there")
#   Passing a Dataframe (strings and numeric)
mmm(gapminder)
#   Passing a Dataframe (numeric only)
#   Surprising Result -> Works but completely wrong
mmm(gapminder[c("lifeExp", "gdpPercap", "pop")])
#   Passing boolean
mmm(c(T,F,T,F))
```

__Recap__

What have we done? We have

* wrote a function from "working"" top-level code
  * with formal validity checking of inputs
  * returning meaningful errors to the user
* performed informal testing

__We have a basic function that works and now we want to move to the next level__. The next level is to generalise the function to calculate the difference between two arbitrary quantiles for the provided numeric vector (formal parameter). Please note that such function is going to cover the previous case cause the max quantiles is 1.0 (the max value), the min quantile is 0.0 (the min value).
__Af first be open to explore different ways/ options to figure out how to solve the task at hand__ - these can be valuable for double checking the results (validity & correctness) later. One interesting function to be used is the `quantile` function.

```{r}
# Generalise -> calculate difference of 2 quantiles
# Play with some top level code in order to build up
# some more knowledge about what you want to achieve
# and how to achieve it.

# Idea - using quantile() function (see documentation)
# How does the quantile function work?
# Which arguments are available?
quantile(gapminder$lifeExp)
quantile(1:20)
quantile(gapminder$lifeExp, probs = 0.5)
quantile(gapminder$lifeExp, probs = c(0.25, 0.5, 0.75))

# Idea - use the boxplot function
# limitation: no custom quantiles
boxplot(gapminder$lifeExp, plot = F)$stats

# Idea - use the Iter Quantile
# limitation: no custom quantiles
IQR(gapminder$lifeExp)

# After having playing around for some time we can see that
# quantile() function is a good enough solution
# One possibility - use the following code to solve the problem at hand.
the_probs <- c(0.25, 0.75)
the_quantiles <- quantile(gapminder$lifeExp, probs = the_probs)
max(the_quantiles) - min(the_quantiles)
```

Next write the function to perform such calculation based on the knowledge built...

- define the function interface (arguments, default values, ...)
- __think-out-loud__ what could be wrong with the arguments?
- perform some basic testings, using __optional alternatives__ for verification

```{r}
# Version 1
qdiff1 <- function(x, probs){
  stopifnot(is.numeric((x)))  
  the_quantiles <- quantile(x, probs = probs)
  max(the_quantiles) - min(the_quantiles)
}

# Informal Testing
qdiff1(gapminder$lifeExp, probs=c(0.25, 0.75))
IQR(gapminder$lifeExp)

qdiff1(gapminder$gdpPercap, probs=c(0.25, 0.75))
IQR(gapminder$gdpPercap)
```

__Note__ especially when cutting and paste the exploratory code is a good practice to restart the R session as often as possible (RStudio).

## Good practices

### Argument Names

Names should be meaningful

- use `x` for the data
- reuse the same names for the arguments as the names in the used functions (within the function)

```{r}
# You can give any name to your parameters but remember they should be meaningful
# Argument names: freedom and conventions
# What is zeus? And hera? (no meaning)
qdiff2 <- function(zeus, hera) {
  stopifnot(is.numeric(zeus))
  the_quantiles <- quantile(x = zeus, probs = hera)
  max(the_quantiles) - min(the_quantiles)
}

qdiff2(zeus = gapminder$lifeExp, hera = 0:1)

# What is my_x? And my_probs? Why my_ (special meaning)?
qdiff3 <- function(my_x, my_probs) {
  stopifnot(is.numeric(my_x))
  the_quantiles <- quantile(x = my_x, probs = my_probs)
  max(the_quantiles) - min(the_quantiles)
}

qdiff3(my_x = gapminder$lifeExp, my_probs = 0:1)
```

### Returning

By default R returns the value connected with the latest statement executed in the function (not an assignment). Optionally the `return` function can be used. There is not a strong R convention or guidelines. Another interesting function is the `invisible` function is used to create an invisibility effect for the function.

### Set __default values__ for formal arguments

If not providing default values for the arguments, all arguments need to be passed to the function or the function fails. An alternative is to provide default values for some of the arguments so if the argument is given a value that value is used otherwise the default one is used.

```{r}
# Default values
# no prob parameter is given -> error
qdiff1(gapminder$lifeExp)

# Adding a default value for the
qdiff4 <- function(x, probs = c(0, 1)) {
  stopifnot(is.numeric(x))
  the_quantiles <- quantile(x, probs = probs)
  max(the_quantiles) - min(the_quantiles)
}

qdiff4(gapminder$lifeExp, c(0, 0.4, 0.7))
qdiff4(gapminder$lifeExp, c(0.7, 0.4))
```

### Validity check: more on that

Think out-of-the-box: __What else could we add for validity checks__? E.g.

* are the probs between 0 and 1?
* two probs?
* is probs numeric?
* are the probs in order?

## Deal with `NA`

What happens to our current function in the presence of NAs?

```{r}
z <- gapminder$lifeExp
z[3] <- NA
#quantile(z)
qdiff4(z)
```

For dealing with NAs there are two option,
- dealing with NAs by ourselves, e.g. with an ad-hoc formal parameters (using the same name as the one normally used) or
- using the `...`

__Use an ad-hoc formal parameter__...

```{r}
# The solution used in this function is not a viable/ user friendly solution
# cause the setting of the parameter is hidden from the user
qdiff5 <- function(x, probs = c(0, 1)) {
  stopifnot(is.numeric(x))
  the_quantiles <- quantile(x, probs = probs, na.rm = T)
  max(the_quantiles) - min(the_quantiles)
}

# A better approach is to use a formal parameter with a default setting
# qeustinable is which value the default should be
qdiff6 <- function(x, probs = c(0, 1), na.rm  = T) {
  stopifnot(is.numeric(x))
  the_quantiles <- quantile(x, probs = probs, na.rm = na.rm)
  max(the_quantiles) - min(the_quantiles)
}

z <- gapminder$lifeExp
z[3] <- NA
#quantile(z)
qdiff6(z)
qdiff6(z, na.rm = F) #Error should be thrown
```

### Use the `...` argument

`...` is a special argument and it is commonly used to take unspecified arguments and passing down to lower functions. It is just a way to manage something unknown without touching it and just passing down to the core function(s).

Be careful when the `...` is used for different functions within your function, especially when some parts of `...` go somewhere and other parts of `...` go somewhere else.

```{r}
qdiff7 <- function(x, probs = c(0, 1), na.rm  = T, ...) {
  stopifnot(is.numeric(x))
  the_quantiles <- quantile(x, probs = probs, na.rm = na.rm, ...)
  max(the_quantiles) - min(the_quantiles)
}

qdiff7(gapminder$lifeExp)
```

A used practice is to capture the `...` in a list, see example below...

```{r}
foo <- function(...){
  y <- list(...)
  y[1]
}

foo(a = 1, b = 2)
```

### Formal Unit Testing

When you write a function you should write a test harness around it - especially once a function is going to used by many people in different context. One package to be used is `testthat` for unit testing and TDD. There are also some mocking packages like `mockr`...

```{r}
library(testthat)

# Writing a simple test
test_that("invalid args are detected",{
  expect_error(qdiff7("eggplant"), "is.numeric\\(x\\) is not TRUE")
})

test_that("NA handling works", {
  expect_error(qdiff7(c(1:5, NA), na.rm = FALSE))
  expect_silent(qdiff7(c(1:5, NA), na.rm = TRUE))
  expect_equal(qdiff7(c(1:5, NA), na.rm = TRUE), 4)
})

# See example below for a function with a test failing
qdiff_no_NA <- function(x, probs = c(0, 1)) {
  stopifnot(is.numeric(x))
  the_quantiles <-
    quantile(x, probs = probs)
  max(the_quantiles) - min(the_quantiles)
}

test_that("NA handling works", {
  expect_equal(qdiff_no_NA(c(1:5, NA), na.rm = TRUE), 4)
  expect_equal(qdiff_no_NA(c(1:5, NA)), 4)
})
```

### Others

Some other things that you need to be aware

- vectorisations,
- the `apply` family of functions (especially when using non vectorised functions)
- non-standard evaluation,
- `glue` package is an interesting package for writing error messages,
- ...

## References

Some other useful references/ materials from Jenny Bryan

- [Github Profile](https://github.com/jennybc)  
- [Function-writing content in STAT545.com](http://stat545.com/block011_write-your-own-function-01.html)  
- [`purrr` tutorial](https://jennybc.github.io/purrr-tutorial)  

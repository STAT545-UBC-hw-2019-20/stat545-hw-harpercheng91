STAT545\_Assignment 1
================
Xiaolan Harper Cheng

-   [Introduction](#introduction)
-   [Data Analysis](#data-analysis)

Introduction
------------

In this R Markdown document, the dataset 'Gapminder' is briefly analyzed.

Data Analysis
-------------

Let us first load the dataset and explore the structure of it. It is described in the R documentation that 'gapminder' is an excerpt of the Gapminder data on life expectancy, GDP per capita, and population by country.

``` r
library(gapminder)
?gapminder
str(gapminder)
```

    ## Classes 'tbl_df', 'tbl' and 'data.frame':    1704 obs. of  6 variables:
    ##  $ country  : Factor w/ 142 levels "Afghanistan",..: 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ continent: Factor w/ 5 levels "Africa","Americas",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
    ##  $ lifeExp  : num  28.8 30.3 32 34 36.1 ...
    ##  $ pop      : int  8425333 9240934 10267083 11537966 13079460 14880372 12881816 13867957 16317921 22227415 ...
    ##  $ gdpPercap: num  779 821 853 836 740 ...

It is suggested by the internal structure that 'gapminder' is a data frame with 1704 observations and 6 variables which are country, continent, year, life expectancy, population, and GDP per capita.

Japanese people have the longest life expectancy, while Kuwait is the richest country.

``` r
gapminder$country[gapminder$lifeExp==max(gapminder$lifeExp)]
```

    ## [1] Japan
    ## 142 Levels: Afghanistan Albania Algeria Angola Argentina ... Zimbabwe

``` r
gapminder$country[gapminder$gdpPercap==max(gapminder$gdpPercap)]
```

    ## [1] Kuwait
    ## 142 Levels: Afghanistan Albania Algeria Angola Argentina ... Zimbabwe

Next, the relationship between life expectancy and GDP per capita are analyzed. The scatter plot indicates that people in wealthier countries generally have longer life spans.

``` r
attach(gapminder)
agg <- aggregate(gapminder, by=list(country), FUN=mean)
scatter.smooth(y=agg$lifeExp, x=agg$gdpPercap, 
               main="Relationship b/w life expectancy and GDP per capita", 
               ylab="Life Expectancy", xlab="GDP per capita")
```

![](hw01_gapminder_files/figure-markdown_github/unnamed-chunk-2-1.png)

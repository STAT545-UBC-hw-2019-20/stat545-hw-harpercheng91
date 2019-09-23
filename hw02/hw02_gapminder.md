STAT545 Homework 2
================
Xiaolan (Harper) Cheng

**In this document, ggplot2 and dplyr functionalities are explored.**

Load packages and dataset

``` r
suppressPackageStartupMessages(library(ggplot2))
suppressPackageStartupMessages(library(dplyr))
suppressPackageStartupMessages(library(gapminder))
```

Exercise 1: Basic `dplyr`
=========================

### 1.1 Use `filter()` to subset the `gapminder` data to three countries of your choice in the 1970’s.

``` r
(gapminder_subset <- gapminder %>% 
  filter(year < 1979 & year > 1970, 
         country=='Afghanistan' | country== 'Albania' | country == 'Algeria'))
```

    ## # A tibble: 6 x 6
    ##   country     continent  year lifeExp      pop gdpPercap
    ##   <fct>       <fct>     <int>   <dbl>    <int>     <dbl>
    ## 1 Afghanistan Asia       1972    36.1 13079460      740.
    ## 2 Afghanistan Asia       1977    38.4 14880372      786.
    ## 3 Albania     Europe     1972    67.7  2263554     3313.
    ## 4 Albania     Europe     1977    68.9  2509048     3533.
    ## 5 Algeria     Africa     1972    54.5 14760787     4183.
    ## 6 Algeria     Africa     1977    58.0 17152804     4910.

### 1.2 Use the pipe operator `%>%` to select “country” and “gdpPercap” from your filtered dataset in 1.1.

``` r
gapminder_subset %>% 
  select(country, gdpPercap)
```

    ## # A tibble: 6 x 2
    ##   country     gdpPercap
    ##   <fct>           <dbl>
    ## 1 Afghanistan      740.
    ## 2 Afghanistan      786.
    ## 3 Albania         3313.
    ## 4 Albania         3533.
    ## 5 Algeria         4183.
    ## 6 Algeria         4910.

### 1.3 Filter gapminder to all entries that have experienced a drop in life expectancy. Be sure to include a new variable that’s the increase in life expectancy in your tibble. Hint: you might find the `lag()` or `diff()` functions useful.

``` r
gapminder %>%
  mutate(lifeExp_gain = c(diff(gapminder$lifeExp), NA)) %>% 
  filter(lifeExp_gain < 0)
```

    ## # A tibble: 221 x 7
    ##    country   continent  year lifeExp      pop gdpPercap lifeExp_gain
    ##    <fct>     <fct>     <int>   <dbl>    <int>     <dbl>        <dbl>
    ##  1 Albania   Europe     1987    72    3075321     3739.       -0.419
    ##  2 Albania   Europe     2007    76.4  3600523     5937.      -33.3  
    ##  3 Algeria   Africa     2007    72.3 33333216     6223.      -42.3  
    ##  4 Angola    Africa     1982    39.9  7016384     2757.       -0.036
    ##  5 Argentina Americas   2007    75.3 40301927    12779.       -6.20 
    ##  6 Australia Oceania    2007    81.2 20434176    34435.      -14.4  
    ##  7 Austria   Europe     2007    79.8  8199783    36126.      -28.9  
    ##  8 Bahrain   Asia       2007    75.6   708573    29796.      -38.2  
    ##  9 Belgium   Europe     2007    79.4 10392226    33693.      -41.2  
    ## 10 Benin     Africa     1997    54.8  6066080     1233.       -0.371
    ## # … with 211 more rows

### 1.4 Filter gapminder so that it shows the max GDP per capita experienced by each country.

``` r
gapminder %>% 
  group_by(country) %>% 
  summarize(maxGDP = max(gdpPercap))
```

    ## # A tibble: 142 x 2
    ##    country     maxGDP
    ##    <fct>        <dbl>
    ##  1 Afghanistan   978.
    ##  2 Albania      5937.
    ##  3 Algeria      6223.
    ##  4 Angola       5523.
    ##  5 Argentina   12779.
    ##  6 Australia   34435.
    ##  7 Austria     36126.
    ##  8 Bahrain     29796.
    ##  9 Bangladesh   1391.
    ## 10 Belgium     33693.
    ## # … with 132 more rows

### 1.5 Produce a scatterplot of Canada’s life expectancy vs. GDP per capita using `ggplot2`, without defining a new variable. That is, after filtering the `gapminder` data set, pipe it directly into the `ggplot()` function. Ensure GDP per capita is on a log scale.

``` r
gapminder %>%
  filter(country == 'Canada') %>% 
  select(lifeExp, gdpPercap) %>% 
  ggplot(aes(x = gdpPercap, y = lifeExp)) +
    scale_x_log10(labels=scales::dollar_format()) +
      geom_point(alpha = 0.5) +
      labs(y="Life Expectancy", x="GDP per capita on a log10 scale")
```

![](hw02_gapminder_files/figure-markdown_github/1.5-1.png)

Exercise 2: Explore individual variables with `dplyr`
=====================================================

Pick one categorical variable (`continent`) and one quantitative variable (`gdpPercap`) to explore. Answer the following questions in whichever way you think is appropriate, using `dplyr`:

### 1. What are possible values (or range, whichever is appropriate) of each variable?

``` r
gapminder %>%
  group_by(continent) %>% 
  summarize(num_countries = n_distinct(country))
```

    ## # A tibble: 5 x 2
    ##   continent num_countries
    ##   <fct>             <int>
    ## 1 Africa               52
    ## 2 Americas             25
    ## 3 Asia                 33
    ## 4 Europe               30
    ## 5 Oceania               2

For the categorical variable `continent`, there are five values, namely 'Africa', 'Americas', 'Asia', 'Europe', and 'Oceania'. Each variable records countries that are geographically belong to that continent. The above table shows the number of countries that are sampled from each continent.

``` r
gapminder %>%
  group_by(continent) %>% 
  summarize(gdp_min = min(gdpPercap), gdp_max = max(gdpPercap))
```

    ## # A tibble: 5 x 3
    ##   continent gdp_min gdp_max
    ##   <fct>       <dbl>   <dbl>
    ## 1 Africa       241.  21951.
    ## 2 Americas    1202.  42952.
    ## 3 Asia         331  113523.
    ## 4 Europe       974.  49357.
    ## 5 Oceania    10040.  34435.

The ranges of quantitative variable `gdpPercap` for each continent are showed in the above table.

### 2. What values are typical? What’s the spread? What’s the distribution? Etc., tailored to the variable at hand.

There are 52 countries in Africa, making Africa the most frequent occurring continent in `gapminder`. Now, in terms of `pop` variable:

``` r
gapminder %>%
  ggplot(aes(x=gdpPercap)) +
  geom_histogram(aes(y=..density..), col='black', fill='lightblue', bins=200) +
  geom_density(alpha=0.5, col='red')
```

![](hw02_gapminder_files/figure-markdown_github/unnamed-chunk-2-1.png) This is a right skewed distribution, indicating that for most countries in most of the time, GDP per capita was relatively low. The probability density function graph demonstrates a wide spread of data, where a few countries in some period of time had extremely high GDP per capita compared to others.

Exercise 3: Explore various plot types
======================================

Make two plots that have some value to them.

``` r
gapminder %>%
  filter(country=='Cuba') %>% 
  ggplot(aes(x=lifeExp, y=gdpPercap)) +
    geom_point(aes(size=pop), col='cornflowerblue') +
      geom_text(aes(label=year), nudge_x = -1) +
        labs(title="A Scatterplot on GDP and Life Expectancy of Cuba ", 
             x="Life Expectancy", y="GDP per capita")
```

![](hw02_gapminder_files/figure-markdown_github/plots-1.png)

``` r
gapminder %>% 
  filter(country=='Cuba' | country=='Argentina' | country=='Brazil') %>% 
  ggplot(aes(x=year, y=lifeExp, col=country)) +
    geom_line(size=1.5) +
      labs(title='A Boxplot on Life Expectancy Changes of Three South America Countries', 
           x='Country', y='Life Expectancy')
```

![](hw02_gapminder_files/figure-markdown_github/plots-2.png)

Recycling
=========

Evaluate this code and describe the result. Presumably the analyst’s intent was to get the data for Rwanda and Afghanistan. Did they succeed? Why or why not? If not, what is the correct way to do this?

``` r
filter(gapminder, country == c("Rwanda", "Afghanistan"))
```

    ## # A tibble: 12 x 6
    ##    country     continent  year lifeExp      pop gdpPercap
    ##    <fct>       <fct>     <int>   <dbl>    <int>     <dbl>
    ##  1 Afghanistan Asia       1957    30.3  9240934      821.
    ##  2 Afghanistan Asia       1967    34.0 11537966      836.
    ##  3 Afghanistan Asia       1977    38.4 14880372      786.
    ##  4 Afghanistan Asia       1987    40.8 13867957      852.
    ##  5 Afghanistan Asia       1997    41.8 22227415      635.
    ##  6 Afghanistan Asia       2007    43.8 31889923      975.
    ##  7 Rwanda      Africa     1952    40    2534927      493.
    ##  8 Rwanda      Africa     1962    43    3051242      597.
    ##  9 Rwanda      Africa     1972    44.6  3992121      591.
    ## 10 Rwanda      Africa     1982    46.2  5507565      882.
    ## 11 Rwanda      Africa     1992    23.6  7290203      737.
    ## 12 Rwanda      Africa     2002    43.4  7852401      786.

They did NOT succeed in subsetting. The above code only managed to select half of the dataset that are supposed to be selected. The correct way to achieve the objective is as followed.

``` r
filter(gapminder, country=='Rwanda' | country=='Afghanistan')
```

    ## # A tibble: 24 x 6
    ##    country     continent  year lifeExp      pop gdpPercap
    ##    <fct>       <fct>     <int>   <dbl>    <int>     <dbl>
    ##  1 Afghanistan Asia       1952    28.8  8425333      779.
    ##  2 Afghanistan Asia       1957    30.3  9240934      821.
    ##  3 Afghanistan Asia       1962    32.0 10267083      853.
    ##  4 Afghanistan Asia       1967    34.0 11537966      836.
    ##  5 Afghanistan Asia       1972    36.1 13079460      740.
    ##  6 Afghanistan Asia       1977    38.4 14880372      786.
    ##  7 Afghanistan Asia       1982    39.9 12881816      978.
    ##  8 Afghanistan Asia       1987    40.8 13867957      852.
    ##  9 Afghanistan Asia       1992    41.7 16317921      649.
    ## 10 Afghanistan Asia       1997    41.8 22227415      635.
    ## # … with 14 more rows

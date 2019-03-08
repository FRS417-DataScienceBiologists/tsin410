---
title: "HW_6"
author: "Tiffany Sin"
date: "3/7/2019"
output: 
  html_document: 
    keep_md: yes
---


```

```r
library(tidyverse)
```

```
## ── Attaching packages ────────────────────────────────────────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──
```

```
## ✔ ggplot2 3.1.0       ✔ purrr   0.3.1  
## ✔ tibble  2.0.1       ✔ dplyr   0.8.0.1
## ✔ tidyr   0.8.3       ✔ stringr 1.4.0  
## ✔ readr   1.3.1       ✔ forcats 0.4.0
```

```
## ── Conflicts ───────────────────────────────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
```

```r
library(skimr)
```

```
## 
## Attaching package: 'skimr'
```

```
## The following object is masked from 'package:stats':
## 
##     filter
```

```r
library("RColorBrewer")
#install.packages("gapminder")
```


```r
library("gapminder")
```


```r
gapminder<-
  gapminder::gapminder
gapminder
```

```
## # A tibble: 1,704 x 6
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
## # … with 1,694 more rows
```
1. The data is tidy because each observation has its own row. 

```r
colnames(gapminder)##Below are the column names
```

```
## [1] "country"   "continent" "year"      "lifeExp"   "pop"       "gdpPercap"
```

```r
glimpse(gapminder)
```

```
## Observations: 1,704
## Variables: 6
## $ country   <fct> Afghanistan, Afghanistan, Afghanistan, Afghanistan, Af…
## $ continent <fct> Asia, Asia, Asia, Asia, Asia, Asia, Asia, Asia, Asia, …
## $ year      <int> 1952, 1957, 1962, 1967, 1972, 1977, 1982, 1987, 1992, …
## $ lifeExp   <dbl> 28.801, 30.332, 31.997, 34.020, 36.088, 38.438, 39.854…
## $ pop       <int> 8425333, 9240934, 10267083, 11537966, 13079460, 148803…
## $ gdpPercap <dbl> 779.4453, 820.8530, 853.1007, 836.1971, 739.9811, 786.…
```
##Dimensions: 1704 rows x 6 columns

```r
gapminder<-
  gapminder %>% 
na_if(-999)
```

```r
number_nas= sum(is.na(gapminder))
gapminder %>% 
purrr::map_df(~sum(is.na(.))) %>% 
  tidyr::gather(variables, number_nas) %>% 
  arrange(desc(number_nas))
```

```
## # A tibble: 6 x 2
##   variables number_nas
##   <chr>          <int>
## 1 country            0
## 2 continent          0
## 3 year               0
## 4 lifeExp            0
## 5 pop                0
## 6 gdpPercap          0
```
There are no NAs in gapfinder.
2.

```r
gapminder %>% 
  ggplot(aes(x=gdpPercap, y=lifeExp))+
  geom_point()+
  labs(title = "Life Expectancy vs. GDP",
       x="Per Capita GDP",
       Y="Life Expectancy")
```

![](HW_6_files/figure-html/unnamed-chunk-8-1.png)<!-- -->
3. There is a positive correlation between life expectancy and GDP. 
4.

```r
gapminder %>% 
  ggplot(aes(x = log10(gdpPercap), y=lifeExp)) +
  geom_point() +
   labs(title = "Life Expectancy vs GDP",
       x = "Per Capita GDP",
       y= "Life Expectancy")+
  facet_wrap(~year)
```

![](HW_6_files/figure-html/unnamed-chunk-9-1.png)<!-- -->
5.

```r
gapminder %>% 
  filter(year==1952 | year==2007) %>%
  ggplot(aes(x=log10(gdpPercap), y=lifeExp))+
  geom_point(color="salmon")+
  facet_wrap(~year)+
  theme_light()
```

![](HW_6_files/figure-html/unnamed-chunk-10-1.png)<!-- -->
6.

```r
gapminder %>%
  filter(year==1952 | year==2007) %>% 
  ggplot(aes(x=log10(gdpPercap), y=lifeExp, color=continent, size=pop))+
  geom_point()+
  facet_grid(~year)+
  scale_size(range = c(0.1, 10), guide = "none")
```

![](HW_6_files/figure-html/unnamed-chunk-11-1.png)<!-- -->
7.

```r
?theme_light
```
8.

```r
gapminder %>% 
  filter(continent=="Asia", year=="2007") %>% 
  ggplot(aes(x=country, y=pop, fill=country))+
  geom_bar(stat="identity")+
   labs(title = "China Population over the Years",
       x = "Popualtion",
       y= "Country")+
  coord_flip()
```

![](HW_6_files/figure-html/unnamed-chunk-13-1.png)<!-- -->
9.

```r
gapminder %>% 
  filter(country=="China") %>% 
   ggplot(aes(x=year, y=pop))+
  geom_bar(stat = "identity")+
  labs(title = "China Population over the Years",
       x = "Year",
       y= "Population")+
  ylim(0, 1.5E+09)
```

![](HW_6_files/figure-html/unnamed-chunk-14-1.png)<!-- -->
The bar plot above shows how China's population is increasing as the years increase.
10.

```r
gapminder %>% 
  filter(country=="China" | country=="India") %>% 
  ggplot(aes(x=year, y=pop, fill=country))+
    geom_bar(stat="identity", position="dodge")+
  scale_fill_brewer(palette = "Dark2")
```

![](HW_6_files/figure-html/unnamed-chunk-15-1.png)<!-- -->


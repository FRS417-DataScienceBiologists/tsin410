---
title: "Lab 5 Homework"
author: "Tiffany Sin"
date: "Winter 2019"
output:
  html_document:
    keep_md: yes
    theme: spacelab
---


##SKIP 8-10. DO 1-7
## Instructions
Answer the following questions and complete the exercises in RMarkdown. Please embed all of your code, keep track of your versions using git, and push your final work to our [GitHub repository](https://github.com/FRS417-DataScienceBiologists). I will randomly select a few examples of student work at the start of each session to use as examples so be sure that your code is working to the best of your ability.

## Load the tidyverse

```r
library(tidyverse)
```

## Mammals Life History
Let's revisit the mammal life history data to practice our `ggplot` skills. Some of the tidy steps will be a repeat from the homework, but it is good practice. The [data](http://esapubs.org/archive/ecol/E084/093/) are from: *S. K. Morgan Ernest. 2003. Life history characteristics of placental non-volant mammals. Ecology 84:3402.*

```r
library(tidyverse)
```

1. Load the data.


```r
mammals_lifehistory <- readr::read_csv("mammal_lifehistories_v2.csv")
```

```
## Parsed with column specification:
## cols(
##   order = col_character(),
##   family = col_character(),
##   Genus = col_character(),
##   species = col_character(),
##   mass = col_double(),
##   gestation = col_double(),
##   newborn = col_double(),
##   weaning = col_double(),
##   `wean mass` = col_double(),
##   AFR = col_double(),
##   `max. life` = col_double(),
##   `litter size` = col_double(),
##   `litters/year` = col_double()
## )
```

2. Use your preferred function to have a look. Do you notice any problems?

```r
mammals_lifehistory
```

```
## # A tibble: 1,440 x 13
##    order family Genus species   mass gestation newborn weaning `wean mass`
##    <chr> <chr>  <chr> <chr>    <dbl>     <dbl>   <dbl>   <dbl>       <dbl>
##  1 Arti… Antil… Anti… americ… 4.54e4      8.13   3246.    3           8900
##  2 Arti… Bovid… Addax nasoma… 1.82e5      9.39   5480     6.5         -999
##  3 Arti… Bovid… Aepy… melamp… 4.15e4      6.35   5093     5.63       15900
##  4 Arti… Bovid… Alce… busela… 1.50e5      7.9   10167.    6.5         -999
##  5 Arti… Bovid… Ammo… clarkei 2.85e4      6.8    -999  -999           -999
##  6 Arti… Bovid… Ammo… lervia  5.55e4      5.08   3810     4           -999
##  7 Arti… Bovid… Anti… marsup… 3.00e4      5.72   3910     4.04        -999
##  8 Arti… Bovid… Anti… cervic… 3.75e4      5.5    3846     2.13        -999
##  9 Arti… Bovid… Bison bison   4.98e5      8.93  20000    10.7       157500
## 10 Arti… Bovid… Bison bonasus 5.00e5      9.14  23000.    6.6         -999
## # … with 1,430 more rows, and 4 more variables: AFR <dbl>, `max.
## #   life` <dbl>, `litter size` <dbl>, `litters/year` <dbl>
```
##Yes. There are NAs throughout the data.

3. There are NA's. How are you going to deal with them?

```r
mammals_lifehistory %>% 
  summarize(number_nas= sum(is.na(mammals_lifehistory)))
```

```
## # A tibble: 1 x 1
##   number_nas
##        <int>
## 1          0
```

```r
mammals_lifehistory<-
  mammals_lifehistory %>% 
  na_if(-999.0)
mammals_lifehistory
```

```
## # A tibble: 1,440 x 13
##    order family Genus species   mass gestation newborn weaning `wean mass`
##    <chr> <chr>  <chr> <chr>    <dbl>     <dbl>   <dbl>   <dbl>       <dbl>
##  1 Arti… Antil… Anti… americ… 4.54e4      8.13   3246.    3           8900
##  2 Arti… Bovid… Addax nasoma… 1.82e5      9.39   5480     6.5           NA
##  3 Arti… Bovid… Aepy… melamp… 4.15e4      6.35   5093     5.63       15900
##  4 Arti… Bovid… Alce… busela… 1.50e5      7.9   10167.    6.5           NA
##  5 Arti… Bovid… Ammo… clarkei 2.85e4      6.8      NA    NA             NA
##  6 Arti… Bovid… Ammo… lervia  5.55e4      5.08   3810     4             NA
##  7 Arti… Bovid… Anti… marsup… 3.00e4      5.72   3910     4.04          NA
##  8 Arti… Bovid… Anti… cervic… 3.75e4      5.5    3846     2.13          NA
##  9 Arti… Bovid… Bison bison   4.98e5      8.93  20000    10.7       157500
## 10 Arti… Bovid… Bison bonasus 5.00e5      9.14  23000.    6.6           NA
## # … with 1,430 more rows, and 4 more variables: AFR <dbl>, `max.
## #   life` <dbl>, `litter size` <dbl>, `litters/year` <dbl>
```

```r
library("skimr")
mammals_lifehistory %>% 
  skimr::skim()
```

```
## Skim summary statistics
##  n obs: 1440 
##  n variables: 13 
## 
## ── Variable type:character ────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
##  variable missing complete    n min max empty n_unique
##    family       0     1440 1440   6  15     0       96
##     Genus       0     1440 1440   3  16     0      618
##     order       0     1440 1440   7  14     0       17
##   species       0     1440 1440   3  17     0     1191
## 
## ── Variable type:numeric ──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
##      variable missing complete    n      mean         sd    p0   p25
##           AFR     607      833 1440     22.44      26.45  0.7   4.5 
##     gestation     418     1022 1440      3.86       3.62  0.49  0.99
##   litter size      84     1356 1440      2.8        1.77  1     1.02
##  litters/year     689      751 1440      1.64       1.17  0.14  1   
##          mass      85     1355 1440 407701.39 5210474.99  2.1  61.15
##     max. life     841      599 1440    224.03     189.74 12    84   
##       newborn     595      845 1440  12126.55  118408.21  0.21  4.4 
##     wean mass    1039      401 1440  60220.5   953857.17  2.09 20.15
##       weaning     619      821 1440      3.97       5.38  0.3   0.92
##     p50     p75          p100     hist
##   12      28.24     210       ▇▂▁▁▁▁▁▁
##    2.11    6         21.46    ▇▂▂▁▁▁▁▁
##    2.5     4         14.18    ▇▃▂▁▁▁▁▁
##    1       2          7.5     ▇▂▃▁▁▁▁▁
##  606    8554          1.5e+08 ▇▁▁▁▁▁▁▁
##  192     288       1368       ▇▆▂▁▁▁▁▁
##   43.7   542.5  2250000       ▇▁▁▁▁▁▁▁
##  102.6  2000          1.9e+07 ▇▁▁▁▁▁▁▁
##    1.69    4.84      48       ▇▁▁▁▁▁▁▁
```


4. Where are the NA's? This is important to keep in mind as you build plots.
##They aew AFR, gestation, litter size, litters/year, mass, max life, newborns, wean mass, and weaning

5. Some of the variable names will be problematic. Let's rename them here as a final tidy step.

rename(
          genus        = Genus,
          wean_mass    = `wean mass`,
          max_life     = `max. life`,
          litter_size  = `litter size`,
          litters_yr   = `litters/year`
          )

##`ggplot()`
For the questions below, try to use the aesthetics you have learned to make visually appealing and informative plots. Make sure to include labels for the axes and titles.

```r
options(scipen=999) #cancels the use of scientific notation for the session
```

6. What is the relationship between newborn body mass and gestation? Make a scatterplot that shows this relationship. 

```r
names(mammals_lifehistory)
```

```
##  [1] "order"        "family"       "Genus"        "species"     
##  [5] "mass"         "gestation"    "newborn"      "weaning"     
##  [9] "wean mass"    "AFR"          "max. life"    "litter size" 
## [13] "litters/year"
```


```r
mammals_lifehistory %>% 
  ggplot(aes(x=newborn, y=gestation))+
  geom_point()
```

```
## Warning: Removed 673 rows containing missing values (geom_point).
```

![](lab5_hw_rev_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

7. You should notice that because of the outliers in newborn mass, we need to make some changes. We didn't talk about this in lab, but you can use `scale_x_log10()` as a layer to correct for this issue. This will log transform the y-axis values.

```r
mammals_lifehistory %>% 
  ggplot(aes(x=newborn, y=gestation))+
  scale_x_log10()+
  geom_point()
```

```
## Warning: Removed 673 rows containing missing values (geom_point).
```

![](lab5_hw_rev_files/figure-html/unnamed-chunk-10-1.png)<!-- -->


8. Now that you have the basic plot, color the points by taxonomic order.

```r
mammals_lifehistory %>% 
  ggplot(aes(x=newborn, y=gestation, color=order))+
  scale_x_log10()+
  geom_point()
```

```
## Warning: Removed 673 rows containing missing values (geom_point).
```

![](lab5_hw_rev_files/figure-html/unnamed-chunk-11-1.png)<!-- -->


9. Lastly, make the size of the points proportional to body mass.

```r
mammals_lifehistory %>% 
  ggplot(aes(x=newborn, y=gestation, color=order, size=mass))+
  scale_x_log10()+
  geom_point()
```

```
## Warning: Removed 691 rows containing missing values (geom_point).
```

![](lab5_hw_rev_files/figure-html/unnamed-chunk-12-1.png)<!-- -->



10. Make a plot that shows the range of lifespan by order.

```r
mammals_lifehistory %>% 
  ggplot(aes(x=order, y=`max. life`))+
  geom_boxplot()+
  coord_flip()
```

```
## Warning: Removed 841 rows containing non-finite values (stat_boxplot).
```

![](lab5_hw_rev_files/figure-html/unnamed-chunk-13-1.png)<!-- -->


## Push your final code to [GitHub](https://github.com/FRS417-DataScienceBiologists)
Make sure that you push your code into the appropriate folder. Also, be sure that you have check the `keep md` file in the knit preferences.

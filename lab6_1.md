---
title: "Visualizing Data 3"
author: "Joel Ledford"
date: "Winter 2019"
output:
  html_document:
    keep_md: yes
    theme: spacelab
    toc: yes
    toc_float: yes
  pdf_document:
    toc: yes
---

## Resources
- [ggplot2 cheatsheet](https://www.rstudio.com/wp-content/uploads/2015/03/ggplot2-cheatsheet.pdf)
- [R for Data Science](https://r4ds.had.co.nz/)
- [R Cookbook](http://www.cookbook-r.com/)

## Learning Goals
*At the end of this exercise, you will be able to:*    
1. Build stacked barplots of categorical variables.  
2. Build side-by-side barplots using `position= "dodge"`.  
3. Customize colors in plots using `RColorBrewer` and `paletteer`.  
4. Build histograms and density plots.  

## Load the libraries

```r
library(tidyverse)
```


```r
options(scipen=999) #cancels the use of scientific notation for the session
```

## Data
**Database of vertebrate home range sizes.**  
Reference: Tamburello N, Cote IM, Dulvy NK (2015) Energy and the scaling of animal space use. The American Naturalist 186(2):196-211. http://dx.doi.org/10.1086/682070.  
Data: http://datadryad.org/resource/doi:10.5061/dryad.q5j65/1  


```r
homerange <- 
  readr::read_csv("data/Tamburelloetal_HomeRangeDatabase.csv", na = c("", " ", "NA", "#N/A", "-999", "\\"))
```

## Barplots revisited
At this point you should be able to build a barplot that shows counts of observations in a variable using `geom_bar()`. But, you should also be able to use `stat="identity"` to specify both x and y axes.  

Although we did not use it last time, `geom_col()` is the same as specifying `stat="identity"` using `geom_bar()`.     

## Here is the plot using `geom_bar(stat="identity")`

```r
homerange %>% 
  filter(family=="salmonidae") %>% #filter for salmonid fish only
  ggplot(aes(x=common.name, y=log10.mass))+
  geom_bar(stat="identity")+
  scale_fill_brewer(palette="Purples")
```

![](lab6_1_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

## Practice
1. Make the same plot above, but use `geom_col()`

```r
homerange %>% 
  filter(family=="salmonidae") %>% #filter for salmonid fish only
  ggplot(aes(x=common.name, y=log10.mass, fill=common.name))+
  geom_col()
```

![](lab6_1_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

## Barplots and multiple variables
Last time we explored the `fill` option in boxplots as a way to bring color to the plot; i.e. we filled by the same variable that we were plotting. What happens when we fill by a different categorical variable? 

Let's start by counting how many obervations we have in each taxonomic group.

```r
homerange %>% 
  count(taxon)
```

```
## # A tibble: 9 x 2
##   taxon             n
##   <chr>         <int>
## 1 birds           140
## 2 lake fishes       9
## 3 lizards          11
## 4 mammals         238
## 5 marine fishes    90
## 6 river fishes     14
## 7 snakes           41
## 8 tortoises        12
## 9 turtles          14
```

Now let's make a barplot of these data.

```r
homerange %>% 
  ggplot(aes(x=taxon, fill=taxon))+
  geom_bar(alpha=0.9, na.rm=TRUE)+
  coord_flip()+
  labs(title = "Observations by Taxon in Homerange Data",
       x = "Taxonomic Group")
```

![](lab6_1_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

By specifying `fill=trophic.guild` we build a stacked bar plot that shows the proportion of a given taxonomic group that is an herbivore or carnivore.

```r
homerange %>% 
  ggplot(aes(x=taxon, fill=trophic.guild))+
  geom_bar(alpha=0.9, na.rm=T)+
  coord_flip()+
  labs(title = "Observations by Taxon in Homerange Data",
       x = "Taxonomic Group",
       fill= "Trophic Guild")
```

![](lab6_1_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

We can also have the proportions of each trophic guild within taxonomic group shown side-by-side by specifying `position="dodge"`.

```r
homerange %>% 
  ggplot(aes(x=taxon, fill=trophic.guild))+
  geom_bar(alpha=0.9, na.rm=T, position="dodge")+
  theme(legend.position = "right",
        axis.text.x = element_text(angle = 60, hjust=1))+
  labs(title = "Observations by Taxon in Homerange Data",
       x = "Taxonomic Group",
       fill= "Trophic Guild")
```

![](lab6_1_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

## Practice
1. Make a barplot that shows locomotion type by `primarymethod`. Try both a stacked barplot and `position="dodge"`.

```r
homerange %>% 
  ggplot(aes(x=locomotion, fill=primarymethod))+
  geom_bar( position="dodge")+
  theme(legend.position = "right",
        axis.text.x = element_text(angle = 60, hjust=1))+
  labs(title = "Observations by Taxon in Homerange Data",
       x = "Locomotion Type",
       fill= "Primary Method")
```

![](lab6_1_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

```r
homerange %>% 
  ggplot(aes(x=locomotion, fill=primarymethod))+
  geom_bar()+
  coord_flip()+
  labs(title = "Observations by Taxon in Homerange Data",
       x = "Locomotion Type",
       fill= "Primary Method")
```

![](lab6_1_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

```r
homerange %>% 
  ggplot(aes(x=locomotion, fill=primarymethod))+
  geom_bar(position=position_fill(), stat="count")+
  scale_y_continuous(labels=scales::percent)
```

![](lab6_1_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

```r
##reflective of proportions of data type
```

## Histograms and Density Plots
Histograms are frequently used by biologists; they show the distribution of continuous variables. As students, you almost certainly have seen histograms of grade distributions. Without getting into the statistics, a histogram `bins` the data and you specify the number of bins that encompass a range of observations. For something like grades, this is easy because the number of bins corresponds to the grades A-F. By default, R uses a formula to calculate the number of bins but some adjustment is often required.  

What does the distribution of body mass look like in the homerange data?

```r
homerange %>% 
  ggplot(aes(x=log10.mass)) +
  geom_histogram(fill="steelblue", alpha=0.8, color="black")+
  labs(title = "Distribution of Body Mass")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](lab6_1_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

`Density plots` are similar to histograms but they use a smoothing function to make the distribution more even and clean looking. They do not use bins.

```r
homerange %>% 
  ggplot(aes(x=log10.mass)) +
  geom_density(fill="steelblue", alpha=0.4)+
  labs(title = "Distribution of Body Mass")
```

![](lab6_1_files/figure-html/unnamed-chunk-14-1.png)<!-- -->

I like to see both the histogram and the density curve so I often plot them together. Note that I assign the density plot a different color.

```r
homerange %>% 
  ggplot(aes(x=log10.mass)) +
  geom_histogram(aes(y = ..density..), binwidth = .4, fill="steelblue", alpha=0.8, color="black")+
  geom_density(color="red")+
  labs(title = "Distribution of Body Mass")
```

![](lab6_1_files/figure-html/unnamed-chunk-15-1.png)<!-- -->

## Practice
1. Make a histogram of `log10.hra`. Make sure to add a title.

```r
homerange %>% 
  ggplot(aes(x=log10.hra))+
  geom_histogram(fill="violet", alpha=0.8, color="black")+
  labs(title="Distribution of HRA")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](lab6_1_files/figure-html/unnamed-chunk-16-1.png)<!-- -->

2. Now plot the same variable using `geom_density()`.

```r
homerange %>% 
  ggplot(aes(x=log10.hra))+
  geom_density(fill="salmon", alpha=0.8)+
  labs(title="Distribution of HRA")
```

![](lab6_1_files/figure-html/unnamed-chunk-17-1.png)<!-- -->

3. Combine them both!

```r
homerange %>% 
  ggplot(aes(x=log10.hra)) +
  geom_histogram(aes(y = ..density..), binwidth = .4, fill="skyblue", alpha=0.8, color="black")+
  geom_density(color="salmon")+
  labs(title = "Distribution of Body Mass")
```

![](lab6_1_files/figure-html/unnamed-chunk-18-1.png)<!-- -->

## That's it, let's take a break!   

--> On to [part 2](https://jmledford3115.github.io/datascibiol/lab5_2.html)  

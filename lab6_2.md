---
title: "Visualizing Data 4"
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
- [R ColorBrewer](http://colorbrewer2.org/#type=sequential&scheme=BuGn&n=3)

## Learning Goals
*At the end of this exercise, you will be able to:*    
1. Adjust x and y axis limits on plots.  
2. Use faceting to display plots for multiple variables simultaneously.
3. Use RColorBrewer to specify a custom color theme.

## Load the tidyverse

```r
library(tidyverse)
options(scipen=999)
```

## Data
**Database of vertebrate home range sizes.**  
Reference: Tamburello N, Cote IM, Dulvy NK (2015) Energy and the scaling of animal space use. The American Naturalist 186(2):196-211. http://dx.doi.org/10.1086/682070.  
Data: http://datadryad.org/resource/doi:10.5061/dryad.q5j65/1  

```r
homerange <- 
  readr::read_csv("data/Tamburelloetal_HomeRangeDatabase.csv", na = c("", " ", "NA", "#N/A", "-999", "\\"))
```

```
## Parsed with column specification:
## cols(
##   .default = col_character(),
##   mean.mass.g = col_double(),
##   log10.mass = col_double(),
##   mean.hra.m2 = col_double(),
##   log10.hra = col_double(),
##   preymass = col_double(),
##   log10.preymass = col_double(),
##   PPMR = col_double()
## )
```

```
## See spec(...) for full column specifications.
```

## Review color and fill options
We have learned that there are a variety of color and fill options in `ggplot`. These are helpful in making plots more informative and visually appealing; i.e., in this scatterplot we show the relationship between mass and homerange while coloring the points by a different variable.

```r
homerange %>% 
  ggplot(aes(x=log10.mass, y=log10.hra, color=locomotion))+
  geom_point()
```

![](lab6_2_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

We can change the color of the points universally instead of using `fill`, but we need to put this into a different layer and specify the exact color. Notice the different options! Experiment by adjusting them.

```r
homerange %>% 
  ggplot(aes(x=log10.mass, y=log10.hra))+
  geom_point(shape = 21, colour = "black", fill = "steelblue", size = 2.5, stroke = 0.5, alpha=0.75)##21=circle,22=square,23=diamond
```

![](lab6_2_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

## Practice
1. Make a barplot that shows counts of animals by taxonomic class. Fill by thermoregulation type.

```r
homerange %>% 
  ggplot(aes(x=class, fill=thermoregulation))+
  geom_bar()
```

![](lab6_2_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

2. Make a box plot that shows the range of log10.mass by taxonomic class. Use `group` to cluster animals together by taxon and `fill` to color each box by taxon.

```r
homerange %>% 
  ggplot(aes(x=class, y=log10.mass, group=taxon, fill=taxon))+
  geom_boxplot()##plotting class of animal by mass, group by taxon
```

![](lab6_2_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

## Adjusting the x and y limits
There are many options for adjusting the x and y axes. For details, look over examples in the [R Cookbook](http://www.cookbook-r.com/). To adjust limits, we can use the `xlim` and `ylim` commands. When you do this, any data outside the specified ranges are not plotted.

```r
homerange %>% 
  ggplot(aes(x=log10.mass, y=log10.hra, color=locomotion))+
  geom_point()+
  xlim(0, 4)+
  ylim(1, 6)
```

```
## Warning: Removed 175 rows containing missing values (geom_point).
```

![](lab6_2_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

## Faceting: `facet_wrap()`
Faceting is one of the most amazing features of ggplot. It allows you to make multi-panel plots for easy comparison. Here, I am making histograms of log10.mass for every taxon. We read the `~` in the `facet_wrap()` layer as `by`.

```r
homerange %>% 
  ggplot(aes(x = log10.mass)) +
  geom_histogram() +
  facet_wrap(~taxon)##~=by. facet_wrap("by"taxon). Lots of different categories
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](lab6_2_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

## Faceting: `facet_grid()`
As you can imagine, there are lots of options for faceting. Another useful facet type is `facet_grid`. This can be helpful when you facet by only a few variables.

```r
homerange %>% 
  ggplot(aes(x = log10.mass)) +
  geom_histogram() +
  facet_grid(~thermoregulation) +
  scale_fill_brewer(palette = "Dark2") ##Good for few categories.Compare side by side.
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](lab6_2_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

## Practice
1. Use faceting to produce density distributions of log10.mass by taxonomic class.

```r
homerange %>% 
  ggplot(aes(x=log10.mass))+
  geom_density(fill="steelblue", alpha=0.4)+
  geom_histogram(fill="royal blue")+
  facet_wrap(~thermoregulation)+
  facet_grid(~class)+
  scale_colour_brewer(palette = "Dark2")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](lab6_2_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

## RColorBrewer
The default color palettes used by ggplot are often uninspiring. They don't make plots pop out in presentations or publications, and you may want to use a customized palette to make things visually consistent.

```r
#install.packages("RColorBrewer")
library("RColorBrewer")
```

Access the help for RColor Brewer.

```r
?RColorBrewer
```

The key thing to notice is that there are three different palettes: 1) sequential, 2) diverging, and 3) qualitative. Within each of these there are several selections. You can bring up the colors by using `display.brewer.pal()`. Specify the number of colors that you want and the palette name.

```r
display.brewer.pal(8,"BrBG")
```

![](lab6_2_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

The [R Color Brewer](http://colorbrewer2.org/#type=sequential&scheme=BuGn&n=3) website is very helpful for getting an idea of the color palettes. To make things easy, use these two guidelines:

+`scale_colour_brewer()` is for points  
+`scale_fill_brewer()` is for fills  


```r
homerange %>% 
  ggplot(aes(x=log10.mass, y=log10.hra, color=locomotion))+
  geom_point(size=1.5, alpha=0.8)+
  scale_colour_brewer(palette = "Dark2")
```

![](lab6_2_files/figure-html/unnamed-chunk-14-1.png)<!-- -->


```r
homerange %>% 
  ggplot(aes(x=thermoregulation, fill=thermoregulation))+
  geom_bar()+
  labs(title = "Thermoregulation Counts",
       x = "Thermoregulation Type",
       y = "# Individuals")+ 
  theme(plot.title = element_text(size = rel(1.25)))+
  scale_fill_brewer(palette="Dark2")
```

![](lab6_2_files/figure-html/unnamed-chunk-15-1.png)<!-- -->

## Practice
1. Build a dodged bar plot that shows the number of carnivores and herbivores filled by taxonomic class. Use a custom color theme.

```r
homerange %>%
  ggplot(aes(x=trophic.guild, fill=class)) +
  geom_bar(alpha=0.9, position="dodge")+
  scale_fill_brewer(palette="Dark2")
```

![](lab6_2_files/figure-html/unnamed-chunk-16-1.png)<!-- -->

```r
#install.packages("paletteer")
```

```r
library(paletteer)
```


```r
colors<-
  paletteer::palettes_d_names
```


```r
my_palette<-
  paletteer_d(package="ggsci", palette="springfield_simpsons")
barplot(rep(4, 14), axes=FALSE, col=my_palette)
```

![](lab6_2_files/figure-html/unnamed-chunk-20-1.png)<!-- -->

## Wrap-up
Please review the learning goals and be sure to use the code here as a reference when completing the homework.

-->[Home](https://jmledford3115.github.io/datascibiol/)

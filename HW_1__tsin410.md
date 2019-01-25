---
title: "HW_1_tsin410"
author: "Tiffany Sin"
date: "1/18/2019"
output: 
  html_document: 
    keep_md: yes
---

```r
5-3*2
```

```
## [1] -1
```

```r
8/2**2
```

```
## [1] 2
```

```r
(5-3)*2
```

```
## [1] 4
```

```r
(8/2)**2
```

```
## [1] 16
```

```r
blackjack <- c(140, -20, 70, -120, 240)
```

```r
roulette <- c(60, 50, 120, -300, 10)
```

```r
days <- c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday")
```

```r
names(blackjack) <- days
```

```r
names(roulette) <-days
```

```r
total_blackjack <- sum(blackjack)
total_blackjack
```

```
## [1] 310
```
## Won $310 in blackjack over the week.

```r
total_roulette <-sum(roulette)
total_roulette
```

```
## [1] -60
```
##Lost $60 in roulette over the week
```

```r
total_week <-(blackjack + roulette)
total_week
```

```
##    Monday   Tuesday Wednesday  Thursday    Friday 
##       200        30       190      -420       250
```
##Thursday I lost the most. Friday I won the most.
```

```r
total_blackjack > 0
```

```
## [1] TRUE
```

```r
total_roulette > 0 
```

```
## [1] FALSE
```

##Should stick with blackjack because you won $310 compared to roulette which resulted in a loss of $60. 310 > -60.


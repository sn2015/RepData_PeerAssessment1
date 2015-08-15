---
title: "Reproducible Research: Peer Assessment 1"
author: "Sergey Nazarov"
date: "Wednesday, August 12, 2015"
output: html_document
---

## Loading and preprocessing the data


```r
unzip("./repdata-data-activity.zip")
data <- read.csv("./activity.csv")
```

looking at data

```r
summary(data)
head(data)
str(data)
```

## What is mean total number of steps taken per day?

here i aggregate step numbers by date, which i will use later in histogram

```r
aggregate(steps ~ date, data = data, sum)
```

making histogram steps by day. at first look it looks like normal distribution just
with more thick first half

```r
hist(aggregate(steps ~ date, data = data, sum)$steps, main = "Histogram of steps by day",
     xlab = "number of steps")
```

![plot of chunk histogram](figure/histogram-1.png) 

here we can look at histogram more closely, using "breaks", and we see something
interseting - days with extra activity (more than 20000 steps)

```r
hist(aggregate(steps ~ date, data = data, sum)$steps, breaks = 10, main = "Histogram of steps by day",
     xlab = "number of steps")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

### Mean number of steps by day

```r
mean(aggregate(steps ~ date, data = data, sum)$steps)
```

```
## [1] 10766.19
```

### Median number of steps by day

```r
median(aggregate(steps ~ date, data = data, sum)$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?


```r
dailypattern <- aggregate(steps ~ interval, data = data, mean)
plot(dailypattern, type = "l")
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png) 

the maximum number of steps contains interval # 835 (on average across all the days in the dataset)

```r
dailypattern[dailypattern$steps == max(dailypattern$steps), ]
```

```
##     interval    steps
## 104      835 206.1698
```


## Imputing missing values



## Are there differences in activity patterns between weekdays and weekends?







### Version information

```r
sessionInfo()
```

```
## R version 3.1.3 (2015-03-09)
## Platform: x86_64-w64-mingw32/x64 (64-bit)
## Running under: Windows 8 x64 (build 9200)
## 
## locale:
## [1] LC_COLLATE=Russian_Russia.1251  LC_CTYPE=Russian_Russia.1251   
## [3] LC_MONETARY=Russian_Russia.1251 LC_NUMERIC=C                   
## [5] LC_TIME=Russian_Russia.1251    
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
## [1] knitr_1.9
## 
## loaded via a namespace (and not attached):
## [1] digest_0.6.8    evaluate_0.5.5  formatR_1.0     htmltools_0.2.6
## [5] rmarkdown_0.5.1 stringr_0.6.2   tools_3.1.3     yaml_2.1.13
```

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

we can look at histogram, using "breaks" = 10, and we see something
interseting - some days with extra activity (more than 20000 steps)

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

finding maximum number of steps

```r
dailypattern[dailypattern$steps == max(dailypattern$steps), ]
```

```
##     interval    steps
## 104      835 206.1698
```
the maximum number of steps contains interval # 835 (on average across all the days in the dataset)
it's actually 8:35 AM - jogging or hurrying to work? :)

## Imputing missing values
lets see how many missing values are in the dataset

```r
sum(is.na(data$steps))
```

```
## [1] 2304
```

```r
sum(is.na(data$steps))/nrow(data)
```

```
## [1] 0.1311475
```
near 13% is missing

for this task i create a separate data frame *"data without NA"* in which i fill NA values
using the mean for the correspondent 5-minute interval

```r
datawithoutNA <- data

for (i in 1:nrow(datawithoutNA)) {
        if (is.na(datawithoutNA$steps[i]) == TRUE) {
                datawithoutNA$steps[i] <- dailypattern$steps[dailypattern$interval == datawithoutNA$interval[i]]
        }
}
```
just checking there is no NA's

```r
sum(is.na(datawithoutNA$steps)) 
```

```
## [1] 0
```

here i aggregate step numbers by date, which i will use later in histogram2

```r
aggregate(steps ~ date, data = datawithoutNA, sum)
```

here we can look at histogram

```r
hist(aggregate(steps ~ date, data = datawithoutNA, sum)$steps, breaks = 10, main = "Histogram of steps by day",
     xlab = "number of steps")
```

![plot of chunk unnamed-chunk-13](figure/unnamed-chunk-13-1.png) 

### Mean number of steps by day without NA

```r
mean(aggregate(steps ~ date, data = datawithoutNA, sum)$steps)
```

```
## [1] 10766.19
```

### Median number of steps by day without NA

```r
median(aggregate(steps ~ date, data = datawithoutNA, sum)$steps)
```

```
## [1] 10766.19
```
### the is no difference between mean number of steps with and without NA


## Are there differences in activity patterns between weekdays and weekends?
first I create a new dataset using data without missing values and transform date from factor to time 

```r
dataweek <- datawithoutNA
dataweek$date <- strptime(dataweek$date, "%Y-%m-%d")
```
then I add a new variable using weekdays() function

```r
Sys.setlocale("LC_TIME", "English")
```

```
## [1] "English_United States.1252"
```

```r
dataweek$day <- weekdays(dataweek$date, abbreviate = FALSE)
```
then I transform weekay names into two variables: weekend and weekday 

```r
for (i in 1:nrow(dataweek)) {
        if (dataweek$day[i] == "Saturday" | dataweek$day[i] == "Sunday") {
                dataweek$day[i] <- "weekend"
        } else {dataweek$day[i] <- "weekday"}
}
```
Here I create two separate datasets (weekdays and weekend) with patterns on average 
across all the days in each dataset

```r
weekendpattern <- aggregate(steps ~ interval, data = dataweek[dataweek$day == "weekend",], mean)
weekdaypattern <- aggregate(steps ~ interval, data = dataweek[dataweek$day == "weekday",], mean)
```
making plot for each dataset

```r
par(mfrow=c(2 , 1))
par(mar = c(2, 2, 2, 1))
with(weekdaypattern,  {plot(interval, steps, type = "l", 
                        xlab = "", ylab = "", main = "weekday")})
with(weekendpattern,  {plot(interval, steps, type = "l", 
                            xlab = "", ylab = "", main = "weekend")})
```

![plot of chunk unnamed-chunk-20](figure/unnamed-chunk-20-1.png) 
  
we can see difference in daily activity pattern between weekdays and weekend  
- later start of activity on weekends (presumably late awakening)  
- lower level of active during the day on weekdays then weekends. may be becouse of sitting type of job
        of the subject



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
## [1] LC_COLLATE=Russian_Russia.1251     LC_CTYPE=Russian_Russia.1251      
## [3] LC_MONETARY=Russian_Russia.1251    LC_NUMERIC=C                      
## [5] LC_TIME=English_United States.1252
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
## [1] knitr_1.9
## 
## loaded via a namespace (and not attached):
##  [1] digest_0.6.8    evaluate_0.5.5  formatR_1.0     htmltools_0.2.6
##  [5] markdown_0.7.4  mime_0.2        rmarkdown_0.5.1 stringr_0.6.2  
##  [9] tools_3.1.3     yaml_2.1.13
```

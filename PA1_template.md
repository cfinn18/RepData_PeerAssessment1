# Reproducible Research: Peer Assessment 1

## Loading and preprocessing the data


```r
data <- read.csv("activity.csv")
data_waNA <- data[which(data$steps!= "NA"), ]
```

## What is mean total number of steps taken per day?


```r
library(plyr)
daily_steps <- ddply(data_waNA, .(date), summarise, steps=sum(steps))
hist(daily_steps$steps, xlab="steps per day")
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png)


```r
mean(daily_steps$steps)
```

```
## [1] 10766.19
```

```r
median(daily_steps$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?


```r
average_date <- ddply(data_waNA, .(interval), summarise, steps=mean(steps))
plot(average_date$interval, average_date$steps, type="l", xlab="5-minute interval", 
     ylab="Average steps",main="Average daily activity")
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png)


```r
average_date[average_date$steps==max(average_date$steps),]
```

```
##     interval    steps
## 104      835 206.1698
```

```r
colnames(average_date)[2] <- "intervalAvg"
```


## Imputing missing values


```r
sum(is.na(data$steps))
```

```
## [1] 2304
```

```r
merged <- arrange(join(data, average_date), interval)
```

```
## Joining by: interval
```

```r
merged$steps[is.na(merged$steps)] <- merged$intervalAvg[is.na(merged$steps)]
new_daily_steps <- ddply(merged, .(date), summarise, steps=sum(steps))
hist(new_daily_steps$steps, main="Number of Steps", 
     xlab="steps taken each day",,)
```

![plot of chunk unnamed-chunk-13](figure/unnamed-chunk-13-1.png)


```r
mean(new_daily_steps$steps)
```

```
## [1] 10766.19
```

```r
median(new_daily_steps$steps)
```

```
## [1] 10766.19
```

```r
daily_steps_1 <- sum(data_waNA$steps)
daily_steps_2 <- sum(merged$steps)
diff <- daily_steps_2 -daily_steps_1 []
```

## Are there differences in activity patterns between weekdays and weekends?


```r
library(lattice)
weekdays <- weekdays(as.Date(merged$date))
data_weekdays <- transform(merged, day=weekdays)
data_weekdays$wk <- ifelse(data_weekdays$day %in% c("Saturday", "Sunday"),"weekend", "weekday")
average_week <- ddply(data_weekdays, .(interval, wk), summarise, steps=mean(steps))
xyplot(steps ~ interval | wk, data = average_week, layout = c(1, 2), type="l")
```

![plot of chunk unnamed-chunk-15](figure/unnamed-chunk-15-1.png)


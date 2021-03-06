# Reproducible Reasearch Project 1

1.Reading the data using read.csv function and converting the class of date column




```r
data <- read.csv('activity.csv')
data$date <- as.Date(data$date,"%Y-%m-%d")
head(data)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```

2. To get a histogram of total number of steps per day,the sum of steps is      calculated and they are grouped by date


```r
noofsteps <- data %>% group_by(date) %>% summarise(Steps = sum(steps))
noofsteps <- noofsteps[(!is.na(noofsteps$Steps)),]
hist(noofsteps$Steps,main="Total number of steps per day",xlab = "NoofSteps")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6-1.png)

3. Mean and median number of steps taken each day is calculated by using the functions given below


```r
mean(noofsteps$Steps)
```

```
## [1] 10766.19
```

```r
median(noofsteps$Steps)
```

```
## [1] 10765
```

4.To find the average number of steps taken through 5 minute interval, the tapply function is used


```r
avgnoofsteps <- tapply(data$steps, data$interval, mean,na.rm = TRUE)
plot(names(avgnoofsteps),avgnoofsteps,type = 'l',
     main = "Average number of steps through five minute interval",
     xlab = "5 minute time intervals",ylab="average number of steps")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8-1.png)

5. To find out the 5minute interval with the highest average, which function is used


```r
max <- which.max(avgnoofsteps)
names(max)
```

```
## [1] "835"
```

6. The number of rows with missing data is given below


```r
sum(is.na(data$steps))
```

```
## [1] 2304
```

To impute the missing values, the mean of the average number of steps taken per 
5 minute interval has been added in the place of NA values


```r
data[is.na(data$steps),1] <- mean(avgnoofsteps)
```

7. The histogram here has been processed the same as in Step2 


```r
noofsteps <- data %>% group_by(date) %>% summarise(Steps = sum(steps))
hist(noofsteps$Steps,main="Total number of steps per day",xlab = "NoofSteps")
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12-1.png)

```r
mean(noofsteps$Steps)
```

```
## [1] 10766.19
```

```r
median(noofsteps$Steps)
```

```
## [1] 10766.19
```

There has been a slight change in the median because of imputing missing values

8.to create a panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends, a factor variable should be created to separate weekdays and weekends


```r
wk <- factor(weekdays(data$date),
  levels = c("Monday","Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday"),
  labels = c("weekday","weekday","weekday","weekday","weekday","weekend","weekend"))
```
The next step is to add the factor variable as a column in the data frame and separate the weekday and weekend data


```r
data$weekday <- wk
weekdaydata <- filter(data,weekday == "weekday")
weekenddata <- filter(data,weekday != "weekday")
```

To get the average number of steps in the weekday and weekend,tapply was used like in Step4


```r
avgnoofsteps <- tapply(weekdaydata$steps, weekdaydata$interval, mean,na.rm = TRUE)
plot(names(avgnoofsteps),avgnoofsteps,type = 'l',main = "Average number of steps comparison",
     xlab = "5 minute time intervals",ylab="average number of steps",col = "blue")
avgnoofsteps <- tapply(weekenddata$steps, weekenddata$interval, mean,na.rm = TRUE)
lines(names(avgnoofsteps),avgnoofsteps,type = 'l',col = "red")
legend("topleft",legend = c("weekday","weekend"),col = c("blue","red"),pch=19)
```

![plot of chunk unnamed-chunk-15](figure/unnamed-chunk-15-1.png)

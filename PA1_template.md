
** Part 1 **
## Loading and transforming the data into a suitable format for 
## the analysis i.e. removing any NAs.
```{r, echo=TRUE}
dataset_url <- "http://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
download.file(dataset_url, "activity.zip")

data1 <- read.csv("activity.csv", header = TRUE)
str(data1)
data1 <- data1[complete.cases(data1[, 1]),]
str(data1)
```
## Subsetting the number of steps by date and returning the total number of steps by day
```{r, echo=TRUE}
data1_Date <- tapply(data1$steps, data1$date, FUN = sum)
```
## Making a histogram of the total number of steps by day
```{r, echo=TRUE}
hist(data1_Date, col ="green", main = "Total number of steps taken each day", xlab = "Total number of steps per day")
```
## Calculating the mean and median of the total number of steps per day
```{r, echo=TRUE}
steps_mean <- mean(data1_Date, na.rm = TRUE)


steps_median <- median(data1_Date, na.rm = TRUE)
```
*The mean of the total number of steps per day is 10766*
*The median of the total number of steps per day is 10765*

** Part 2 **
## Average daily activity pattern

   ### Making a time series plot of the 5-minute interval(x-axis) and the average       
    ### of steps across all days (y-axis)
* Getting the mean of the steps per day and per interval *
```{r, echo=TRUE}
steps_interval <- tapply(data1$steps, data1$interval, FUN = mean, na.rm = TRUE)

plot(x = unique(data1$interval), y = steps_interval, type = "l", xlab = "5-minute interval", ylab = "Average number of steps across all days")
```
``` {r, echo=TRUE}
Interv_max_steps <- unique(data1$interval[steps_interval == max(steps_interval)])
Interv_max_steps
```
The 5-min interval with the maximum of steps is 835.


** Part 3 **
## Imputing missing values 
     ### Total number of missing values in the dataset is 2304. It is determined         
     by the code below where data1 is the raw dataset.
     
```{r, echo=TRUE}
NAs <- is.na(data1[, 1])
sum(NAs)
```
 ### Method to fill in all the missing values in the dataset
I decided to replace all the NAs by the mean of steps of the corresponding interval.
```{r, echo=TRUE}
New_DF1 <- data.frame(steps = steps_interval, interval = unique(unlist(data1$interval)))
colnames(New_DF1) <- c("mean", "interval")

Imputed_data <- data1
Imputed_data[is.na(Imputed_data$steps), ]$steps <- New_DF1[New_DF1$interval == 
Imputed_data[is.na(Imputed_data$steps), ]$interval, ]$mean[[1]]

head(Imputed_data, 4)


New_Dataset_Date <- tapply(Imputed_data$steps, Imputed_data$date, FUN = sum)

hist(New_Dataset_Date, col ="red", main = "Total number of steps taken each day", xlab = "Total number of steps per day")
```
### Mean and median of total number of steps taken per day
```{r echo=TRUE}
steps_mean2 <- mean(New_Dataset_Date, na.rm = TRUE)


steps_median2 <- median(New_Dataset_Date, na.rm = TRUE)
```
*The mean of the total number of steps per day is 9419*
* The median of the total number of steps per day is 10395*
These values are different from the ones obtained in the first part. Replacing missing values causes the mean and median of the total daily number of steps to decrease.The difference between the means (10766 vs 9419) is much bigger than the difference between the medians (10765 vs 10395).

** Part 4 **
The dataset with filled-in missing values is used for this part.
### Creating a new factor variable with 2 levels- "weekday" and "weekend"
```{r, echo=TRUE}
Imputed_data$day <- weekdays(as.Date(Imputed_data$date))

weekend <- Imputed_data$day == "Sunday " | Imputed_data$day == "Saturday"
Imputed_data$day[weekend] <- "weekend"

Imputed_data$day[!weekend] <- "weekday"
Imputed_data$day <- as.factor(Imputed_data$day)

data_weekend <- aggregate(Imputed_data$steps[weekend], by = list(interval = Imputed_data$interval[weekend]), 
    mean, na.rm = T)

data_weekday <- aggregate(Imputed_data$steps[!weekend], by = list(interval = Imputed_data$interval[!weekend]), 
    mean, na.rm = T)

par(mfrow = c(2,1))

plot(data_weekday, type = "l", main= " Average number of steps across weekdays", xlab= "5-minute-interval", ylab= "average steps")

plot(data_weekend, type = "l", main= " Average number of steps across weekends", xlab= "5-minute-interval", ylab= "average steps")
```

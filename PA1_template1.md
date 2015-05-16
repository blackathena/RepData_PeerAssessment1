---
title: "PA1_template1.md"
author: "Likane Anne"
date: "Saturday, May 16, 2015"
output: html_document

** Part 1 **
## Loading and transforming the data into a suitable format for 
## the analysis i.e. removing any NAs.
```{r, echo=TRUE, fig.show='asis'}
dataset_url <- "http://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
download.file(dataset_url, "activity.zip")
unzip("activity.zip", exdir = "activity")


data1 <- read.csv("activity.csv", header = TRUE)
str(data1)
data1 <- data1[complete.cases(data1[, 1]),]
str(data1)
```
## Subsetting the number of steps by date and returning the total number of steps by day
```{r,echo=TRUE}
data1_Date <- tapply(data1$steps, data1$date, FUN = sum)
```
## Making a histogram of the total number of steps by day
```{r, echo=TRUE, fig.show='asis'}
hist(data1_Date, col ="green", main = "Total number of steps taken each day", xlab = "Total number of steps per day")
```
## Making a histogram of the total number of steps by day
```{r, echo=TRUE, fig.show='asis'}
hist(data1_Date, col ="green", main = "Total number of steps taken each day", xlab = "Total number of steps per day")
```

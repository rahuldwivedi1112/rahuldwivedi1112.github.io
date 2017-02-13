---
title: "PML Final Project"
author: "Rahul Dwivedi"
date: "February 12, 2017"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Project Introduction

### Background

Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, your goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. More information is available from the website here: http://groupware.les.inf.puc-rio.br/har (see the section on the Weight Lifting Exercise Dataset).

### Data

The training data for this project are available here: https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv

The test data are available here: https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv

The data for this project come from this source: http://groupware.les.inf.puc-rio.br/har. If you use the document you create for this class for any purpose please cite them as they have been very generous in allowing their data to be used for this kind of assignment.

### Goal

The goal of your project is to predict the manner in which they did the exercise. This is the “classe” variable in the training set. You may use any of the other variables to predict with. You should create a report describing how you built your model, how you used cross validation, what you think the expected out of sample error is, and why you made the choices you did. You will also use your prediction model to predict 20 different test cases.

## Getting the Data
```{r}
library(caret)
library(rpart)
library(rpart.plot)
library(rattle)
library(randomForest)
setwd("~/GitHub/Practical Machine Learning")
training <- read.csv("pml-training.csv", na.strings = c("NA", ""))
testing <- read.csv("pml-testing.csv", na.strings = c("NA", ""))

```
The training dataset has 19622 observations and 160 variables, and the testing data set contains 20 observations and the same variables as the training set.


## Cleaning Data

We delete the columns which have null values in them fromboth Training and Testing set.We can also remove columns line X, username, timestamp etc as they have no predictive power

```{r}
set.seed(55555)
training <- training[, colSums(is.na(training)) == 0]
testing <- testing[, colSums(is.na(testing)) == 0]
training <- training[, -c(1:7)]
testing <- testing[, -c(1:7)]
inTrain = createDataPartition(training$classe, p = 0.6)[[1]]
newTraining <- training[inTrain,]
newtesting <- training[-inTrain,]
```

## Data Prediction

### Decision Trees
Now Predicting with Decision Trees and checking the accuracy 

```{r}


modFitDT <- rpart(classe ~ ., data=newTraining, method="class")
predictionsDT <- predict(modFitDT, newtesting, type = "class")
cmtree <- confusionMatrix(predictionsDT, newtesting$classe)
cmtree
```
Here We see that the Accuracy is 72%. Need to check other models for more accuracy.

### Random Forest
```{r}
set.seed(55555)
modFitRF <- randomForest(classe ~ ., data=newTraining)
predictionRF <- predict(modFitRF, newtesting, type = "class")
cmrf <- confusionMatrix(predictionRF, newtesting$classe)
cmrf
plot(modFitRF)
```
The Accuracy of Random Forest comes out to be 99.31%. This is looking promising. 

## Conclusion
we can simply tell the Random forest classification technique works better. The results that was obtained by using random forest technique were highly accurate on the testing set. 

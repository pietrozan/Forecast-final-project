# Forecasting-final-project
Coursera Practical Machine Learning final project
By Pietro Zanetti 
Overview
Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it.
In this project, we will use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways.
The data consists of a Training data and a Test data (to be used to validate the selected model).
The goal of your project is to predict the manner in which they did the exercise. This is the “classe” variable in the training set. You may use any of the other variables to predict with.
Data Loading and Processing
#library(e1071)
library(caret)
## Loading required package: lattice
## Loading required package: ggplot2
library(rpart)
library(rpart.plot)
library(RColorBrewer)
library(rattle)
## Rattle: A free graphical interface for data science with R.
## Version 5.1.0 Copyright (c) 2006-2017 Togaware Pty Ltd.
## Type 'rattle()' to shake, rattle, and roll your data.
library(randomForest)
## randomForest 4.6-12
## Type rfNews() to see new features/changes/bug fixes.
## 
## Attaching package: 'randomForest'
## The following object is masked from 'package:rattle':
## 
##     importance
## The following object is masked from 'package:ggplot2':
## 
##     margin
library(corrplot)
## corrplot 0.84 loaded
library(gbm)
## Loading required package: survival
## 
## Attaching package: 'survival'
## The following object is masked from 'package:caret':
## 
##     cluster
## Loading required package: splines
## Loading required package: parallel
## Loaded gbm 2.1.3
Getting, Cleaning and Exploring the data
train_in <- read.csv('./pml-training.csv', header=T)
valid_in <- read.csv('./pml-testing.csv', header=T)
dim(train_in)
## [1] 19622   160
dim(valid_in)
## [1]  20 160
As shown below there are 19622 observations and 160 variables in the Training dataset

Cleaning input data
We remove the variables that contains missing values. Note along the cleaning process we display the dimension of the reduced dataset

trainData<- train_in[, colSums(is.na(train_in)) == 0]
validData <- valid_in[, colSums(is.na(valid_in)) == 0]
dim(trainData)
## [1] 19622    93
dim(validData)
## [1] 20 60
We now remove the first seven variables as they have little impact on the outcome classe
trainData <- trainData[, -c(1:7)]
validData <- validData[, -c(1:7)]
dim(trainData)
## [1] 19622    86
dim(validData)
## [1] 20 53
Preparing the datasets for prediction
Preparing the data for prediction by splitting the training data into 70% as train data and 30% as test data. This splitting will server also to compute the out-of-sample errors.

The test data renamed: valid_in (validate data) will stay as is and will be used later to test the prodction algorithm on the 20 cases.

set.seed(1234) 
inTrain <- createDataPartition(trainData$classe, p = 0.7, list = FALSE)
trainData <- trainData[inTrain, ]
testData <- trainData[-inTrain, ]
dim(trainData)
## [1] 13737    86
dim(testData)
## [1] 4124   86
Cleaning even further by removing the variables that are near-zero-variance
NZV <- nearZeroVar(trainData)
trainData <- trainData[, -NZV]
testData  <- testData[, -NZV]
dim(trainData)
## [1] 13737    53
dim(testData)
## [1] 4124   53
After this cleaning we are down now to 53 variables

The following correlation plot uses the following parameters (source:CRAN Package ‘corrplot’) “FPC”: the first principal component order. “AOE”: the angular order tl.cex Numeric, for the size of text label (variable names) tl.col The color of text label.

cor_mat <- cor(trainData[, -53])
corrplot(cor_mat, order = "FPC", method = "color", type = "upper", 
         tl.cex = 0.8, tl.col = rgb(0, 0, 0))
         As shwon in the image below: 
         

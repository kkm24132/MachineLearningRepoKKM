
Machine Learning Project 
=========================

Prediction of Weight Lifting Exercises Dataset
==============================================

## Introduction

This report describes the project assignment of Practical Machine Learning Course (Coursera).
Using low cost accelerometers, it is possible to measure human activities. One application of this is measuring the proper form of weight lifting. More information is available from the website here:[http://groupware.les.inf.puc-rio.br/har](http://groupware.les.inf.puc-rio.br/har). In this report it is studied if it can be determined the weight lifting from using the accelerometer data collected.

## Data preparation

Reading both datasets
```{r}
trainData <- read.csv('pml-training.csv',header=TRUE,sep=',')
testData <- read.csv('pml-testing.csv',header=TRUE,sep=',')
```


Removing zero covariates

```r
library(caret)
```

```
## Loading required package: lattice
## Loading required package: ggplot2
```

```r
nsv<-nearZeroVar(trainData) 
trainData <- trainData[-nsv] 
testData <- testData[-nsv]
```


Assigning missing values

```r
trainData[is.na(trainData)] <- 0 
testData[is.na(testData)] <- 0 
```


Taking only numeric values

```r
num_features_idx = which(lapply(trainData,class) %in% c('numeric') )

trainData <- cbind(trainData$classe, trainData[,num_features_idx]) 
testData <- testData[,num_features_idx] 
names(trainData)[1] <- 'classe' 
```


## Building the prediction model
Using the numerical variables provided, a random forest model is built.


```r
library(randomForest)
```

```
## randomForest 4.6-10
## Type rfNews() to see new features/changes/bug fixes.
```

```r
set.seed(33833) 
predictModel<- randomForest(classe ~ ., trainData)
```


## Training prediction results
Following the training prediction results are shown. As it can be noticed the in sample accuracy is 100% which indicates the model does not suffer from bias.
If the same would be done for Out-of-sample, the accuracy would be lower.


```r
pred_training<-predict(predictModel,trainData) 
confusionMatrix(pred_training, trainData$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 5580    0    0    0    0
##          B    0 3797    0    0    0
##          C    0    0 3422    0    0
##          D    0    0    0 3216    0
##          E    0    0    0    0 3607
## 
## Overall Statistics
##                                 
##                Accuracy : 1     
##                  95% CI : (1, 1)
##     No Information Rate : 0.284 
##     P-Value [Acc > NIR] : <2e-16
##                                 
##                   Kappa : 1     
##  Mcnemar's Test P-Value : NA    
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity             1.000    1.000    1.000    1.000    1.000
## Specificity             1.000    1.000    1.000    1.000    1.000
## Pos Pred Value          1.000    1.000    1.000    1.000    1.000
## Neg Pred Value          1.000    1.000    1.000    1.000    1.000
## Prevalence              0.284    0.194    0.174    0.164    0.184
## Detection Rate          0.284    0.194    0.174    0.164    0.184
## Detection Prevalence    0.284    0.194    0.174    0.164    0.184
## Balanced Accuracy       1.000    1.000    1.000    1.000    1.000
```

```r
print(mean(pred_training == trainData$classe)) 
```

```
## [1] 1
```


## Test Prediction Results
Applying this model to the test data provided yields 100% classification accuracy on the twenty test observations.

```r
answers<-predict(predictModel,testData) 
answers
```

```
##  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 
##  B  A  B  A  A  E  D  B  A  A  B  C  B  A  E  E  A  B  B  B 
## Levels: A B C D E
```


## Conclusion
As the results has shown, it is possible to provide very good prediction of weight lifting style, using accelerometers.

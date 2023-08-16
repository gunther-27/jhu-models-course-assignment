---
title: "Models Project"
author: "Gunther"
date: "2023-08-16"
output: html_document
---



## R Markdown

The project is to predict the activity of the participant.  The documentation of the dataset does not seem available.

#### Load libraries

```r
library(caret)
library(AppliedPredictiveModeling)
```
#### Load the data and convert the classe variable into a factor variable.

```r
ex_data <- read.csv('pml-training.csv', na.strings = c("NA","", "#DIV/0"))
validation <- read.csv('pml-testing.csv', na.strings = c("NA","", "#DIV/0"))

ex_data$classe <- factor(ex_data$classe)
```

#### Get the complete data set columns with no Nans and get rid of unimportant columns (i.e. the first 7)

```r
comp_col <- complete.cases(t(ex_data)) & complete.cases(t(validation))

ex_data <- ex_data[, comp_col] 
validation <- validation[, comp_col] 

ex_data <- ex_data[, -c(1:7)] 
validation <- validation[, -c(1:7)] 
```

#### Split into training and testing sets

```r
set.seed(1221)
ex_data$classe <- factor(ex_data$classe)

inTrain = createDataPartition(ex_data$classe, p = 0.75, list = FALSE)

training = ex_data[ inTrain,]

testing = ex_data[-inTrain,]
```

#### Train a model.  I choose QDA because it was the best that my computer could handle.  Other algorithms took too long with >14000 sample entries.

```r
set.seed(1221)

mod <- train(classe~., data= training, method = 'qda')

pred <- predict(mod, newdata = testing)

accuracy <- mean(pred == testing$classe)
cat("Accuracy:", accuracy, "\n")
```

```
## Accuracy: 0.896615
```

#### Find outputs for the original test/validation set

```r
val_pred <- predict(mod, newdata = validation)

print(val_pred)
```

```
##  [1] A A B A A E D B A A B C B A E E A B B B
## Levels: A B C D E
```

#### After taking the quiz, it seems like the first calssification is still wrong.  But this is expected since I could only get ~90 accuracy.

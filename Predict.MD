
Goal of the project

The goal of your project is to predict the manner in which they did the exercise. This is the "classe" variable in the training set. You may use any of the other variables to predict with. You should create a report describing how you built your model, how you used cross validation, what you think the expected out of sample error is, and why you made the choices you did. You will also use your prediction model to predict 20 different test cases.

library(ggplot2) library(caret) Download data:

fileUrlTraining <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv" fileUrlTesting <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv" download.file(fileUrlTraining, destfile = "pml-training.csv") download.file(fileUrlTesting, destfile = "pml-testing.csv") From looking at the csv-files we can see, that there are multiple possibilities for NA-Strings, so when importing the data we add them to na.strings

training <- read.csv("pml-training.csv", na.strings = c("NA", "#DIV/0!", "")) testing <- read.csv("pml-testing.csv", na.strings = c("NA", "#DIV/0!", "")) Take a look at the data with head(training) - (too much to display here) From this we can see that there are a lot of variables with missing (NA) data. We remove those that have only NA values

training <- training[, colSums(is.na(training)) == 0] testing <- testing[, colSums(is.na(testing)) == 0] We also see that the first 7 variables are just IDs or names, so we can also remove them

training <- training[, -c(1:7)] testing <- testing[, -c(1:7)] Now we can split training into a training and a test dataset.

set.seed(1234) trainingPartition <- createDataPartition(training$classe, p = 0.7, list = FALSE) trainingSet <- training[trainingPartition,] testingSet <- training[-trainingPartition,] Model 1: Classification Tree

First we try a Classification Tree model:

modClass <- train(classe ~ ., data = trainingSet, method = "rpart") predClass <- predict(modClass, testingSet) confusionMatrix(predClass, testingSet$classe) The accuray is not very good (49%), so as a next step we try a random forest model

Model 2: Random Forest

We calculate the model with 3 fold cross validation

tc <- trainControl(method="cv",number=3) modRF <- train(classe ~ ., data = trainingSet, methot = "rf", trControl = tc) predRF <- predict(modRF, testingSet) confusionMatrix(predRF, testingSet$classe) sum(predRF != testingSet$classe)/length(testingSet$classe) We see that this has an accuracy of 99.41% and an out of sample error rate of 0.59%. So we choose the random forest model as our final model.

###Predicting test data Now we can use our final model to predict the classe for the imported test data:

predRFTest <- predict(modRF, testing) predRFTest

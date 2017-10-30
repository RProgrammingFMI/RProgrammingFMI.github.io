```R
# https://cran.r-project.org/web/packages/R6/vignettes/Introduction.html

# https://raw.githubusercontent.com/mlampros/DataSets/master/mnist.zip

mnist <- read.table(unz("mnist.zip", "mnist.csv"), nrows = 70000, header = T, quote = "\"", sep = ",")

library(R6)
NNClassifier <- R6Class("NNClassifier",
                        public = list(
                          XTrain = NULL,
                          yTrain = NULL,
                          initialize = function(XTrain = NA, yTrain = NA){
                            self$XTrain <- XTrain,
                            self$yTrain <- yTrain
                          },
                          train = function(X, y){
                            self$XTrain <- X
                            self$yTrain <- y
                          },
                          predict = function(X){
                            ### ...
                          }
                        ))
```

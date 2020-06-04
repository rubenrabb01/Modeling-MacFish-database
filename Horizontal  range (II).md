# Analysis of range of distances for horizontal movement (II)

This section is a follow-up to the analysis of horizontal range data where the process for GAMM models building and selection of importance variables is about the same with minor differences regarding:
- The definition of smooths terms and surface interactions change with the addition of new variables:
  - Daily water temperature
  - Moon phase
  - Day length
- Weekly temporal trends are not accounted for by in the models

## 1. Classification of variables using Random Forest

With the inclusion of new variables we want to classify them based on their importance for which we use a ML recursive partitioning method such as Random Forest. The aims are:
- Estimate which variables have more weight on average on horizontal range values
- Rule out those variables with higher mean minimal depth (or less MSE increase) thoughout the tree forest

:books:`library(randomForest)`
:books:`library(randomForestExplainer)`
:books:`library(randomForestSRC)`

### Set the random seed to be reproducible

```
set.seed(500)
```

### Create training and test sets

Split data according to random samples into 60% training and 40% test datasets
```
samp.size <- sample(nrow(data_longit_sub), nrow(data_longit_sub) * 0.60)
data_longit_sub.train <- data_longit_sub[samp.size, ]
data_longit_sub.test <- data_longit_sub[-samp.size, ]

# Alternatively, using the _floor_ and _seq_len_ functions to create training and test sets:
samp.size <- floor(0.60 * nrow(data_longit_sub))
data_longit_sub.train_ind <- sample(seq_len(nrow(data_longit_sub)),size = samp.size)
data_longit_sub.train <- data_longit_sub[data_longit_sub.train_ind, ]
data_longit_sub.test <- data_longit_sub[-data_longit_sub.train_ind, ]
```

### Grow regression tree forest

```
model.rf<-randomForest(sqrt(ranged2d+1) ~ body_size + day_length + day_temp + lunar_phase + Species + season + weekly + seasonally, data = data_longit_sub.train,importance=TRUE,ntree=500, mtry=15,do.trace=100)
```

### Summary of tree forest

```
print(model.rf)

Call:
 randomForest(formula = sqrt(ranged2d + 1) ~ body_size + day_length +      day_temp + lunar_phase + Species + season + weekly + seasonally,      data = data_longit_sub.train, importance = TRUE, ntree = 500,      mtry = 15, do.trace = 100)
               Type of random forest: regression
                     Number of trees: 500
No. of variables tried at each split: 8

          Mean of squared residuals: 138.7859
                    % Var explained: 41.79
```
**Note** that if we included subject identity _Id_ in the tree model the % of variance explained would increase by ~8 %. However, provided that variation between individuals is expected to be high, and that _Id_ is a random-effects term, including that variable has confounding effects for variable classification

### Plot variable importance

```
# plot(model.rf)
# varImpPlot(model.rf,sort = T, main="Variable Importance", n.var=8)

```
[Random Forest Summary](http://172.21.3.20:8787/files/Teri_longit_move/Your_forest_explained.html)


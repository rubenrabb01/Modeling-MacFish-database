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

### 1.1. Set the random seed to be reproducible

```
set.seed(500)
```

### 1.2. Create training and test sets

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

### 1.3. Grow regression tree forest

```
model.rf<-randomForest(sqrt(ranged2d+1) ~ body_size + day_length + day_temp + lunar_phase + Species + season + weekly + seasonally, data = data_longit_sub.train,importance=TRUE,ntree=500, mtry=15,do.trace=100)
```

### 1.4. Summary of tree forest

In the following link you can view a complete report of the [Random Forest output](http://172.21.3.20:8787/files/Teri_longit_move/Your_forest_explained.html)

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

### 1.4.1. Variable importance

```
# plot(model.rf)
# varImpPlot(model.rf,sort = T, main="Variable Importance", n.var=8)
variable.imp <- data.frame(importance(model.rf))
variable.imp$Variables <- row.names(variable.imp)
```

Order variables from best to worse MSE
```
print(variable.imp[order(variable.imp$X.IncMSE ,decreasing = T),])
```
```
             X.IncMSE IncNodePurity   Variables
body_size   278.64809     460904.91   body_size
day_temp    112.81204     300561.98    day_temp
day_length  108.10442     287815.93  day_length
Species      58.96291      51158.85     Species
season       58.06306      52670.37      season
lunar_phase  36.85303     188701.82 lunar_phase
seasonally   23.48321      14506.37  seasonally
weekly      -15.19021      89594.85      weekly
```

Order according to Node Purity
```
print(variable.imp[order(variable.imp$IncNodePurity ,decreasing = T),])
```
```
             X.IncMSE IncNodePurity   Variables
body_size   278.64809     460904.91   body_size
day_temp    112.81204     300561.98    day_temp
day_length  108.10442     287815.93  day_length
lunar_phase  36.85303     188701.82 lunar_phase
weekly      -15.19021      89594.85      weekly
season       58.06306      52670.37      season
Species      58.96291      51158.85     Species
seasonally   23.48321      14506.37  seasonally
```

Create a data frame with various measures of importance of variables in the random forest
```
importance_frame <- measure_importance(model.rf)
save(importance_frame, file = "importance_frame.rda")
load("importance_frame.rda")
importance_frame
```
```
     variable mean_min_depth no_of_nodes mse_increase node_purity_increase no_of_trees times_a_root   p_value
1   body_size          0.000      130228   205.610949            460904.91         500          500 0.9998216
2  day_length          2.138      234528   105.880126            287815.93         500            0 0.0000000
3    day_temp          2.970      230287   110.109965            300561.98         500            0 0.0000000
4 lunar_phase          3.714      244839    13.388058            188701.82         500            0 0.0000000
5      season          1.970       13851    47.948352             52670.37         500            0 1.0000000
6  seasonally          4.968       13611     7.550513             14506.37         500            0 1.0000000
7     Species          3.610       12784    47.175310             51158.85         500            0 1.0000000
8      weekly          4.658      171366    -3.895798             89594.85         500            0 0.0000000
```

**Plot Relations between measures of importance**

```
plot_importance_ggpairs(importance_frame)  # or use model.rf (but takes longer)
```
![Horiz_range](/Plots/Horiz_range_II_0.png "Horiz_range")

**Plot Relations between rankings according to different measures**

```
plot_importance_rankings(importance_frame) # or use model.rf (but takes longer)
```
![Horiz_range](/Plots/Horiz_range_II_01.png "Horiz_range")

### 1.4.2. Multi-way importance plots

In the first plot variables are ranked according to the best scores in relation to three importance measures derived from the forest tree:
- Mean depth of first split on the variable
- Number of trees in which the root is split on the variable
- The total number of nodes in the forest that split on that variable

In the second multi-way importance plot the importance measures used take into account the predictive capability of a variable and wether it has a significant effect on the response based on its p-value

```
multi_way_imp_1 <- plot_multi_way_importance(importance_frame, size_measure = "no_of_nodes")   or use model.rf (but takes longer)
multi_way_imp_2 <- plot_multi_way_importance(importance_frame, x_measure = "mse_increase", y_measure = "node_purity_increase", size_measure = "p_value", no_of_labels = 5)
grid.arrange(multi_way_imp_1, multi_way_imp_2, nrow = 1)
```
![Horiz_range](/Plots/Horiz_range_II_1.png "Horiz_range")

- We see that _body_size_ is ranked high and separated from the remaining variables, which match the previous importance frame

### 1.4.3. Distribution of minimal depth

Calculate minimal depth for each variable among the forest trees
```
min_depth_frame <- min_depth_distribution(model.rf)
save(min_depth_frame, file = "min_depth_frame.rda")
load("min_depth_frame.rda")
head(min_depth_frame, n = 10)
```
```
   tree    variable minimal_depth
1     1   body_size             0
2     1  day_length             3
3     1    day_temp             3
4     1 lunar_phase             4
5     1      season             2
6     1  seasonally             4
7     1     Species             2
8     1      weekly             4
9     2   body_size             0
10    2  day_length             2
```

**Plot minimal depth distribution**

```
plot_min_depth_distribution(model.rf)
```
![Horiz_range](/Plots/Horiz_range_II_2.png "Horiz_range")

- We see that the mean of the distribution of minimal depth is lowest for body_size with zero trees in which that variable was used for splitting

### 1.4.4. Variable interactions

```
(vars <- important_variables(importance_frame, k = 5, measures = c("mean_min_depth", "no_of_trees"))) # or use model.rf (but takes longer)
interactions_frame <- min_depth_interactions(model.rf, vars)
save(interactions_frame, file = "interactions_frame.rda")
load("interactions_frame.rda")
head(interactions_frame[order(interactions_frame$occurrences, decreasing = TRUE), ])
```

**Plot variable interactions**

In this plot it is reported the 30 top interactions according to mean of conditional minimal depth – a generalization of minimal depth that measures the depth of the second variable in a tree of which the first variable is a root (a subtree of a tree from the forest). In order to be comparable to normal minimal depth 1 is subtracted so that 0 is the minimum.

```
plot_min_depth_interactions(interactions_frame)
```
![Horiz_range](/Plots/Horiz_range_II_3.png "Horiz_range")

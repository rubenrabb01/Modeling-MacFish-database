## Decision trees for predicting the use of reservoir parts 

:books:`library(rpart)`  
:books:`library(rpart.plot)`  

### Grow a decision tree based on the variable use of reservoir parts

We build a model with a multi-class ordinal response (_res_part_) as DV. In this model each node shows:

- The predicted class (dam, middle, upper, tributary)
- The predicted probability of each reservoir part relative to species, season and mean depth
- The percentage of observations in the node

#### 1.1. Reservoir use by each species across sesions (mean depth)

This model will predict the use of reservoir parts accounting for mean depth

```
tree_res_part<- rpart(res_part ~ 1 + mean_depth + fi_species + season, data = data_poglm, control = rpart.control(cp = 0.005))
```
```
rpart.rules(tree_depth)
```
```
:chart:rpart.plot(tree_res_part,  type = 4, extra = 100, clip.right.labs = FALSE, branch = .3, under = TRUE)
```
![Res_part_tree](/Plots/Res_part_tree_1.png "Res_part_tree")
```
rpart.plot(tree_res_part, type = 4, extra = 100, branch.lty = 3, box.palette = "RdYlGn")
```
![Res_part_tree](/Plots/Res_part_tree_2.png "Res_part_tree")
```
rpart.plot(tree_res_part, type = 4, extra = 101, branch.lty = 3, box.palette = "RdYlGn")
```
![Res_part_tree](/Plots/Res_part_tree_3.png "Res_part_tree")

These plots show us that the class _dam_ is  is marked **unused** as it is never predicted by the model

#### 1.2. Reservoir use by each species across sesions (mean depth + body size)

This model will predict the use of reservoir parts accounting for mean depth and body size

```
tree_res_part<- rpart(res_part ~ 1 + mean_depth + body_size + fi_species + season, data = data_poglm, control = rpart.control(cp = 0.005))
```

#### 1.3. Reservoir use by each species across sesions (Individual fish)

This model will predict the use of reservoir parts for each individual fish

Grow a decision tree including individual fish as a covariate
```
tree_res_part<- rpart(res_part ~ 1 + fi_fishid + fi_species + season, data = data_poglm, control = rpart.control(cp = 0.005))
```
![Res_part_tree](/Plots/Res_part_tree_4.png "Res_part_tree")



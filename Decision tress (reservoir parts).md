
## Decision trees for classification of the use of reservoir parts 

:books:`library(rpart)`  
:books:`library(rpart.plot)`  

### Growth a decision tree based on the variable _res_part_ (DV)
```
tree_dist<- rpart(res_part ~ 1 + mean_depth + fi_species + season, data = data_poglm, control = rpart.control(cp = 0.005))
```
```
rpart.plot(tree_dist,  type = 4, extra = 100, clip.right.labs = FALSE, branch = .3, under = TRUE)
```
![Res_part_tree](/Plots/Res_part_tree_1.png "Res_part_tree")
```
rpart.plot(tree_dist, type = 4, extra = 100, branch.lty = 3, box.palette = "RdYlGn")
```
![Res_part_tree](/Plots/Res_part_tree_2.png "Res_part_tree")
```
rpart.plot(tree_dist, type = 4, extra = 101, branch.lty = 3, box.palette = "RdYlGn")
```
![Res_part_tree](/Plots/Res_part_tree_3.png "Res_part_tree")

### Grow a decision tree including individual fish as a covariate
```
tree_dist<- rpart(res_part ~ 1 + fi_fishid + fi_species + season, data = data_poglm, control = rpart.control(cp = 0.005))
```
![Res_part_tree](/Plots/Res_part_tree_4.png "Res_part_tree")

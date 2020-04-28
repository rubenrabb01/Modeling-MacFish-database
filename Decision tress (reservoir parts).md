
## Build Decision trees on the use of reservoir parts

:books:`library(rpart)`
:books:`library(rpart.plot)`

#### WITH DATA_POGLM (mean_depth)

Growth a decision tree using _res_part_ as DV
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

#### Define stationarity by re-value categories middle, upper and tributary to 1 (0 for dam - stationary)

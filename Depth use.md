# Analysis of mean depth use

- We fit L2 and L3 (random slope and intercept) models with _mean_depth_ as DV and _body_size_, _Species_ and _res_part_ as predictors
- Since we expect variation in mean depth use will differ across time (non-stationary) we include the time variable both in the fixed and random parts to account for the intercept and slope

## Create a new mean depth dataframe

:books:`library(plyr)`

Same steps can also be seen in "Common code" (i.e., data_poglm)
```
data.res.parts <- subset(parts.pref.info,  select=c(fi_fishid, date, res_part))
data_depth <- merge(data, data.res.parts, by = c("fi_fishid", "date"))
data_depth <- subset(data_depth,  select=c(fi_fishid, date, ca_tl_mm, ca_weight_g, fi_species, season, ca_lat_catch, ca_lon_catch, mean_depth, res_part, month ,ranged2d))
```
Rename and convert some variables
```
colnames(data_depth)[3] <- "body_size"
colnames(data_depth)[5] <- "Species"
data_depth$fi_fishid <- as.factor(data_depth$fi_fishid)
data_depth$Species <- as.factor(data_depth$Species)
```
Order by _fi_fishid_ and _date_ and delete duplicate rows
```
data_depth<- data_depth[with(data_depth, order(fi_fishid, date)),]
data_depth<-data_depth[!duplicated(data_depth[c("fi_fishid","date")]),]
data_depth
```
| fi_fishid 	| date      	| body_size  	| ca_weight_g 	| ca_weight_g  	| Species   	| season   	| ca_lat_catch 	| ca_lon_catch 	| mean_depth 	| res_part 	| month 	| ranged2d   	|
|-----------	|-----------	|------------	|-------------	|--------------	|-----------	|----------	|--------------	|--------------	|------------	|----------	|-------	|------------	|
| 1         	| T449202_1 	| 27/04/2017 	| 430         	| 605          	| pikeperch 	| spring_I 	| 48.82551     	| 14.4848      	| 1.9347592  	| middle   	| 4     	| 682.8973   	|
| 3         	| T449202_1 	| 28/04/2017 	| 430         	| 605          	| pikeperch 	| spring_I 	| 48.82551     	| 14.4848      	| 1.6868973  	| middle   	| 4     	| 623.8025   	|
| 4         	| T449202_1 	| 29/04/2017 	| 430         	| 605          	| pikeperch 	| spring_I 	| 48.82551     	| 14.4848      	| 1.7153804  	| upper    	| 4     	| 505.6705   	|
| 6         	| T449202_1 	| 30/04/2017 	| 430         	| 605          	| pikeperch 	| spring_I 	| 48.82551     	| 14.4848      	| 2.0259237  	| middle   	| 4     	| 3056.4011  	|
| 1749      	| T449268_1 	| 27/04/2017 	| 1620        	| 25100        	| wels      	| spring_I 	| 48.82635     	| 14.48225     	| 2.5873515  	| middle   	| 4     	| 1484.12472 	|
| 1750      	| T449268_1 	| 27/04/2017 	| 1620        	| 25100        	| wels      	| spring_I 	| 48.82635     	| 14.48225     	| 2.5873515  	| upper    	| 4     	| 1484.12472 	|
| 1751      	| T449268_1 	| 28/04/2017 	| 1620        	| 25100        	| wels      	| spring_I 	| 48.82635     	| 14.48225     	| 4.2431987  	| upper    	| 4     	| 2724.60869 	|
| 1752      	| T449268_1 	| 28/04/2017 	| 1620        	| 25100        	| wels      	| spring_I 	| 48.82635     	| 14.48225     	| 4.2431987  	| middle   	| 4     	| 2724.60869 	|
| 2304      	| T449271_1 	| 27/04/2017 	| 915         	| 6000         	| pike      	| spring_I 	| 48.83096     	| 14.47816     	| 3.3026707  	| middle   	| 4     	| 1662.9039  	|
| 2305      	| T449271_1 	| 27/04/2017 	| 915         	| 6000         	| pike      	| spring_I 	| 48.83096     	| 14.47816     	| 3.3026707  	| dam      	| 4     	| 1662.9039  	|
| 2306      	| T449271_1 	| 28/04/2017 	| 915         	| 6000         	| pike      	| spring_I 	| 48.83096     	| 14.47816     	| 3.9012472  	| dam      	| 4     	| 1888.7892  	|
| 2307      	| T449271_1 	| 28/04/2017 	| 915         	| 6000         	| pike      	| spring_I 	| 48.83096     	| 14.47816     	| 3.9012472  	| middle   	| 4     	| 1888.7892  	|

Describe the variable _mean_depth

:books:`library(psych)`
```
describe.by(data_depth$mean_depth, data_depth$Species)          #yet better
```
```
 Descriptive statistics by group
group: pike
   vars   n mean   sd median trimmed  mad  min  max range skew kurtosis   se
X1    1 913 2.62 2.09   2.18    2.33 1.78 0.35 17.1 16.75  1.9     6.48 0.07
---------------------------------------------------------------------------------------------------------------------------------
group: pikeperch
   vars    n mean   sd median trimmed  mad  min   max range skew kurtosis   se
X1    1 2117 3.45 2.42   2.63    3.06 1.58 0.39 12.64 12.25 1.34        1 0.05
---------------------------------------------------------------------------------------------------------------------------------
group: wels
   vars    n mean   sd median trimmed  mad  min   max range skew kurtosis   se
X1    1 2344 5.89 4.09   6.44     5.7 6.13 0.36 16.07 15.71 0.17     -1.3 0.08```
```
```
hist(data_depth$mean_depth, breaks = 20)
```
![Mean_depth](/Plots/Mean_depth_hist.png "Mean_depth")


## 1. Fit Mixed-Effects Models (LMM) to the data of depth use

To analyse the depth use made by the three species we fit a series of LMMs using _mean_depth_ as DV and _Species_, _season_, _body_size_ and _res_part_ as predictors

### 1.1. Find the best unconditional model fitted via REML

Fit nested intercept-only models including all potentially relevant random-effects and compare them with LRT
```
m_id<-lmer(mean_depth ~ 1  + (1| fi_fishid),data = data_depth, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_sp<-lmer(mean_depth ~ 1  + (1| Species) + (1| fi_fishid),data = data_depth, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_mon<-lmer(mean_depth ~ 1  + (1| month) + (1| fi_fishid),data = data_depth, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_sp_mon<-lmer(mean_depth ~ 1  + (1| Species) + (1| month),data = data_depth, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_sp_mon<-lmer(mean_depth ~ 1  + (1| month) + (1| fi_fishid) + (1| Species),data = data_depth, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
lrtest(m_id,m_id_sp)
```

### 1.2. Fit nested slope models including all potentially relevant random-effects and compare them with LRT.

### 1.3. Compare the best intercept and slope models

```
anova(m_sl_1,m_id_mon)
```

#### 1. 2. Find the best conditional LMMs fitted via ML including the RF structure of the previously selected unconditional model

Fit the models with setting **REML=FALSE**

```
m1<-lmer(mean_depth ~ 1 + body_size + Species + (month| Species:fi_fishid)+(month| Species),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m2<-lmer(mean_depth ~ 1 + body_size * Species + (month| Species:fi_fishid)+(month| Species),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m3<-lmer(mean_depth ~ 1 + body_size + month + (month| Species:fi_fishid)+(month| Species),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m4<-lmer(mean_depth ~ 1 + body_size * month + (month| Species:fi_fishid)+(month| Species),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m5<-lmer(mean_depth ~ 1 + Species + month + (month| Species:fi_fishid)+(month| Species),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m6<-lmer(mean_depth ~ 1 + Species * month + (month| Species:fi_fishid)+(month| Species),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m7<-lmer(mean_depth ~ 1 + body_size + Species + month + (month| Species:fi_fishid)+(month| Species),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m8<-lmer(mean_depth ~ 1 + body_size + Species * month + (month| Species:fi_fishid)+(month| Species),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m9<-lmer(mean_depth ~ 1 + body_size * Species + month + (month| Species:fi_fishid)+(month| Species),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m10<-lmer(mean_depth ~ 1 + body_size * Species * month + (month| Species:fi_fishid)+(month| Species),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m11<-lmer(mean_depth ~ 1 + body_size * Species + Species * month + (month| Species:fi_fishid)+(month| Species),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
bic = BIC(m1,m2,m3,m4,m5,m6,m7,m8,m9,m10,m11)
sortScore(bic , score = "bic")
```
```

p11<-lmer(mean_depth ~ 1  +  season*Species + (1 | date:fi_fishid)+ (1|fi_fishid) + (1| date),data = data_depth, REML=F, control=lmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
summ(p11)
interact_plot(p11, pred = season, modx = Species, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE)
plot_model(p11, type = "pred", terms = c("season", "Species"))
plot_model(p11,  mdrt.values = "meansd", type = "pred", terms = c("season", "Species"))
plot(Effect(c("season", "Species"), p1),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))


```

## 3. Grow a decision tree to the data of depth use

- We build a **rpart** model using _mean_depth_ as a continous response
- This model will predict depth use in each species across seasons and accounting for body size
- In this model each node shows:
  - The predicted value if mean depth
  - The percentage of observations in the node

Grow the tree
```
tree_depth<- rpart(mean_depth ~ 1 + body_size + Species + season + res_part, data = data_depth, control = rpart.control(cp = 0.005))
```
```
rpart.rules(tree_depth)
```
```
 mean_depth
        1.9 when season is            spring_I
        2.9 when season is spring_II or summer
        3.7 when season is    autumn or winter & fi_species is pike or pikeperch                                      & body_size >=          460
        4.0 when season is    autumn or winter & fi_species is pike or pikeperch & res_part is              tributary & body_size <   460
        6.9 when season is              winter & fi_species is              wels & res_part is     tributary or upper
        7.8 when season is    autumn or winter & fi_species is pike or pikeperch & res_part is dam or middle or upper & body_size <   460
        8.0 when season is    autumn or winter & fi_species is              wels & res_part is          dam or middle & body_size <   915
        9.3 when season is              autumn & fi_species is              wels & res_part is     tributary or upper
        9.5 when season is    autumn or winter & fi_species is              wels & res_part is          dam or middle & body_size is 1025 to 1640
       10.2 when season is              winter & fi_species is              wels & res_part is          dam or middle & body_size >=         1640
       13.5 when season is    autumn or winter & fi_species is              wels & res_part is          dam or middle & body_size is  915 to 1025
       14.2 when season is              autumn & fi_species is              wels & res_part is          dam or middle & body_size >=         1640
```
```
rpart.plot(tree_depth, type = 4, extra = 101, branch.lty = 3, box.palette = "RdYlGn")
```
![Depth_use_tree](/Plots/Depth_use_tree_1.png "Depth_use_tree")

### 3.1. Grow a decision tree including month and horizontal range

```
tree_depth_1<- rpart(mean_depth ~ 1 + body_size + Species + season + res_part + month + ranged2d , data = data_depth, control = rpart.control(cp = 0.005))
```
```
rpart.rules(tree_depth)
```
```
 mean_depth
        1.7 when season is                        spring_I & month <   9
        2.6 when season is             spring_II or summer & month <   9
        2.7 when season is                autumn or winter & month is  2 to 10 & Species is              wels & body_size >=         1575
        3.1 when season is                autumn or winter                     & Species is pike or pikeperch & body_size >=          460
        3.3 when season is spring_I or spring_II or summer & month >=        9                                                            & res_part is dam or middle or tributary
        4.0 when season is                autumn or winter                     & Species is pike or pikeperch & body_size <   460         & res_part is                  tributary
        5.0 when season is spring_I or spring_II or summer & month >=        9                                                            & res_part is                      upper & ranged2d >= 119
        6.2 when season is                autumn or winter & month <  10       & Species is pike or pikeperch & body_size <   460         & res_part is     dam or middle or upper
        6.4 when season is                autumn or winter & month <   2       & Species is              wels & body_size >=         1575
        7.7 when season is                autumn or winter & month <  10       & Species is              wels & body_size is 1025 to 1575
        8.9 when season is                autumn or winter & month >=       10 & Species is pike or pikeperch & body_size <   460         & res_part is     dam or middle or upper
        9.5 when season is                autumn or winter & month >=       10 & Species is              wels & body_size >=         1025
       10.8 when season is spring_I or spring_II or summer & month >=        9                                                            & res_part is                      upper & ranged2d <  119
       12.1 when season is                autumn or winter                     & Species is              wels & body_size <  1025
```
```
rpart.plot(tree_depth_1, type = 4, extra = 101, branch.lty = 3, box.palette = "RdYlGn")
```
![Depth_use_tree](/Plots/Depth_use_tree_2.png "Depth_use_tree")

- Time is a high-order classifying variable (**season** along with **month**)


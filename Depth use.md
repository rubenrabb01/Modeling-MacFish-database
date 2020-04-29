
## How does depth use vary between species and across seasons?

### 1. Fit Mixed-Effects Models (LMM) to the data of depth use

#### 1.1. Find the best unconditional model fitted via REML

Fit nested intercept-only models including all potentially relevant random-effects and compare them with LRT
```
m_id<-lmer(mean_depth ~ 1  + (1| fi_fishid),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_sp<-lmer(mean_depth ~ 1  + (1| Species) + (1| fi_fishid),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_mon<-lmer(mean_depth ~ 1  + (1| month) + (1| fi_fishid),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_sp_mon<-lmer(mean_depth ~ 1  + (1| Species) + (1| month),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_sp_mon<-lmer(mean_depth ~ 1  + (1| month) + (1| fi_fishid) + (1| Species),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
lrtest(m_id,m_id_sp)
```
```


To analyse the depth use made by the three species we fit a series of LMMs using _mean_depth_ as DV and _Species_, _season_, _body_size_ and _res_part_ as predictors

#### 1.2. Fit nested slope models including all potentially relevant random-effects and compare them with LRT.

#### 1.3. Compare the best intercept and slope models

```
anova(m_sl_1,m_id_mon)
```
```
#### 1. 2. Find the best conditional LMMs fitted via ML including the RF structure of the previously selected unconditional model

Fit the models with setting **REML=FALSE**

```
m1<-lmer(mean_depth ~ 1 + body_size + Species + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m2<-lmer(mean_depth ~ 1 + body_size * Species + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m3<-lmer(mean_depth ~ 1 + body_size + month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m4<-lmer(mean_depth ~ 1 + body_size * month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m5<-lmer(mean_depth ~ 1 + Species + month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m6<-lmer(mean_depth ~ 1 + Species * month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m7<-lmer(mean_depth ~ 1 + body_size + Species + month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m8<-lmer(mean_depth ~ 1 + body_size + Species * month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m9<-lmer(mean_depth ~ 1 + body_size * Species + month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m10<-lmer(mean_depth ~ 1 + body_size * Species * month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m11<-lmer(mean_depth ~ 1 + body_size * Species + Species * month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
bic = BIC(m1,m2,m3,m4,m5,m6,m7,m8,m9,m10,m11)
sortScore(bic , score = "bic")
```
```

```

## 3. Grow a decision tree to the data of depth use

- We build a **rpart** model using _mean_depth_ as a continous response
- This model will predict depth use in each species across seasons and accounting for body size
- In this model each node shows:
  - The predicted value if mean depth
  - The percentage of observations in the node

Grow the tree
```
tree_depth<- rpart(mean_depth ~ 1 + body_size + fi_species + season + res_part, data = data_poglm, control = rpart.control(cp = 0.005))
```
```
rpart.rules(tree_depth)
```
```
rpart.plot(tree_depth,  type = 4, extra = 100, clip.right.labs = FALSE, branch = .3, under = TRUE)
```
![Depth_use_tree](/Plots/Depth_use_tree_1.png "Depth_use_tree")
```
rpart.plot(tree_depth, type = 4, extra = 100, branch.lty = 3, box.palette = "RdYlGn")
```
![Depth_use_tree](/Plots/Depth_use_tree_2.png "Depth_use_tree")
```
rpart.plot(tree_depth, type = 4, extra = 101, branch.lty = 3, box.palette = "RdYlGn")
```
![Depth_use_tree](/Plots/Depth_use_tree_3.png "Depth_use_tree")

These plots shw us that the class _dam_ is  is marked **unused** as it is never predicted by the model

### Grow a decision tree including individual fish as a covariate
```
tree_depth<- rpart(mean_depth ~ 1 + fi_fishid + fi_species + season, data = data_poglm, control = rpart.control(cp = 0.005))
```
![Depth_use_tree](/Plots/Depth_use_tree_4.png "Depth_use_tree")

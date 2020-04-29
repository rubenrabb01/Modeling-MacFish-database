# How does depth use vary between species and across seasons?

## Create a dataframe including mean depth

:books:`library(plyr)`

These steps can also be seen in "Common code"
```
data.res.parts <- subset(parts.pref.info,  select=c(fi_fishid, date, res_part))
data_poglm <- merge(data, data.res.parts, by = c("fi_fishid", "date"))
data_poglm <- subset(data_poglm,  select=c(fi_fishid, date, ca_tl_mm, ca_weight_g, fi_species, season, ca_lat_catch, ca_lon_catch, mean_depth, res_part))
colnames(data_poglm)[5] <- "body_size"
```

## 1. Fit Mixed-Effects Models (LMM) to the data of depth use

To analyse the depth use made by the three species we fit a series of LMMs using _mean_depth_ as DV and _Species_, _season_, _body_size_ and _res_part_ as predictors

### 1.1. Find the best unconditional model fitted via REML

Fit nested intercept-only models including all potentially relevant random-effects and compare them with LRT
```
m_id<-lmer(mean_depth ~ 1  + (1| fi_fishid),data = data_poglm, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_sp<-lmer(mean_depth ~ 1  + (1| Species) + (1| fi_fishid),data = data_poglm, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_mon<-lmer(mean_depth ~ 1  + (1| month) + (1| fi_fishid),data = data_poglm, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_sp_mon<-lmer(mean_depth ~ 1  + (1| Species) + (1| month),data = data_poglm, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_sp_mon<-lmer(mean_depth ~ 1  + (1| month) + (1| fi_fishid) + (1| Species),data = data_poglm, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
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
m1<-lmer(mean_depth ~ 1 + body_size + Species + (month| Species:fi_fishid)+(month| Species),data = data_poglm, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m2<-lmer(mean_depth ~ 1 + body_size * Species + (month| Species:fi_fishid)+(month| Species),data = data_poglm, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m3<-lmer(mean_depth ~ 1 + body_size + month + (month| Species:fi_fishid)+(month| Species),data = data_poglm, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m4<-lmer(mean_depth ~ 1 + body_size * month + (month| Species:fi_fishid)+(month| Species),data = data_poglm, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m5<-lmer(mean_depth ~ 1 + Species + month + (month| Species:fi_fishid)+(month| Species),data = data_poglm, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m6<-lmer(mean_depth ~ 1 + Species * month + (month| Species:fi_fishid)+(month| Species),data = data_poglm, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m7<-lmer(mean_depth ~ 1 + body_size + Species + month + (month| Species:fi_fishid)+(month| Species),data = data_poglm, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m8<-lmer(mean_depth ~ 1 + body_size + Species * month + (month| Species:fi_fishid)+(month| Species),data = data_poglm, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m9<-lmer(mean_depth ~ 1 + body_size * Species + month + (month| Species:fi_fishid)+(month| Species),data = data_poglm, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m10<-lmer(mean_depth ~ 1 + body_size * Species * month + (month| Species:fi_fishid)+(month| Species),data = data_poglm, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m11<-lmer(mean_depth ~ 1 + body_size * Species + Species * month + (month| Species:fi_fishid)+(month| Species),data = data_poglm, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
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
![Depth_use_tree](/Plots/Depth_use_tree_3.png "Depth_use_tree")

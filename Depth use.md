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
data_depth$season <- as.factor(data_depth$season)
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


## Data exploration

Describe the variable _mean_depth _

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

## 1. Classify mean depth predictors through building of decision trees

Grow a decision tree to the data of depth use

- We build a **rpart** model using _mean_depth_ as a continous response
- In this model each node shows:
  - The predicted value of mean depth
  - The percentage of observations in the node

### 1.1. Grow a decision tree with species and season as predictors

```
tree_depth<- rpart(mean_depth ~ 1 + Species + season, data = data_depth, control = rpart.control(cp = 0.005))
```
```
rpart.rules(tree_depth)
```
```
mean_depth
        1.7 when season is            spring_I
        2.7 when season is spring_II or summer
        3.0 when season is    autumn or winter & Species is      pike
        4.2 when season is              winter & Species is pikeperch
        5.9 when season is              autumn & Species is pikeperch
        7.6 when season is              winter & Species is      wels
       10.0 when season is              autumn & Species is      wels
```
```
rpart.plot(tree_depth, type = 4, extra = 101, branch.lty = 3, box.palette = "RdYlGn")
```

![Depth_use_tree](/Plots/Depth_use_tree_1.png "Depth_use_tree")

### 1.2. Grow further the decision tree by including body size and reservoir parts

```
tree_depth_1<- rpart(mean_depth ~ 1 + body_size + Species + season + res_part , data = data_depth, control = rpart.control(cp = 0.005))
```
```
rpart.rules(tree_depth_1)
```
```
mean_depth
        1.7 when season is            spring_I
        1.9 when season is spring_II or summer & body_size >=         1575 & res_part is              middle or upper
        2.2 when season is spring_II or summer                             & res_part is             dam or tributary
        2.8 when season is    autumn or winter & body_size >=          460 & res_part is middle or tributary or upper & Species is pike or pikeperch
        3.4 when season is spring_II or summer & body_size <  1345         & res_part is              middle or upper
        3.7 when season is    autumn or winter & body_size <   460         & res_part is                    tributary & Species is pike or pikeperch
        4.3 when season is    autumn or winter & body_size >=          460 & res_part is                          dam & Species is pike or pikeperch
        4.4 when season is              winter & body_size >=         1348                                            & Species is              wels
        6.2 when season is              autumn & body_size >=         1025 & res_part is                    tributary & Species is              wels
        8.0 when season is    autumn or winter & body_size <   460         & res_part is       dam or middle or upper & Species is pike or pikeperch
        8.1 when season is              winter & body_size is 1025 to 1348                                            & Species is              wels
        9.5 when season is spring_II or summer & body_size is 1345 to 1575 & res_part is              middle or upper
        9.6 when season is              autumn & body_size >=         1025 & res_part is       dam or middle or upper & Species is              wels
       12.2 when season is    autumn or winter & body_size <  1025                                                    & Species is              wels
```
```
rpart.plot(tree_depth_1, type = 4, extra = 101, branch.lty = 3, box.palette = "RdYlGn")
```

![Depth_use_tree](/Plots/Depth_use_tree_2.png "Depth_use_tree")

- Time is a high-order classifying variable (**season** along with **month**; we will include _month_ in random effects along with season in further analysis)

## 2. Fit Mixed-Effects Models (LMM) to the data of depth use

- To analyse the depth use made by the three species we fit a series of LMMs using _mean_depth_ as DV and _Species_, _season_, _body_size_ and _res_part_ as predictors
- We use two approaches, one consisting of model selection based on AICc for selection of mixed models and other specifically testing our hypothesis based on the nested structure of the random effects
- The aim of this is just evidencing some sort of model fitting singularities, either due to perfect separation probems, too low sample size or overdispersion. Actually, in our case most singulatiry issues appear to be due to
the existence of duplicated measures of the variable _mean_depth_ between single days. The aggregating nature of these measures might indicate an incorrect sampling

## 2) Approach 1

### 2.1. Find the best conditional model for the Species x season interaction

:books:`library(lmtest)`

Fit nested intercept-only models including all potentially relevant random-effects and compare them with LRT
```
m_id<-lmer(mean_depth ~ 1  + Species * season +(1| fi_fishid),data = data_depth, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_sp<-lmer(mean_depth ~ 1  + Species * season + (1| fi_fishid) + (1| Species),data = data_depth, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_date<-lmer(mean_depth ~ 1  + Species * season + (1| fi_fishid) + (1| date) ,data = data_depth, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_sp_date<-lmer(mean_depth ~ 1  + Species * season + (1| fi_fishid) + (1| Species) + (1| date) ,data = data_depth, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
lrtest(m_id,m_id_sp)
```
```
Likelihood ratio test

Model 1: mean_depth ~ 1 + Species * season + (1 | fi_fishid)
Model 2: mean_depth ~ 1 + Species * season + (1 | fi_fishid) + (1 | Species)
  #Df  LogLik Df Chisq Pr(>Chisq)
1  17 -7748.1
2  18 -7748.1  1     0     0.9999
```
```
lrtest(m_id,m_id_date)
```
```
Likelihood ratio test

Model 1: mean_depth ~ 1 + Species * season + (1 | fi_fishid)
Model 2: mean_depth ~ 1 + Species * season + (1 | fi_fishid) + (1 | date)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  17 -7748.1
2  18 -7715.0  1 66.246  3.981e-16 ***
```
```
lrtest(m_id_sp,m_id_date)
```
```
Likelihood ratio test

Model 1: mean_depth ~ 1 + Species * season + (1 | fi_fishid) + (1 | Species)
Model 2: mean_depth ~ 1 + Species * season + (1 | fi_fishid) + (1 | date)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  18 -7748.1
2  18 -7715.0  0 66.246  < 2.2e-16 ***
```
```
lrtest(m_id_date,m_id_sp_date)
```
```
Likelihood ratio test

Model 1: mean_depth ~ 1 + Species * season + (1 | fi_fishid) + (1 | date)
Model 2: mean_depth ~ 1 + Species * season + (1 | fi_fishid) + (1 | Species) +
    (1 | date)
  #Df LogLik Df Chisq Pr(>Chisq)
1  18  -7715
2  19  -7715  1     0     0.9999
```
Model **m_id_date** is preferred

### 2.2. Find the best conditional model of Species x season controlling for body size and reservoir parts

:books:`library(AICcmodavg)`

Build a set of candidate models based on previous model selection including _body_size_ and _res_part as covariates

```
Cand.mod <- list()

Cand.mod[[1]]<-lmer(mean_depth ~ 1 + Species * season + (1| fi_fishid) + (1| date),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[2]]<-lmer(mean_depth ~ 1 + Species * season + body_size + (1| fi_fishid) + (1| date),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[3]]<-lmer(mean_depth ~ 1 + Species * season + res_part + (1| fi_fishid) + (1| date),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[4]]<-lmer(mean_depth ~ 1 + Species * season + body_size + res_part + (1| fi_fishid) + (1| date),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)

Modnames <- c("Model 1", "Model 2", "Model 3", "Model 4")
```
Create a model selection table based on AICc, better than AIC/BIC with small samples
```
aictab(cand.set = Cand.mod, modnames = Modnames)
```
```
Model selection based on AICc:

         K     AICc Delta_AICc AICcWt Cum.Wt       LL
Model 4 22 14953.32       0.00   0.51   0.51 -7454.52
Model 3 21 14953.37       0.05   0.49   1.00 -7455.56
Model 1 18 15451.20     497.88   0.00   1.00 -7707.51
Model 2 19 15451.55     498.22   0.00   1.00 -7706.67
```
Compute the evidence ratio
```
evidence(aictab(cand.set = Cand.mod, modnames = Modnames))
```
```
Evidence ratio between models 'Model 4' and 'Model 3': 1.02
```
Compute confidence set based on 'raw' method
```
confset(cand.set = Cand.mod, modnames = Modnames, second.ord = TRUE, method = "raw")
```
```
Confidence set for the best model

Method:	 raw sum of model probabilities

95% confidence set:
         K     AICc Delta_AICc AICcWt
Model 4 22 14953.32       0.00   0.51
Model 3 21 14953.37       0.05   0.49

Model probabilities sum to 1
```

### 2.3. Compare best-fit models

We compare the first two nested models (within 2 Delta_AICc) to test wether _body_size_ is important or not

```
m4<-lmer(mean_depth ~ 1 + Species * season + body_size + res_part + (1| fi_fishid) + (1| date),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m3<-lmer(mean_depth ~ 1 + Species * season + res_part + (1| fi_fishid) + (1| date),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
lrtest(m4,m3)
```
```
Likelihood ratio test

Model 1: mean_depth ~ 1 + Species * season + body_size + res_part + (1 |
    fi_fishid) + (1 | date)
Model 2: mean_depth ~ 1 + Species * season + res_part + (1 | fi_fishid) +
    (1 | date)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  22 -7454.5
2  21 -7455.6 -1 2.0703     0.1502
```

- **Model 3** is preferred so we can drop body size as a covariate

Export table from candidate models set

:books:`library(sjPlot)`  
:books:`library(sjstats)`  
:books:`library(sjmisc)`  
```
tab_model(m3,m4,m1,m2, transform = NULL, collapse.ci = F,  auto.label = FALSE,  show.se = TRUE,collapse.se = T,
              dv.labels = c("Model 1", "Model 2","Model 3","Model 4"),
               string.pred = "Variable",
               string.p = "P" , use.viewer = TRUE)
```
|  Model 1                         	|                	|               	| Model 2 	|                	|               	| Model 3 	|                	|               	| Model 4 	|                	|               	|        	|
|----------------------------------	|----------------	|---------------	|---------	|----------------	|---------------	|---------	|----------------	|---------------	|---------	|----------------	|---------------	|--------	|
| Variable                         	| Estimates      	| CI            	| P       	| Estimates      	| CI            	| P       	| Estimates      	| CI            	| P       	| Estimates      	| CI            	| P      	|
| (Intercept)                      	| 3.80 (0.80)    	| 2.23 – 5.37   	| <0.001  	| 6.32 (1.84)    	| 2.72 – 9.93   	| 0.001   	| 3.18 (0.69)    	| 1.82 – 4.54   	| <0.001  	| 5.18 (1.62)    	| 1.99 – 8.36   	| 0.001  	|
| Speciespikeperch                 	| 2.09 (0.92)    	| 0.29 – 3.88   	| 0.023   	| 0.70 (1.26)    	| -1.77 – 3.16  	| 0.579   	| 2.84 (0.80)    	| 1.27 – 4.41   	| <0.001  	| 1.74 (1.11)    	| -0.44 – 3.92  	| 0.117  	|
| Specieswels                      	| 6.24 (0.94)    	| 4.40 – 8.08   	| <0.001  	| 6.77 (0.94)    	| 4.93 – 8.60   	| <0.001  	| 6.90 (0.82)    	| 5.29 – 8.50   	| <0.001  	| 7.31 (0.83)    	| 5.69 – 8.94   	| <0.001 	|
| seasonspring_I                   	| -2.32 (0.24)   	| -2.78 – -1.86 	| <0.001  	| -2.32 (0.24)   	| -2.78 – -1.86 	| <0.001  	| -1.82 (0.25)   	| -2.31 – -1.33 	| <0.001  	| -1.82 (0.25)   	| -2.31 – -1.33 	| <0.001 	|
| seasonspring_II                  	| -2.56 (0.44)   	| -3.41 – -1.70 	| <0.001  	| -2.55 (0.44)   	| -3.40 – -1.70 	| <0.001  	| -1.85 (0.47)   	| -2.76 – -0.93 	| <0.001  	| -1.84 (0.47)   	| -2.76 – -0.93 	| <0.001 	|
| seasonsummer                     	| -0.28 (0.21)   	| -0.69 – 0.13  	| 0.175   	| -0.28 (0.21)   	| -0.69 – 0.13  	| 0.175   	| -0.20 (0.22)   	| -0.64 – 0.24  	| 0.365   	| -0.20 (0.22)   	| -0.64 – 0.24  	| 0.363  	|
| seasonwinter                     	| -0.91 (0.22)   	| -1.35 – -0.47 	| <0.001  	| -0.91 (0.22)   	| -1.35 – -0.47 	| <0.001  	| -0.40 (0.24)   	| -0.87 – 0.07  	| 0.095   	| -0.40 (0.24)   	| -0.87 – 0.07  	| 0.097  	|
| res_partmiddle                   	| 0.41 (0.12)    	| 0.17 – 0.65   	| 0.001   	| 0.41 (0.12)    	| 0.17 – 0.65   	| 0.001   	|                	|               	|         	|                	|               	|        	|
| res_parttributary                	| -1.62 (0.13)   	| -1.88 – -1.35 	| <0.001  	| -1.61 (0.13)   	| -1.87 – -1.35 	| <0.001  	|                	|               	|         	|                	|               	|        	|
| res_partupper                    	| 0.13 (0.14)    	| -0.13 – 0.40  	| 0.325   	| 0.14 (0.14)    	| -0.13 – 0.41  	| 0.299   	|                	|               	|         	|                	|               	|        	|
| Speciespikeperch:seasonspring_I  	| -1.06 (0.27)   	| -1.58 – -0.54 	| <0.001  	| -1.06 (0.27)   	| -1.58 – -0.54 	| <0.001  	| -2.23 (0.28)   	| -2.77 – -1.68 	| <0.001  	| -2.23 (0.28)   	| -2.77 – -1.68 	| <0.001 	|
| Specieswels:seasonspring_I       	| -5.47 (0.27)   	| -6.00 – -4.95 	| <0.001  	| -5.47 (0.27)   	| -6.00 – -4.95 	| <0.001  	| -6.78 (0.28)   	| -7.32 – -6.23 	| <0.001  	| -6.78 (0.28)   	| -7.32 – -6.23 	| <0.001 	|
| Speciespikeperch:seasonspring_II 	| -2.02 (0.52)   	| -3.04 – -1.01 	| <0.001  	| -2.03 (0.52)   	| -3.04 – -1.02 	| <0.001  	| -2.77 (0.55)   	| -3.85 – -1.69 	| <0.001  	| -2.78 (0.55)   	| -3.85 – -1.70 	| <0.001 	|
| Specieswels:seasonspring_II      	| -1.89 (0.49)   	| -2.86 – -0.93 	| <0.001  	| -1.90 (0.49)   	| -2.86 – -0.93 	| <0.001  	| -2.89 (0.52)   	| -3.91 – -1.86 	| <0.001  	| -2.89 (0.52)   	| -3.92 – -1.86 	| <0.001 	|
| Speciespikeperch:seasonsummer    	| -2.44 (0.24)   	| -2.90 – -1.98 	| <0.001  	| -2.44 (0.24)   	| -2.90 – -1.98 	| <0.001  	| -3.24 (0.25)   	| -3.73 – -2.75 	| <0.001  	| -3.24 (0.25)   	| -3.73 – -2.75 	| <0.001 	|
| Specieswels:seasonsummer         	| -6.08 (0.24)   	| -6.55 – -5.62 	| <0.001  	| -6.08 (0.24)   	| -6.55 – -5.62 	| <0.001  	| -7.03 (0.25)   	| -7.52 – -6.54 	| <0.001  	| -7.03 (0.25)   	| -7.51 – -6.54 	| <0.001 	|
| Speciespikeperch:seasonwinter    	| -0.77 (0.25)   	| -1.27 – -0.27 	| 0.002   	| -0.77 (0.25)   	| -1.27 – -0.27 	| 0.002   	| -1.48 (0.27)   	| -2.01 – -0.95 	| <0.001  	| -1.48 (0.27)   	| -2.01 – -0.95 	| <0.001 	|
| Specieswels:seasonwinter         	| -0.88 (0.25)   	| -1.38 – -0.39 	| <0.001  	| -0.89 (0.25)   	| -1.38 – -0.39 	| <0.001  	| -1.59 (0.27)   	| -2.12 – -1.07 	| <0.001  	| -1.60 (0.27)   	| -2.12 – -1.07 	| <0.001 	|
| body_size                        	|                	|               	|         	| -0.00 (0.00)   	| -0.01 – 0.00  	| 0.134   	|                	|               	|         	| -0.00 (0.00)   	| -0.00 – 0.00  	| 0.180  	|
| Random Effects                   	|                	|               	|         	|                	|               	|         	|                	|               	|         	|                	|               	|        	|
| s2                               	| 3.18           	|               	|         	| 3.18           	|               	|         	| 3.64           	|               	|         	| 3.64           	|               	|        	|
| t00                              	| 0.22 date      	|               	|         	| 0.22 date      	|               	|         	| 0.27 date      	|               	|         	| 0.27 date      	|               	|        	|
|                                  	| 1.22 fi_fishid 	|               	|         	| 1.03 fi_fishid 	|               	|         	| 0.92 fi_fishid 	|               	|         	| 0.80 fi_fishid 	|               	|        	|
| ICC                              	| 0.31           	|               	|         	| 0.28           	|               	|         	| 0.25           	|               	|         	| 0.23           	|               	|        	|
| N                                	| 13 fi_fishid   	|               	|         	| 13 fi_fishid   	|               	|         	| 13 fi_fishid   	|               	|         	| 13 fi_fishid   	|               	|        	|
|                                  	| 344 date       	|               	|         	| 344 date       	|               	|         	| 344 date       	|               	|         	| 344 date       	|               	|        	|
| Observations                     	| 3671           	|               	|         	| 3671           	|               	|         	| 3671           	|               	|         	| 3671           	|               	|        	|
| Marginal R2 / Conditional R2     	| 0.644 / 0.755  	|               	|         	| 0.659 / 0.756  	|               	|         	| 0.613 / 0.708  	|               	|         	| 0.626 / 0.711  	|               	|        	|

## 3. Analysis of the Species x season interaction

### 3.1. Summarise of best-fit model

```
summ(m3,  center = TRUE, scale = TRUE, n.sd = 2)
```
```
MODEL INFO:
Observations: 3671
Dependent Variable: mean_depth
Type: Mixed effects linear regression

MODEL FIT:
AIC = 14953.12, BIC = 15083.49
Pseudo-R² (fixed effects) = 0.64
Pseudo-R² (total) = 0.75

FIXED EFFECTS:
-------------------------------------------------------------------------------
                                          Est.   S.E.   t val.      d.f.      p
-------------------------------------- ------- ------ -------- --------- ------
(Intercept)                               3.80   0.80     4.75     14.08   0.00
Speciespikeperch                          2.09   0.92     2.28     13.66   0.04
Specieswels                               6.24   0.94     6.65     13.56   0.00
seasonspring_I                           -2.32   0.24    -9.87   2583.64   0.00
seasonspring_II                          -2.56   0.44    -5.86   3473.51   0.00
seasonsummer                             -0.28   0.21    -1.36   2693.79   0.18
seasonwinter                             -0.91   0.22    -4.07   2886.55   0.00
res_partmiddle                            0.41   0.12     3.34   3620.70   0.00
res_parttributary                        -1.62   0.13   -12.08   3560.08   0.00
res_partupper                             0.13   0.14     0.98   3554.37   0.33
Speciespikeperch:seasonspring_I          -1.06   0.27    -4.00   3363.59   0.00
Specieswels:seasonspring_I               -5.47   0.27   -20.51   3354.84   0.00
Speciespikeperch:seasonspring_II         -2.02   0.52    -3.92   3337.94   0.00
Specieswels:seasonspring_II              -1.89   0.49    -3.85   3347.80   0.00
Speciespikeperch:seasonsummer            -2.44   0.24   -10.38   3368.30   0.00
Specieswels:seasonsummer                 -6.08   0.24   -25.65   3360.31   0.00
Speciespikeperch:seasonwinter            -0.77   0.25    -3.03   3380.61   0.00
Specieswels:seasonwinter                 -0.88   0.25    -3.49   3374.63   0.00
-------------------------------------------------------------------------------

p values calculated using Satterthwaite d.f.

RANDOM EFFECTS:
-------------------------------------
   Group      Parameter    Std. Dev.
----------- ------------- -----------
   date      (Intercept)     0.47
 fi_fishid   (Intercept)     1.10
 Residual                    1.78
-------------------------------------

Grouping variables:
-----------------------------
   Group     # groups   ICC
----------- ---------- ------
   date        344      0.05
 fi_fishid      13      0.26
-----------------------------

Continuous predictors are mean-centered and scaled by 2 s.d.
```

### 3.2. Calculate Level-2 and Level-3 ICC indices

:books:`library(easystats)`
```
iccm.1st <- icc(m3)
print(iccm.1st)
print(iccm.1st, comp = "var")
```
```
# Intraclass Correlation Coefficient

     Adjusted ICC: 0.311
  Conditional ICC: 0.111

# Intraclass Correlation Coefficient

     Adjusted ICC: 0.311
  Conditional ICC: 0.111
```
Between levels 1 and 2
```
sum(get_re_var(m3)) / (sum(get_re_var(m3)) + get_re_var(m3, "sigma_2"))
```
Between levels 2 and 3
```
get_re_var(m3)[2] / sum(get_re_var(m3))
```

### 3.3. Pairwise comparisons

:books:`library(emmeans)`
```
emmeans(m3, pairwise ~ Species, pbkrtest.limit = 10000, lmerTest.limit = 10000)
```
```
$emmeans
 Species   emmean    SE   df lower.CL upper.CL
 pike        2.31 0.897 17.2    0.423     4.21
 pikeperch   3.14 0.520 17.5    2.049     4.24
 wels        5.69 0.568 17.2    4.489     6.88

Results are averaged over the levels of: season, res_part
Degrees-of-freedom method: kenward-roger
Confidence level used: 0.95

$contrasts
 contrast         estimate    SE   df t.ratio p.value
 pike - pikeperch    -0.83 1.036 17.2 -0.800  0.7078
 pike - wels         -3.37 1.061 17.1 -3.178  0.0143
 pikeperch - wels    -2.54 0.769 17.2 -3.306  0.0109

Results are averaged over the levels of: season, res_part
Degrees-of-freedom method: kenward-roger
P value adjustment: tukey method for comparing a family of 3 estimates
```

### 3.4. Plot main-effects

:books:`library(effects)`
```
plot(Effect(c("Species", "season"), m3),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Mean_depth_date](/Plots/Mean_depth_date_00.png "Mean_depth_date")

### 3.5. Plot marginal effects

```
plot_model(m3, type = "pred", terms = c("season", "Species")) + geom_smooth(method=glmer, se=FALSE, fullrange=TRUE)+ theme_classic()
```
![Mean_depth_date](/Plots/Mean_depth_date_0.png "Mean_depth_date")

```
plot_model(m3,  mdrt.values = "meansd", type = "pred", terms = c("Species", "season")) + geom_smooth(method=glmer, se=FALSE, fullrange=TRUE)+ theme_classic()
```
![Mean_depth_date](/Plots/Mean_depth_date_01.png "Mean_depth_date")

- While these results are not totally wrong the close-to-zero p-values might suggest there might be an issue of perfect separation
- Fitting a gamma model in this case would not solve the problem as convergence still occurs
- We need to look at the residuals but an instant plot of the linear relationship between mean depth and date can be enough to find the source

### 3.6. Plot mean depth as subject to distance range against daily date

```
ggplot(data_depth, aes(x=date, y=mean_depth ,  size = ranged2d, color=Species)) + geom_point() + geom_smooth(method=glmer, se=FALSE, fullrange=TRUE)+ theme_classic() +
labs(title = "Mean depth use and horizontal movement in three species ",
     subtitle = "Plot of mean depth at varying horizontal travel distance",
     x = "Date",
     y = "Mean depth (m)",
     size="Horizontal range (m) ",
     color="Species") +
      theme(plot.title = element_text(hjust = 0.5, size = 12,face = "bold"),
      plot.subtitle = element_text(hjust = 0.5,size = 11),
      text=element_text(size=12)) + scale_color_brewer(palette="Dark2") +
      theme(legend.background = element_rect(fill = "white"),legend.key = element_rect(fill = "white", color = NA),legend.key.size = unit(0.5, "cm"),legend.key.width = unit(0.5,"cm"))
```
![Mean_depth](/Plots/Mean_depth_date.png "Mean_depth")

- We see that between August and November 2017 _wels_ show many data points with equal values
- Wether this is indicating complete lack of vertical movement or stationarity is unlikely so it must be related to some error during the sampling process/dataset creation
- We could partilly correct this by removing those data points or computing _stan_glmer_, _brglm_ or _MCMC_ models (**pending**)

## 2) Approach 2

### 2.1. Find the best conditional model for the Species x season interaction

- We fit a series of nested random-effects models following the formula (1|date/fi_fishid) (i.e., fi_fishid nested within date: (1|date:fi_fishid) + (1|date))
- With this model we expect that _mean_depth_ daily varies (across dates) and across _fi_fishid_ within _date_ by including the random effects of date and of a date-by-fi_fishid interaction
- Since multiple fish are measured on each date, but there is only one observation per date-fi_fishid combination, we rule out the inclusion of the (1|fi_fishid) term to the model

```
Cand.mod <- list()

Cand.mod[[1]]<-lmer(mean_depth ~ 1 + Species * season + (1|date:fi_fishid) + (1|date),data = data_depth, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[2]]<-lmer(mean_depth ~ 1 + Species * season + body_size + (1|date:fi_fishid) + (1|date),data = data_depth, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[3]]<-lmer(mean_depth ~ 1 + Species * season + res_part + (1|date:fi_fishid) + (1|date),data = data_depth, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[4]]<-lmer(mean_depth ~ 1 + Species * season + body_size + res_part + (1|date:fi_fishid) + (1|date),data = data_depth, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)

Modnames <- c("Model 1", "Model 2", "Model 3", "Model 4")
```
Create a model selection table based on AICc, better than AIC/BIC with small samples
```
aictab(cand.set = Cand.mod, modnames = Modnames)
```
```
Model selection based on AICc:

         K     AICc Delta_AICc AICcWt Cum.Wt   Res.LL
Model 4 22 15830.23       0.00      1      1 -7892.98
Model 3 21 16052.54     222.31      0      1 -8005.14
Model 2 19 16133.89     303.66      0      1 -8047.84
Model 1 18 16260.17     429.94      0      1 -8111.99
```
There is no need for further comparions as **Model 4** is significantly separated in terms of AICc from the second best model

### 2.2. Compare best-fit models

We compare the first two nested models (within 2 Delta_AICc) to test wether _body_size_ is important or not

```
m4<-lmer(mean_depth ~ 1 + Species * season + body_size + res_part + (1|date:fi_fishid) + (1|date),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m3<-lmer(mean_depth ~ 1 + Species * season + res_part + (1|date:fi_fishid) + (1|date),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
Export table from candidate models set

```
tab_model(m4,m3,m1,m2, transform = NULL, collapse.ci = F,  auto.label = FALSE,  show.se = TRUE,collapse.se = T,
              dv.labels = c("Model 1", "Model 2","Model 3","Model 4"),
               string.pred = "Variable",
               string.p = "P" , use.viewer = TRUE)
```
|                                  	| Model 1             	|               	|        	| Model 2             	|               	|        	| Model 3             	|               	|        	| Model 4             	|               	|        	|
|----------------------------------	|---------------------	|---------------	|--------	|---------------------	|---------------	|--------	|---------------------	|---------------	|--------	|---------------------	|---------------	|--------	|
| Variable                         	| Estimates           	| CI            	| P      	| Estimates           	| CI            	| P      	| Estimates           	| CI            	| P      	| Estimates           	| CI            	| P      	|
| (Intercept)                      	| 6.32 (0.26)         	| 5.80 – 6.84   	| <0.001 	| 3.27 (0.19)         	| 2.91 – 3.64   	| <0.001 	| 3.18 (0.17)         	| 2.85 – 3.52   	| <0.001 	| 5.64 (0.26)         	| 5.12 – 6.15   	| <0.001 	|
| Speciespikeperch                 	| 0.66 (0.22)         	| 0.22 – 1.09   	| 0.003  	| 2.49 (0.20)         	| 2.11 – 2.88   	| <0.001 	| 2.80 (0.20)         	| 2.42 – 3.19   	| <0.001 	| 1.47 (0.22)         	| 1.03 – 1.91   	| <0.001 	|
| Specieswels                      	| 6.98 (0.19)         	| 6.61 – 7.35   	| <0.001 	| 6.46 (0.19)         	| 6.08 – 6.84   	| <0.001 	| 6.82 (0.20)         	| 6.44 – 7.21   	| <0.001 	| 7.30 (0.20)         	| 6.91 – 7.68   	| <0.001 	|
| seasonspring_I                   	| -2.25 (0.26)        	| -2.76 – -1.74 	| <0.001 	| -2.20 (0.27)        	| -2.73 – -1.68 	| <0.001 	| -1.82 (0.28)        	| -2.37 – -1.28 	| <0.001 	| -1.82 (0.27)        	| -2.35 – -1.28 	| <0.001 	|
| seasonspring_II                  	| -1.70 (0.48)        	| -2.64 – -0.76 	| <0.001 	| -2.07 (0.49)        	| -3.04 – -1.10 	| <0.001 	| -1.93 (0.51)        	| -2.93 – -0.92 	| <0.001 	| -1.63 (0.50)        	| -2.62 – -0.65 	| 0.001  	|
| seasonsummer                     	| -0.29 (0.23)        	| -0.75 – 0.16  	| 0.205  	| -0.27 (0.24)        	| -0.74 – 0.20  	| 0.262  	| -0.19 (0.25)        	| -0.68 – 0.29  	| 0.440  	| -0.24 (0.24)        	| -0.71 – 0.24  	| 0.335  	|
| seasonwinter                     	| -0.65 (0.25)        	| -1.13 – -0.16 	| 0.009  	| -0.70 (0.26)        	| -1.20 – -0.20 	| 0.006  	| -0.42 (0.26)        	| -0.93 – 0.10  	| 0.115  	| -0.32 (0.26)        	| -0.83 – 0.19  	| 0.213  	|
| body_size                        	| -0.00 (0.00)        	| -0.00 – -0.00 	| <0.001 	|                     	|               	|        	|                     	|               	|        	| -0.00 (0.00)        	| -0.00 – -0.00 	| <0.001 	|
| res_partmiddle                   	| 0.98 (0.11)         	| 0.76 – 1.20   	| <0.001 	| 0.75 (0.12)         	| 0.52 – 0.98   	| <0.001 	|                     	|               	|        	|                     	|               	|        	|
| res_parttributary                	| -0.63 (0.11)        	| -0.84 – -0.41 	| <0.001 	| -0.71 (0.11)        	| -0.93 – -0.48 	| <0.001 	|                     	|               	|        	|                     	|               	|        	|
| res_partupper                    	| 0.71 (0.11)         	| 0.49 – 0.93   	| <0.001 	| 0.26 (0.11)         	| 0.04 – 0.48   	| 0.021  	|                     	|               	|        	|                     	|               	|        	|
| Speciespikeperch:seasonspring_I  	| -1.46 (0.30)        	| -2.04 – -0.87 	| <0.001 	| -1.53 (0.31)        	| -2.13 – -0.92 	| <0.001 	| -2.18 (0.31)        	| -2.79 – -1.57 	| <0.001 	| -2.20 (0.31)        	| -2.80 – -1.60 	| <0.001 	|
| Specieswels:seasonspring_I       	| -5.71 (0.30)        	| -6.30 – -5.12 	| <0.001 	| -5.87 (0.31)        	| -6.48 – -5.26 	| <0.001 	| -6.70 (0.32)        	| -7.32 – -6.08 	| <0.001 	| -6.66 (0.31)        	| -7.27 – -6.06 	| <0.001 	|
| Speciespikeperch:seasonspring_II 	| -3.10 (0.58)        	| -4.23 – -1.97 	| <0.001 	| -2.72 (0.60)        	| -3.89 – -1.54 	| <0.001 	| -3.06 (0.61)        	| -4.27 – -1.86 	| <0.001 	| -3.29 (0.60)        	| -4.47 – -2.11 	| <0.001 	|
| Specieswels:seasonspring_II      	| -3.64 (0.55)        	| -4.72 – -2.56 	| <0.001 	| -3.40 (0.57)        	| -4.51 – -2.28 	| <0.001 	| -3.67 (0.58)        	| -4.81 – -2.52 	| <0.001 	| -3.91 (0.57)        	| -5.03 – -2.79 	| <0.001 	|
| Speciespikeperch:seasonsummer    	| -2.70 (0.26)        	| -3.21 – -2.18 	| <0.001 	| -2.78 (0.27)        	| -3.32 – -2.25 	| <0.001 	| -3.22 (0.28)        	| -3.77 – -2.67 	| <0.001 	| -3.19 (0.27)        	| -3.73 – -2.66 	| <0.001 	|
| Specieswels:seasonsummer         	| -6.27 (0.27)        	| -6.79 – -5.74 	| <0.001 	| -6.41 (0.28)        	| -6.95 – -5.87 	| <0.001 	| -6.98 (0.28)        	| -7.53 – -6.43 	| <0.001 	| -6.90 (0.27)        	| -7.44 – -6.36 	| <0.001 	|
| Speciespikeperch:seasonwinter    	| -1.00 (0.29)        	| -1.57 – -0.44 	| <0.001 	| -1.00 (0.30)        	| -1.59 – -0.42 	| 0.001  	| -1.45 (0.30)        	| -2.04 – -0.85 	| <0.001 	| -1.53 (0.30)        	| -2.12 – -0.95 	| <0.001 	|
| Specieswels:seasonwinter         	| -1.69 (0.28)        	| -2.25 – -1.13 	| <0.001 	| -1.60 (0.29)        	| -2.18 – -1.02 	| <0.001 	| -1.98 (0.30)        	| -2.57 – -1.39 	| <0.001 	| -2.14 (0.30)        	| -2.72 – -1.57 	| <0.001 	|
| Random Effects                   	|                     	|               	|        	|                     	|               	|        	|                     	|               	|        	|                     	|               	|        	|
| s2                               	| 2.27                	|               	|        	| 2.19                	|               	|        	| 1.90                	|               	|        	| 1.75                	|               	|        	|
| t00                              	| 1.85 date:fi_fishid 	|               	|        	| 2.22 date:fi_fishid 	|               	|        	| 2.77 date:fi_fishid 	|               	|        	| 2.73 date:fi_fishid 	|               	|        	|
|                                  	| 0.16 date           	|               	|        	| 0.16 date           	|               	|        	| 0.22 date           	|               	|        	| 0.23 date           	|               	|        	|
| ICC                              	| 0.07                	|               	|        	| 0.07                	|               	|        	| 0.11                	|               	|        	| 0.12                	|               	|        	|
| N                                	| 344 date            	|               	|        	| 344 date            	|               	|        	| 344 date            	|               	|        	| 344 date            	|               	|        	|
|                                  	| 13 fi_fishid        	|               	|        	| 13 fi_fishid        	|               	|        	| 13 fi_fishid        	|               	|        	| 13 fi_fishid        	|               	|        	|
| Observations                     	| 3671                	|               	|        	| 3671                	|               	|        	| 3671                	|               	|        	| 3671                	|               	|        	|
| Marginal R2 / Conditional R2     	| 0.763 / 0.779       	|               	|        	| 0.763 / 0.779       	|               	|        	| 0.774 / 0.798       	|               	|        	| 0.790 / 0.814       	|               	|        	|

## 3. Analysis of the Species x season interaction

### 3.1. Summarise of best-fit model

```
summ(m4,  center = TRUE, scale = TRUE, n.sd = 2)
```
```
MODEL INFO:
Observations: 3671
Dependent Variable: mean_depth
Type: Mixed effects linear regression

MODEL FIT:
AIC = 15778.63, BIC = 15915.21
Pseudo-R² (fixed effects) = 0.76
Pseudo-R² (total) = 0.78

FIXED EFFECTS:
-------------------------------------------------------------------------------
                                          Est.   S.E.   t val.      d.f.      p
-------------------------------------- ------- ------ -------- --------- ------
(Intercept)                               3.61   0.18    19.85   3253.83   0.00
Speciespikeperch                          0.66   0.22     2.97   3396.75   0.00
Specieswels                               6.98   0.19    36.79   3349.53   0.00
seasonspring_I                           -2.25   0.26    -8.65   2909.69   0.00
seasonspring_II                          -1.70   0.48    -3.54   3578.90   0.00
seasonsummer                             -0.29   0.23    -1.27   2997.69   0.20
seasonwinter                             -0.65   0.25    -2.63   3128.53   0.01
body_size                                -2.49   0.16   -15.77   3392.32   0.00
res_partmiddle                            0.98   0.11     8.72   3616.46   0.00
res_parttributary                        -0.63   0.11    -5.71   3561.71   0.00
res_partupper                             0.71   0.11     6.27   3557.77   0.00
Speciespikeperch:seasonspring_I          -1.46   0.30    -4.90   3360.54   0.00
Specieswels:seasonspring_I               -5.71   0.30   -18.93   3360.96   0.00
Speciespikeperch:seasonspring_II         -3.10   0.58    -5.36   3345.20   0.00
Specieswels:seasonspring_II              -3.64   0.55    -6.62   3352.99   0.00
Speciespikeperch:seasonsummer            -2.70   0.26   -10.23   3371.43   0.00
Specieswels:seasonsummer                 -6.27   0.27   -23.40   3370.99   0.00
Speciespikeperch:seasonwinter            -1.00   0.29    -3.50   3391.05   0.00
Specieswels:seasonwinter                 -1.69   0.28    -5.95   3384.61   0.00
-------------------------------------------------------------------------------

p values calculated using Satterthwaite d.f.

RANDOM EFFECTS:
------------------------------------------
     Group         Parameter    Std. Dev.
---------------- ------------- -----------
 date:fi_fishid   (Intercept)     1.36
      date        (Intercept)     0.40
    Residual                      1.51
------------------------------------------

Grouping variables:
----------------------------------
     Group        # groups   ICC
---------------- ---------- ------
 date:fi_fishid     3671     0.43
      date          344      0.04
----------------------------------

Continuous predictors are mean-centered and scaled by 2 s.d.
```

### 3.2. Calculate Level-2 and Level-3 ICC indices

:books:`library(easystats)`
```
iccm.1st <- icc(m4)
print(iccm.1st)
print(iccm.1st, comp = "var")
```
```
# Intraclass Correlation Coefficient

     Adjusted ICC: 0.065
  Conditional ICC: 0.015

# Intraclass Correlation Coefficient

     Adjusted ICC: 0.065
  Conditional ICC: 0.015
```
Between levels 1 and 2
```
sum(get_re_var(m4)) / (sum(get_re_var(m4)) + get_re_var(m4, "sigma_2"))
```
Between levels 2 and 3
```
get_re_var(m4)[2] / sum(get_re_var(m4))
```

### 3.3. Pairwise comparisons

```
emmeans(m4, pairwise ~ Species, pbkrtest.limit = 10000, lmerTest.limit = 10000)
```
```
$emmeans
 Species   emmean    SE   df lower.CL upper.CL
 pike        2.90 0.123 3557     2.66     3.14
 pikeperch   1.90 0.116 3454     1.68     2.13
 wels        6.41 0.102 3152     6.21     6.62

Results are averaged over the levels of: season, res_part
Degrees-of-freedom method: kenward-roger
Confidence level used: 0.95

$contrasts
 contrast         estimate    SE   df t.ratio p.value
 pike - pikeperch    0.993 0.183 3396   5.435 <.0001
 pike - wels        -3.518 0.137 3375 -25.692 <.0001
 pikeperch - wels   -4.511 0.184 3401 -24.531 <.0001

Results are averaged over the levels of: season, res_part
Degrees-of-freedom method: kenward-roger
P value adjustment: tukey method for comparing a family of 3 estimates
```
- In contrast to the previous results (Approach 1) differences between _pike_ and _pikeperch_ are now significant
- This result suggests that the inclusion of _body_size_ (along with _res_part_) with respect to the former best-fit model contributes to explain marginal mean depth differences and shouldnt be dropped form the model
- Next, we will analyse those differences accounting for both variables, _body_size_ and _res_part_

### 3.4. Plot main-effects

```
plot(Effect(c("Species", "season"), m4),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Mean_depth_date](/Plots/Mean_depth_date_02.png "Mean_depth_date")

### 3.5. Plot marginal effects

```
plot_model(m4, mdrt.values = "meansd", type = "pred", terms = c("season", "Species")) + geom_smooth(method=glmer, se=FALSE, fullrange=TRUE)+ theme_classic()
```
![Mean_depth_date](/Plots/Mean_depth_date_03.png "Mean_depth_date")

```
plot_model(m4,  mdrt.values = "meansd", type = "pred", terms = c("Species", "season")) + geom_smooth(method=glmer, se=FALSE, fullrange=TRUE)+ theme_classic()
```
![Mean_depth_date](/Plots/Mean_depth_date_04.png "Mean_depth_date")

- We see that the minimum and maximum values intervals are narrowed with respect to previous model plots
- Though no interaction is present in this model affecting _body_size_ or _res_part_ we may see how these variables are affecting the Cohen estimates (i.e., marginal means) in the following plots

```
plot_model(m4, mdrt.values = "meansd", type = "pred", terms = c("res_part", "Species")) + geom_smooth(method=glmer, se=FALSE, fullrange=TRUE)+ theme_classic()
```
![Mean_depth_date](/Plots/Mean_depth_date_05.png "Mean_depth_date")

```
plot_model(m4,  mdrt.values = "meansd", type = "pred", terms = c("res_part", "season")) + geom_smooth(method=glmer, se=FALSE, fullrange=TRUE)+ theme_classic()
```
![Mean_depth_date](/Plots/Mean_depth_date_06.png "Mean_depth_date")

```
plot_model(m4, mdrt.values = "meansd", type = "pred", terms = c("body_size", "Species")) + geom_smooth(method=glmer, se=FALSE, fullrange=TRUE)+ theme_classic()
```
![Mean_depth_date](/Plots/Mean_depth_date_07.png "Mean_depth_date")

```
plot_model(m4,  mdrt.values = "meansd", type = "pred", terms = c("body_size", "season")) + geom_smooth(method=glmer, se=FALSE, fullrange=TRUE)+ theme_classic()
```
![Mean_depth_date](/Plots/Mean_depth_date_08.png "Mean_depth_date")

## 3. Effects of body size and reservoir parts on mean depth use

Taking into account the former fitted models, we explore the interaction effects of reservoir parts and body size on mean depth use

### 3.1. How does mean depth vary with body size between species?

Fit a model including the _Species x body_size_ interaction
```
m_sp_size<-lmer(mean_depth ~ 1 + Species * body_size + (1|date:fi_fishid) + (1|date),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```

Compare with a model without the interaction term
```
m_sp_size_nint<-update(m_sp_size,.~.-  Species : body_size)
```
```
anova(m_sp_size, m_sp_size_nint)
```
```
Models:
m_sp_size_nint: mean_depth ~ Species + body_size + (1 | date:fi_fishid) + (1 |
m_sp_size_nint:     date)
m_sp_size: mean_depth ~ 1 + Species * body_size + (1 | date:fi_fishid) +
m_sp_size:     (1 | date)
               npar   AIC   BIC  logLik deviance  Chisq Df Pr(>Chisq)
m_sp_size_nint    7 17689 17733 -8837.6    17675
m_sp_size         9 17569 17625 -8775.5    17551 124.15  2  < 2.2e-16 ***
```
The interaction term improves the model fit over a model without it

Pairwise comparisons
```
emmeans(m_sp_size, pairwise ~ Species * body_size, pbkrtest.limit = 10000, lmerTest.limit = 10000)
```
```
$emmeans
 Species   body_size emmean    SE   df lower.CL upper.CL
 pike            877   2.82 0.205 2050     2.41     3.22
 pikeperch       877  -1.78 0.430 3669    -2.63    -0.94
 wels            877   6.45 0.155  883     6.14     6.75

Degrees-of-freedom method: kenward-roger
Confidence level used: 0.95

$contrasts
 contrast                                           estimate    SE   df t.ratio p.value
 pike,876.763824570962 - pikeperch,876.763824570962     4.60 0.449 3342  10.253 <.0001
 pike,876.763824570962 - wels,876.763824570962         -3.63 0.199 3344 -18.294 <.0001
 pikeperch,876.763824570962 - wels,876.763824570962    -8.23 0.428 3337 -19.243 <.0001

Degrees-of-freedom method: kenward-roger
P value adjustment: tukey method for comparing a family of 3 estimates
```
- Differences are significant between species with average body size

Fit the interaction model with a gamma distribution
```
m_sp_size_gamma<-glmer(mean_depth ~ 1 + Species * body_size + (1|date:fi_fishid) + (1|date),data = data_depth, control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```

Simple slope analysis of the Species x body size interaction
```
probe_interaction(m_sp_size_gamma, pred = body_size, modx = Species, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Body size (cm)", y.label = "Mean depth (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on mean depth use")
```
```
SIMPLE SLOPES ANALYSIS

When Species = wels:

                               Est.   S.E.      t val.      p
--------------------------- ------- ------ ----------- ------
Slope of body_size            -0.00   0.00      -35.08   0.00
Conditional intercept          1.49   0.00   163185.05   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.00   0.00    -6.95   0.00
Conditional intercept          0.01   0.15     0.10   0.92

When Species = pike:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.00   0.00    -1.27   0.20
Conditional intercept          0.72   0.06    12.04   0.00
```

- The slopes of _body_size_ are significant (p < 0.05) and negative in _wels_ and _pikeperch_ but not _pike_ suggesting conditional effects of larger body sizes on decreased mean depth only in those species

```
p<-interact_plot(m_sp_size_gamma, pred = body_size, modx = Species, pplot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE, ,x.label = "Body size (cm)", y.label = "Mean depth (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on mean depth use")
p + geom_smooth(method=glmer, se=FALSE, fullrange=TRUE) + theme_classic()
```

![Mean_depth_date](/Plots/Mean_depth_date_09.png "Mean_depth_date")

### 3.2. How does mean depth vary between species and reservoir parts?

Fit a model including the _Species x res_part_ interaction
```
m_sp_res<-lmer(mean_depth ~ 1 + Species * res_part + (1|date:fi_fishid) + (1|date),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
Compare with a model without the interaction term

```
m_sp_res_nint<-update(m_season_size,.~.-  Species : res_part)
```
```
anova(m_sp_res, m_sp_res_nint)
```
```
Models:
m_sp_res_nint: mean_depth ~ season + body_size + (1 | date:fi_fishid) + (1 |
m_sp_res_nint:     date) + season:body_size
m_sp_res: mean_depth ~ 1 + Species * res_part + (1 | date:fi_fishid) +
m_sp_res:     (1 | date)
              npar   AIC   BIC  logLik deviance  Chisq Df Pr(>Chisq)
m_sp_res_nint   13 17682 17763 -8828.2    17656
m_sp_res        15 17093 17186 -8531.7    17063 592.99  2  < 2.2e-16 ***
```
The interaction term improves the model fit over a model without it

Pairwise comparisons

```
emmeans(m_sp_res, pairwise ~ Species * res_part, pbkrtest.limit = 10000, lmerTest.limit = 10000)
```
```
$emmeans
 Species   res_part  emmean    SE   df lower.CL upper.CL
 pike      dam         3.31 0.275 3527     2.77     3.85
 pikeperch dam         2.89 0.166 1723     2.57     3.22
 wels      dam         7.82 0.192 2397     7.44     8.19
 pike      middle      2.76 0.184 2211     2.40     3.12
 pikeperch middle      5.21 0.164 1647     4.89     5.53
 wels      middle      6.49 0.154 1386     6.19     6.79
 pike      tributary   2.11 0.190 2349     1.74     2.49
 pikeperch tributary   3.31 0.145 1117     3.03     3.59
 wels      tributary   3.47 0.145 1108     3.18     3.75
 pike      upper       2.49 0.281 3545     1.94     3.04
 pikeperch upper       3.64 0.143 1098     3.35     3.92
 wels      upper       6.46 0.150 1256     6.17     6.75

Degrees-of-freedom method: kenward-roger
Confidence level used: 0.95

$contrasts
 contrast                               estimate    SE   df t.ratio p.value
 pike,dam - pikeperch,dam                 0.4148 0.288 3389   1.441 0.9553
 pike,dam - wels,dam                     -4.5095 0.307 3418 -14.712 <.0001
 pike,dam - pike,middle                   0.5505 0.304 3422   1.812 0.8118
 pike,dam - pikeperch,middle             -1.9027 0.289 3413  -6.575 <.0001
 pike,dam - wels,middle                  -3.1831 0.284 3413 -11.208 <.0001
 pike,dam - pike,tributary                1.1933 0.304 3405   3.927 0.0050
 pike,dam - pikeperch,tributary          -0.0042 0.277 3401  -0.015 1.0000
 pike,dam - wels,tributary               -0.1609 0.276 3400  -0.583 1.0000
 pike,dam - pike,upper                    0.8142 0.370 3425   2.201 0.5490
 pike,dam - pikeperch,upper              -0.3284 0.276 3396  -1.188 0.9899
 pike,dam - wels,upper                   -3.1541 0.280 3400 -11.283 <.0001
 pikeperch,dam - wels,dam                -4.9244 0.213 3405 -23.151 <.0001
 pikeperch,dam - pike,middle              0.1357 0.205 3384   0.661 1.0000
 pikeperch,dam - pikeperch,middle        -2.3175 0.188 3397 -12.309 <.0001
 pikeperch,dam - wels,middle             -3.5979 0.179 3395 -20.048 <.0001
 pikeperch,dam - pike,tributary           0.7785 0.210 3387   3.708 0.0114
 pikeperch,dam - pikeperch,tributary     -0.4190 0.170 3383  -2.471 0.3588
 pikeperch,dam - wels,tributary          -0.5757 0.170 3402  -3.382 0.0351
 pikeperch,dam - pike,upper               0.3993 0.296 3418   1.347 0.9727
 pikeperch,dam - pikeperch,upper         -0.7432 0.170 3384  -4.367 0.0008
 pikeperch,dam - wels,upper              -3.5689 0.175 3385 -20.435 <.0001
 wels,dam - pike,middle                   5.0600 0.228 3411  22.235 <.0001
 wels,dam - pikeperch,middle              2.6069 0.210 3407  12.386 <.0001
 wels,dam - wels,middle                   1.3265 0.205 3431   6.461 <.0001
 wels,dam - pike,tributary                5.7028 0.231 3400  24.705 <.0001
 wels,dam - pikeperch,tributary           4.5054 0.199 3450  22.586 <.0001
 wels,dam - wels,tributary                4.3486 0.201 3468  21.677 <.0001
 wels,dam - pike,upper                    5.3237 0.310 3416  17.153 <.0001
 wels,dam - pikeperch,upper               4.1812 0.196 3410  21.344 <.0001
 wels,dam - wels,upper                    1.3555 0.199 3406   6.797 <.0001
 pike,middle - pikeperch,middle          -2.4532 0.203 3388 -12.112 <.0001
 pike,middle - wels,middle               -3.7336 0.196 3397 -19.039 <.0001
 pike,middle - pike,tributary             0.6428 0.228 3421   2.823 0.1705
 pike,middle - pikeperch,tributary       -0.5547 0.189 3405  -2.943 0.1266
 pike,middle - wels,tributary            -0.7114 0.188 3413  -3.780 0.0088
 pike,middle - pike,upper                 0.2637 0.307 3423   0.858 0.9995
 pike,middle - pikeperch,upper           -0.8789 0.188 3393  -4.677 0.0002
 pike,middle - wels,upper                -3.7046 0.193 3402 -19.231 <.0001
 pikeperch,middle - wels,middle          -1.2804 0.176 3398  -7.255 <.0001
 pikeperch,middle - pike,tributary        3.0960 0.209 3407  14.790 <.0001
 pikeperch,middle - pikeperch,tributary   1.8985 0.171 3438  11.094 <.0001
 pikeperch,middle - wels,tributary        1.7418 0.171 3447  10.210 <.0001
 pikeperch,middle - pike,upper            2.7168 0.293 3412   9.261 <.0001
 pikeperch,middle - pikeperch,upper       1.5743 0.168 3403   9.352 <.0001
 pikeperch,middle - wels,upper           -1.2514 0.172 3391  -7.285 <.0001
 wels,middle - pike,tributary             4.3764 0.202 3404  21.711 <.0001
 wels,middle - pikeperch,tributary        3.1789 0.162 3437  19.683 <.0001
 wels,middle - wels,tributary             3.0222 0.162 3459  18.611 <.0001
 wels,middle - pike,upper                 3.9972 0.289 3416  13.841 <.0001
 wels,middle - pikeperch,upper            2.8547 0.158 3392  18.011 <.0001
 wels,middle - wels,upper                 0.0290 0.166 3428   0.175 1.0000
 pike,tributary - pikeperch,tributary    -1.1975 0.194 3406  -6.177 <.0001
 pike,tributary - wels,tributary         -1.3542 0.195 3425  -6.950 <.0001
 pike,tributary - pike,upper             -0.3791 0.314 3441  -1.209 0.9884
 pike,tributary - pikeperch,upper        -1.5217 0.193 3396  -7.873 <.0001
 pike,tributary - wels,upper             -4.3474 0.198 3402 -21.975 <.0001
 pikeperch,tributary - wels,tributary    -0.1567 0.145 3374  -1.080 0.9955
 pikeperch,tributary - pike,upper         0.8183 0.286 3437   2.861 0.1554
 pikeperch,tributary - pikeperch,upper   -0.3242 0.151 3427  -2.150 0.5862
 pikeperch,tributary - wels,upper        -3.1499 0.157 3438 -20.095 <.0001
 wels,tributary - pike,upper              0.9751 0.286 3442   3.408 0.0324
 wels,tributary - pikeperch,upper        -0.1675 0.149 3423  -1.123 0.9937
 wels,tributary - wels,upper             -2.9932 0.158 3470 -18.903 <.0001
 pike,upper - pikeperch,upper            -1.1425 0.283 3415  -4.031 0.0033
 pike,upper - wels,upper                 -3.9682 0.286 3414 -13.875 <.0001
 pikeperch,upper - wels,upper            -2.8257 0.153 3390 -18.422 <.0001

Degrees-of-freedom method: kenward-roger
P value adjustment: tukey method for comparing a family of 12 estimates
```

Plot main-effects

```
plot(Effect(c("Species", "res_part"), m_sp_res),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Mean_depth_date](/Plots/Mean_depth_date_1.png "Mean_depth_date")

Plot marginal effects

```
plot_model(m_sp_res, mdrt.values = "meansd", type = "pred", terms = c("Species", "res_part")) + geom_smooth(method=glmer, se=TRUE, fullrange=TRUE)+ theme_classic()
```
![Mean_depth_date](/Plots/Mean_depth_date_2.png "Mean_depth_date")

### 3.3. How does mean depth across reservoir parts and seasons?

Fit a three-way interaction model including the _Species x res_part x season_ interaction
```
m_sp_res_season_gamma<-glmer(mean_depth ~ 1 + Species * res_part * season  + (1|date:fi_fishid) + (1|date),data = data_depth, control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```
```
plot_model(m_sp_res_season_gamma, mdrt.values = "meansd", type = "pred", terms = c("season", "Species", "res_part"))
```
![Mean_depth_date](/Plots/Mean_depth_date_3.png "Mean_depth_date")

Now we fit a three-way interaction model but changing the random-effects structure following the formula (1|Species/fi_fishid) (i.e., fi_fishid nested within Species: (1|Species:fi_fishid) + (1|Species))
- With this model we expect that _mean_depth_ varies between species and across fish of the same species so accounting for variability between and within species
- Since there are multiple fish of each species and several observations per species-fi_fishid combination, we include (1|fi_fishid) term to the model

```
m_sp_res_season_gamma_1<-glmer(mean_depth ~ 1 + Species * res_part * season  + (1|Species:fi_fishid) + (1|Species) + (1|fi_fishid),data = data_depth, control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```
```
plot_model(m_sp_res_season_gamma_1, type = "pred", terms = c("season", "Species","res_part")) + geom_smooth(method=glmer, se=TRUE, fullrange=TRUE)+ theme_classic()
```
![Mean_depth_date](/Plots/Mean_depth_date_4.png "Mean_depth_date")

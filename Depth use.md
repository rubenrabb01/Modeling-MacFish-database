# Analysis of mean depth use

- We fit L2 and L3 (random slope and intercept) models with _mean_depth_ as DV and _body_size_, _Species_ and _res_part_ as predictors
- Since we expect variation in mean depth use will differ across time (non-stationary) we include the time variable both in the fixed and random parts to account for the intercept and slope

## Create a new mean depth dataframe

:books:`library(plyr)`

```
data_depth <- merge(mean_depth_dt, data.res.parts, by = c("fi_fishid", "date"))
colnames(data_depth)[5] <- "body_size"
colnames(data_depth)[8] <- "Species"
data_depth$fi_fishid <- as.factor(data_depth$fi_fishid)
data_depth$fi_species <- as.factor(data_depth$fi_species)
data_depth$season <- as.factor(data_depth$season)
data_depth$res_part <- as.factor(data_depth$res_part)
```
Add a new ordered response variable based on mean depth range data
```
data_depth$pelg_zone = cut(data_depth$mean_depth,c(0,3,6,9,12))
levels(data_depth$pelg_zone) = c("0-3","4-6","7-10",">10")
data_depth
```
|        	| fi_fishid 	| date       	| mean_depth 	| season    	| body_size 	| ca_weight_g 	| fi_sex 	| Species   	| pelg_zone 	| res_part 	|
|--------	|-----------	|------------	|------------	|-----------	|-----------	|-------------	|--------	|-----------	|-----------	|----------	|
| 1:00   	| T449202_1 	| 27/04/2017 	| 1.934759   	| spring_I  	| 430       	| 605         	| M      	| pikeperch 	| 0-3       	| middle   	|
| 2:00   	| T449202_1 	| 27/04/2017 	| 1.934759   	| spring_I  	| 430       	| 605         	| M      	| pikeperch 	| 0-3       	| upper    	|
| 3:00   	| T449202_1 	| 28/04/2017 	| 1.686897   	| spring_I  	| 430       	| 605         	| M      	| pikeperch 	| 0-3       	| middle   	|
| 4:00   	| T449202_1 	| 29/04/2017 	| 1.71538    	| spring_I  	| 430       	| 605         	| M      	| pikeperch 	| 0-3       	| middle   	|
| 5:00   	| T449202_1 	| 29/04/2017 	| 1.71538    	| spring_I  	| 430       	| 605         	| M      	| pikeperch 	| 0-3       	| upper    	|
| ---    	|           	|            	|            	|           	|           	|             	|        	|           	|           	|          	|
| 12559: 	| T449319_1 	| 21/03/2018 	| 4.539428   	| spring_II 	| 1070      	| 7700        	| M      	| wels      	| 04-jun    	| middle   	|
| 12560: 	| T449319_1 	| 22/03/2018 	| 4.539725   	| spring_II 	| 1070      	| 7700        	| M      	| wels      	| 04-jun    	| upper    	|
| 12561: 	| T449319_1 	| 22/03/2018 	| 4.539725   	| spring_II 	| 1070      	| 7700        	| M      	| wels      	| 04-jun    	| middle   	|
| 12562: 	| T449319_1 	| 23/03/2018 	| 4.527633   	| spring_II 	| 1070      	| 7700        	| M      	| wels      	| 04-jun    	| upper    	|
| 12563: 	| T449319_1 	| 23/03/2018 	| 4.527633   	| spring_II 	| 1070      	| 7700        	| M      	| wels      	| 04-jun    	| middle   	|

## Data exploration

Describe the variable _mean_depth _

:books:`library(psych)`
```
describe.by(data_depth$mean_depth, data_depth$Species)
```
```
 Descriptive statistics by group
group: pike
   vars    n mean  sd median trimmed  mad  min   max range skew kurtosis   se
X1    1 3550 3.34 2.5   3.05    3.12 2.95 0.35 31.85  31.5 1.08     4.82 0.04
-----------------------------------------------------------------------------------------------------------
group: pikeperch
   vars    n mean   sd median trimmed  mad  min   max range skew kurtosis   se
X1    1 2475 3.55 2.31   2.89    3.21 1.85 0.39 12.64 12.25 1.23     0.98 0.05
-----------------------------------------------------------------------------------------------------------
group: wels
   vars    n mean   sd median trimmed  mad  min   max range skew kurtosis   se
X1    1 6533  6.2 4.79   6.42    5.78 6.38 0.35 26.97 26.62 0.69     0.34 0.06
```
```
hist(data_depth$mean_depth, breaks = 20)
```
![Mean_depth](/Plots/Mean_depth_hist.png "Mean_depth")

## Intitial plots of depth use based on mean depth and mean depth range

:books:`library(ggplot2)`  
:books:`library(colorRamps)`  
:books:`library(ggeffects)`  
```
cbPalette <- c("#999999", "#E69F00", "#56B4E9", "#009E73", "#F0E442", "#0072B2", "#D55E00", "#CC79A7")
```
```
ggplot(data_depth, aes(x = date, y = mean_depth, colour = Species)) +
                   geom_line() +
                   labs(x ="", y = "mean depth (m)") +
                   theme_classic()
```
![Mean_depth](/Plots/Mean_depth_date_sp.png "Mean_depth")

- We see that between August and November 2017 _wels_ show many data points with equal values, which is indicating complete lack of vertical movement (i.e., stationarity)
- With these data points the best option would be to compute _stan_glmer_, _brglm_ or _MCMC_ models (**pending**)

```
ggplot(data_depth, aes(res_part, mean_depth)) +
                   geom_boxplot(aes(fill = fi_fishid)) +
                   labs(x ="", y = "mean depth (m)") +
                   theme(legend.position = "top")
```
![Mean_depth](/Plots/Mean_depth_date_id.png "Mean_depth")

## 1. Classify mean depth predictors through building of decision trees

Grow a decision tree to the data of depth use

- We build a **rpart** model using _mean_depth_ as a continous response
- In this model each node shows:
  - The predicted value of mean depth
  - The percentage of observations in the node

### 1.1. Grow a decision tree with species and season as predictors


:books:`library(rpart)`  
:books:`library(rpart.plot)`  

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

**Update**: Removed Approach 2 and resumed

### 2.1. Find the best conditional model for the Species x season interaction

:books:`library(lmtest)`

Fit nested intercept-only models with Gamma distribution including all potentially relevant random-effects and compare them with LRT

```
m_id<-lmer(mean_depth ~ 1  + Species * season +(1| fi_fishid),data = data_depth, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_sp<-lmer(mean_depth ~ 1  + Species * season + (1| fi_fishid) + (1| Species),data = data_depth, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_date<-lmer(mean_depth ~ 1  + Species * season + (1| fi_fishid) + (1| date) ,data = data_depth, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_sp_date<-lmer(mean_depth ~ 1  + Species * season + (1| fi_fishid) + (1| Species) + (1| date) ,data = data_depth, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
anova(m_id,m_id_sp)
```
```
Data: data_depth
Models:
m_id: mean_depth ~ 1 + Species * season + (1 | fi_fishid)
m_id_sp: mean_depth ~ 1 + Species * season + (1 | fi_fishid) + (1 | Species)
        npar   AIC   BIC logLik deviance Chisq Df Pr(>Chisq)
m_id      17 56743 56869 -28354    56709
m_id_sp   18 56745 56879 -28354    56709     0  1          1
```
```
anova(m_id,m_id_date)
```
```
Data: data_depth
Models:
m_id: mean_depth ~ 1 + Species * season + (1 | fi_fishid)
m_id_date: mean_depth ~ 1 + Species * season + (1 | fi_fishid) + (1 | date)
          npar   AIC   BIC logLik deviance  Chisq Df Pr(>Chisq)
m_id        17 56743 56869 -28354    56709
m_id_date   18 55504 55638 -27734    55468 1240.6  1  < 2.2e-16 ***
```
```
anova(m_id_sp,m_id_date)
```
```
Data: data_depth
Models:
m_id_sp: mean_depth ~ 1 + Species * season + (1 | fi_fishid) + (1 | Species)
m_id_date: mean_depth ~ 1 + Species * season + (1 | fi_fishid) + (1 | date)
          npar   AIC   BIC logLik deviance  Chisq Df Pr(>Chisq)
m_id_sp     18 56745 56879 -28354    56709
m_id_date   18 55504 55638 -27734    55468 1240.6  0  < 2.2e-16 ***
```
```
anova(m_id_date,m_id_sp_date)
```
```
Data: data_depth
Models:
m_id_date: mean_depth ~ 1 + Species * season + (1 | fi_fishid) + (1 | date)
m_id_sp_date: mean_depth ~ 1 + Species * season + (1 | fi_fishid) + (1 | Species) +
m_id_sp_date:     (1 | date)
             npar   AIC   BIC logLik deviance Chisq Df Pr(>Chisq)
m_id_date      18 55504 55638 -27734    55468
m_id_sp_date   19 55506 55648 -27734    55468     0  1          1
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

         K     AICc Delta_AICc AICcWt Cum.Wt        LL
Model 3 21 55298.19       0.00   0.73   0.73 -27628.06
Model 4 22 55300.14       1.95   0.27   1.00 -27628.03
Model 1 18 55504.39     206.20   0.00   1.00 -27734.17
Model 2 19 55506.24     208.05   0.00   1.00 -27734.09
```
Compute the evidence ratio
```
evidence(aictab(cand.set = Cand.mod, modnames = Modnames))
```
```
Evidence ratio between models 'Model 3' and 'Model 4': 2.65
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
Model 3 21 55298.19       0.00   0.73
Model 4 22 55300.14       1.95   0.27

Model probabilities sum to 1
```

### 2.3. Compare best-fit models

We compare the first two nested models (within 2 Delta_AICc) to test wether _body_size_ is important or not

```
m1<-lmer(mean_depth ~ 1 + Species * season + (1| fi_fishid) + (1| date), data = data_depth, REML=F, control=lmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m2<-lmer(mean_depth ~ 1 + Species * season + body_size + (1| fi_fishid) + (1| date), data = data_depth, REML=F, control=lmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m3<-lmer(mean_depth ~ 1 + Species * season + res_part + (1| fi_fishid) + (1| date), data = data_depth, REML=F, control=lmerControl(optimizer = "bobyqa", optCtrl=list(maxfun=2e5), check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m4<-lmer(mean_depth ~ 1 + Species * season + body_size + res_part + (1| fi_fishid) + (1| date), data = data_depth, REML=F, control=lmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
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
  #Df LogLik Df  Chisq Pr(>Chisq)
1  22 -27628
2  21 -27628 -1 0.0564     0.8122
```

- **Model 3** is significantly better than **Model 4** so we can drop body size as a covariate

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
| Model 1                          	|                	|               	| Model 2 	|                	|               	| Model 3 	|                	|               	| Model 4 	|                	|               	|        	|
|----------------------------------	|----------------	|---------------	|---------	|----------------	|---------------	|---------	|----------------	|---------------	|---------	|----------------	|---------------	|--------	|
| Variable                         	| Estimates      	| CI            	| P       	| Estimates      	| CI            	| P       	| Estimates      	| CI            	| P       	| Estimates      	| CI            	| P      	|
| (Intercept)                      	| 3.30 (0.40)    	| 2.51 – 4.09   	| <0.001  	| 3.49 (0.88)    	| 1.76 – 5.22   	| <0.001  	| 3.38 (0.40)    	| 2.59 – 4.16   	| <0.001  	| 3.69 (0.87)    	| 1.98 – 5.41   	| <0.001 	|
| Speciespikeperch                 	| 2.53 (0.61)    	| 1.33 – 3.72   	| <0.001  	| 2.47 (0.66)    	| 1.17 – 3.76   	| <0.001  	| 2.46 (0.61)    	| 1.27 – 3.64   	| <0.001  	| 2.35 (0.66)    	| 1.07 – 3.64   	| <0.001 	|
| Specieswels                      	| 6.88 (0.51)    	| 5.88 – 7.88   	| <0.001  	| 7.00 (0.74)    	| 5.56 – 8.44   	| <0.001  	| 6.88 (0.51)    	| 5.89 – 7.88   	| <0.001  	| 7.09 (0.73)    	| 5.66 – 8.52   	| <0.001 	|
| seasonspring_I                   	| -1.22 (0.19)   	| -1.59 – -0.85 	| <0.001  	| -1.22 (0.19)   	| -1.59 – -0.85 	| <0.001  	| -1.18 (0.19)   	| -1.56 – -0.81 	| <0.001  	| -1.18 (0.19)   	| -1.56 – -0.81 	| <0.001 	|
| seasonspring_II                  	| -1.70 (0.29)   	| -2.28 – -1.12 	| <0.001  	| -1.70 (0.29)   	| -2.28 – -1.12 	| <0.001  	| -1.62 (0.30)   	| -2.21 – -1.04 	| <0.001  	| -1.62 (0.30)   	| -2.21 – -1.04 	| <0.001 	|
| seasonsummer                     	| 0.21 (0.16)    	| -0.11 – 0.53  	| 0.189   	| 0.21 (0.16)    	| -0.11 – 0.53  	| 0.189   	| 0.22 (0.17)    	| -0.11 – 0.54  	| 0.192   	| 0.22 (0.17)    	| -0.11 – 0.54  	| 0.192  	|
| seasonwinter                     	| 0.43 (0.17)    	| 0.11 – 0.76   	| 0.009   	| 0.43 (0.17)    	| 0.11 – 0.76   	| 0.009   	| 0.45 (0.17)    	| 0.12 – 0.78   	| 0.007   	| 0.45 (0.17)    	| 0.12 – 0.78   	| 0.007  	|
| res_partmiddle                   	| 0.23 (0.06)    	| 0.11 – 0.35   	| <0.001  	| 0.23 (0.06)    	| 0.11 – 0.35   	| <0.001  	|                	|               	|         	|                	|               	|        	|
| res_parttributary                	| -0.85 (0.09)   	| -1.02 – -0.68 	| <0.001  	| -0.85 (0.09)   	| -1.02 – -0.68 	| <0.001  	|                	|               	|         	|                	|               	|        	|
| res_partupper                    	| -0.03 (0.08)   	| -0.17 – 0.12  	| 0.723   	| -0.03 (0.08)   	| -0.17 – 0.12  	| 0.724   	|                	|               	|         	|                	|               	|        	|
| Speciespikeperch:seasonspring_I  	| -1.93 (0.18)   	| -2.28 – -1.59 	| <0.001  	| -1.93 (0.18)   	| -2.28 – -1.59 	| <0.001  	| -2.21 (0.18)   	| -2.55 – -1.86 	| <0.001  	| -2.21 (0.18)   	| -2.55 – -1.86 	| <0.001 	|
| Specieswels:seasonspring_I       	| -7.37 (0.14)   	| -7.64 – -7.09 	| <0.001  	| -7.37 (0.14)   	| -7.64 – -7.09 	| <0.001  	| -7.59 (0.14)   	| -7.87 – -7.32 	| <0.001  	| -7.59 (0.14)   	| -7.87 – -7.32 	| <0.001 	|
| Speciespikeperch:seasonspring_II 	| -2.57 (0.33)   	| -3.21 – -1.92 	| <0.001  	| -2.57 (0.33)   	| -3.21 – -1.92 	| <0.001  	| -2.69 (0.33)   	| -3.34 – -2.04 	| <0.001  	| -2.69 (0.33)   	| -3.34 – -2.04 	| <0.001 	|
| Specieswels:seasonspring_II      	| -3.19 (0.26)   	| -3.70 – -2.68 	| <0.001  	| -3.19 (0.26)   	| -3.70 – -2.68 	| <0.001  	| -3.32 (0.26)   	| -3.84 – -2.81 	| <0.001  	| -3.32 (0.26)   	| -3.84 – -2.81 	| <0.001 	|
| Speciespikeperch:seasonsummer    	| -2.90 (0.16)   	| -3.20 – -2.59 	| <0.001  	| -2.90 (0.16)   	| -3.20 – -2.59 	| <0.001  	| -3.16 (0.16)   	| -3.47 – -2.86 	| <0.001  	| -3.16 (0.16)   	| -3.47 – -2.86 	| <0.001 	|
| Specieswels:seasonsummer         	| -7.74 (0.12)   	| -7.98 – -7.50 	| <0.001  	| -7.74 (0.12)   	| -7.98 – -7.50 	| <0.001  	| -7.95 (0.12)   	| -8.19 – -7.71 	| <0.001  	| -7.95 (0.12)   	| -8.19 – -7.71 	| <0.001 	|
| Speciespikeperch:seasonwinter    	| -2.42 (0.17)   	| -2.75 – -2.10 	| <0.001  	| -2.42 (0.17)   	| -2.75 – -2.10 	| <0.001  	| -2.53 (0.17)   	| -2.85 – -2.20 	| <0.001  	| -2.52 (0.17)   	| -2.85 – -2.20 	| <0.001 	|
| Specieswels:seasonwinter         	| -1.21 (0.12)   	| -1.45 – -0.96 	| <0.001  	| -1.21 (0.12)   	| -1.45 – -0.96 	| <0.001  	| -1.27 (0.13)   	| -1.51 – -1.02 	| <0.001  	| -1.27 (0.13)   	| -1.51 – -1.02 	| <0.001 	|
| body_size                        	|                	|               	|         	| -0.00 (0.00)   	| -0.00 – 0.00  	| 0.812   	|                	|               	|         	| -0.00 (0.00)   	| -0.00 – 0.00  	| 0.687  	|
| Random Effects                   	|                	|               	|         	|                	|               	|         	|                	|               	|         	|                	|               	|        	|
| s2                               	| 4.47           	|               	|         	| 4.47           	|               	|         	| 4.54           	|               	|         	| 4.54           	|               	|        	|
| t00                              	| 0.75 date      	|               	|         	| 0.75 date      	|               	|         	| 0.79 date      	|               	|         	| 0.79 date      	|               	|        	|
|                                  	| 1.47 fi_fishid 	|               	|         	| 1.47 fi_fishid 	|               	|         	| 1.45 fi_fishid 	|               	|         	| 1.44 fi_fishid 	|               	|        	|
| ICC                              	| 0.33           	|               	|         	| 0.33           	|               	|         	| 0.33           	|               	|         	| 0.33           	|               	|        	|
| N                                	| 31 fi_fishid   	|               	|         	| 31 fi_fishid   	|               	|         	| 31 fi_fishid   	|               	|         	| 31 fi_fishid   	|               	|        	|
|                                  	| 344 date       	|               	|         	| 344 date       	|               	|         	| 344 date       	|               	|         	| 344 date       	|               	|        	|
| Observations                     	| 12558          	|               	|         	| 12558          	|               	|         	| 12558          	|               	|         	| 12558          	|               	|        	|
| Marginal R2 / Conditional R2     	| 0.604 / 0.736  	|               	|         	| 0.605 / 0.736  	|               	|         	| 0.597 / 0.730  	|               	|         	| 0.598 / 0.731  	|               	|        	|

## 3. Analysis of the Species x season interaction

### 3.1. Calculate Level-2 and Level-3 ICC indices

:books:`library(easystats)`
```
iccm.1st <- icc(m3)
print(iccm.1st)
print(iccm.1st, comp = "var")
```
```
# Intraclass Correlation Coefficient

     Adjusted ICC: 0.332
  Conditional ICC: 0.132

# Intraclass Correlation Coefficient

     Adjusted ICC: 0.332
  Conditional ICC: 0.132
```
Between levels 1 and 2
```
sum(get_re_var(m3)) / (sum(get_re_var(m3)) + get_re_var(m3, "sigma_2"))
```
Between levels 2 and 3
```
get_re_var(m3)[2] / sum(get_re_var(m3))
```

### 3.2. Pairwise comparisons

:books:`library(emmeans)`
```
emmeans(m3, pairwise ~ Species, pbkrtest.limit = 10000, lmerTest.limit = 10000)
```
```
$emmeans
 Species   emmean    SE  df asymp.LCL asymp.UCL
 pike        2.68 0.392 Inf      1.92      3.45
 pikeperch   3.25 0.466 Inf      2.33      4.16
 wels        5.66 0.331 Inf      5.01      6.31

Results are averaged over the levels of: season, res_part
Degrees-of-freedom method: asymptotic
Confidence level used: 0.95

$contrasts
 contrast         estimate    SE  df z.ratio p.value
 pike - pikeperch   -0.565 0.604 Inf -0.935  0.6178
 pike - wels        -2.976 0.506 Inf -5.875  <.0001
 pikeperch - wels   -2.411 0.566 Inf -4.257  0.0001

Results are averaged over the levels of: season, res_part
Degrees-of-freedom method: asymptotic
P value adjustment: tukey method for comparing a family of 3 estimates
```

### 3.3. Plot main-effects

:books:`library(effects)`  
:books:`library(colorRamps)`  

```
plot(Effect(c("Species", "season"), m3),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Mean_depth_date](/Plots/Mean_depth_date_00.png "Mean_depth_date")

### 3.4. Plot marginal effects

```
ggplot(data_depth, aes(Species, mean_depth)) +
                   geom_boxplot(aes(fill = season)) +
                   theme(legend.position = "top") +
                   labs(x ="Species", y = "mean depth (m)") +
                   scale_fill_manual(values = c("#00AFBB", "#E7B800", "#FC4E07", "#52854C", "#293352"))
```
![Mean_depth_date](/Plots/Mean_depth_date_0.png "Mean_depth_date")

## 4. How does mean depth vary with body size between species?

Fit a model including the _Species x body_size_ interaction
```
m_sp_size<-lmer(mean_depth ~ 1 + Species * body_size + (1| fi_fishid) + (1| date),data = data_depth, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
summ(m_sp_size,  center = TRUE, scale = TRUE, n.sd = 2)
```
```
MODEL INFO:
Observations: 12558
Dependent Variable: mean_depth
Type: Mixed effects linear regression

MODEL FIT:
AIC = 61732.32, BIC = 61799.26
Pseudo-R² (fixed effects) = 0.10
Pseudo-R² (total) = 0.56

FIXED EFFECTS:
-----------------------------------------------------------------------
                                    Est.   S.E.   t val.    d.f.      p
-------------------------------- ------- ------ -------- ------- ------
(Intercept)                         3.17   0.57     5.52   34.38   0.00
Speciespikeperch                   -2.74   3.20    -0.86   30.88   0.40
Specieswels                         3.10   0.72     4.32   30.71   0.00
body_size                          -1.14   1.44    -0.79   30.77   0.44
Speciespikeperch:body_size         -4.92   5.25    -0.94   30.92   0.36
Specieswels:body_size               0.96   1.69     0.57   30.76   0.57
-----------------------------------------------------------------------

p values calculated using Satterthwaite d.f.

RANDOM EFFECTS:
-------------------------------------
   Group      Parameter    Std. Dev.
----------- ------------- -----------
   date      (Intercept)     2.49
 fi_fishid   (Intercept)     1.14
 Residual                    2.68
-------------------------------------

Grouping variables:
-----------------------------
   Group     # groups   ICC
----------- ---------- ------
   date        344      0.42
 fi_fishid      31      0.09
-----------------------------

Continuous predictors are mean-centered and scaled by 2 s.d.
```
- The interaction is not significant
- Compare with a model without the interaction term
```
m_sp_size_nint<-update(m_sp_size,.~.-  Species : body_size)
```
```
anova(m_sp_size, m_sp_size_nint)
```
```
Data: data_depth
Models:
m_sp_size_nint: mean_depth ~ Species + body_size + (1 | fi_fishid) + (1 | date)
m_sp_size: mean_depth ~ 1 + Species * body_size + (1 | fi_fishid) + (1 |
m_sp_size:     date)
               npar   AIC   BIC logLik deviance  Chisq Df Pr(>Chisq)
m_sp_size_nint    7 61730 61782 -30858    61716
m_sp_size         9 61732 61799 -30857    61714 1.5001  2     0.4723
```
The interaction term does not improve the model fit over a model without it

Pairwise comparisons betwee species
```
emmeans(m_sp_size_nint, pairwise ~ Species , pbkrtest.limit = 10000, lmerTest.limit = 10000)
```
```
$emmeans
 Species   emmean    SE  df asymp.LCL asymp.UCL
 pike        3.34 0.455 Inf      2.45      4.23
 pikeperch   3.83 0.661 Inf      2.53      5.12
 wels        6.42 0.447 Inf      5.54      7.29

Degrees-of-freedom method: asymptotic
Confidence level used: 0.95

$contrasts
 contrast         estimate    SE  df z.ratio p.value
 pike - pikeperch   -0.486 0.630 Inf -0.772  0.7205
 pike - wels        -3.078 0.706 Inf -4.358  <.0001
 pikeperch - wels   -2.591 0.933 Inf -2.776  0.0152

Degrees-of-freedom method: asymptotic
P value adjustment: tukey method for comparing a family of 3 estimates
```

### 5. How does mean depth vary between reservoir parts?

Now we fit models with Gamma distribution changing the random-effects structure following the formula (1|Species/fi_fishid) (i.e., fi_fishid nested within Species: (1|Species:fi_fishid) + (1|Species))
- With this model we expect that _mean_depth_ varies between species and across fish of the same species so accounting for variability between and within species
- Since there are multiple fish of each species and several observations per species-fi_fishid combination, we include (1|fi_fishid) term to the model

#### 5.1. Fit a model including the _Species x res_part_ interaction

```
m_sp_res_gamma<-glmer(mean_depth ~ 1 + Species * res_part + (1|Species:fi_fishid) + (1|Species) + (1|fi_fishid),data = data_depth, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))

```
Compare with a model without the interaction term

```
m_sp_res_gamma_nint<-update(m_sp_res_gamma,.~.-  Species : res_part)
```
```
anova(m_sp_res_gamma, m_sp_res_gamma_nint)
```
```
Data: data_depth
Models:
m_sp_res_gamma_nint: mean_depth ~ Species + res_part + (1 | Species:fi_fishid) + (1 |
m_sp_res_gamma_nint:     Species) + (1 | fi_fishid)
m_sp_res_gamma: mean_depth ~ 1 + Species * res_part + (1 | Species:fi_fishid) +
m_sp_res_gamma:     (1 | Species) + (1 | fi_fishid)
                     npar   AIC   BIC logLik deviance Chisq Df Pr(>Chisq)
m_sp_res_gamma_nint   10 60805 60879 -30392    60785
m_sp_res_gamma        16 60379 60498 -30174    60347 437.9  6  < 2.2e-16 ***
```
- The interaction term improves the model fit over a model without it

Pairwise comparisons betwee species
```
emmeans(m_sp_res_gamma, pairwise ~ Species , pbkrtest.limit = 10000, lmerTest.limit = 10000)
```
```
$emmeans
 Species   emmean     SE  df asymp.LCL asymp.UCL
 pike        1.19 0.0958 Inf     0.998      1.37
 pikeperch   1.20 0.1132 Inf     0.977      1.42
 wels        1.59 0.0801 Inf     1.437      1.75

Results are averaged over the levels of: res_part
Results are given on the log (not the response) scale.
Confidence level used: 0.95

$contrasts
 contrast         estimate    SE  df z.ratio p.value
 pike - pikeperch  -0.0125 0.148 Inf -0.084  0.9961
 pike - wels       -0.4077 0.125 Inf -3.266  0.0031
 pikeperch - wels  -0.3952 0.139 Inf -2.850  0.0122

Results are averaged over the levels of: res_part
Results are given on the log (not the response) scale.
P value adjustment: tukey method for comparing a family of 3 estimates
```
**Plot main and marginal effects**

```
plot(Effect(c("Species", "res_part"), m_sp_res_gamma),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Mean_depth_date](/Plots/Mean_depth_date_1.png "Mean_depth_date")

```
ggplot(data_depth, aes(Species, mean_depth)) +
                   geom_boxplot(fill = "#F4EDCA", color = "#D16103") +
                   geom_boxplot(aes(fill = res_part)) +
                   theme(legend.position = "top") +
                   scale_fill_manual(values = c("#00AFBB", "#E7B800", "#FC4E07", "#52854C", "#293352"))
```
![Mean_depth_date](/Plots/Mean_depth_date_11.png "Mean_depth_date")

#### 5.2. Fit a three way interaction model _Species x res_part x season_

```
m_sp_res_season_gamma<-glmer(mean_depth ~ 1 + Species * res_part * season  + (1|Species:fi_fishid) + (1|Species) + (1|fi_fishid),data = data_depth, control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```
```
plot_model(m_sp_res_season_gamma, mdrt.values = "meansd", type = "pred", terms = c("season", "Species", "res_part")) + geom_smooth(method=glmer, se=FALSE, fullrange=TRUE)
```
![Mean_depth_date](/Plots/Mean_depth_date_2.png "Mean_depth_date")

### 6. Are there any selection gradients on mean depth range between reservoir parts and across seasons? Are there any effects of body size?

- We fit ordinal logistic regression (i.e., proportional odds logit) models using the ordered variable _pelg_zone_ as the response
- First, take a look at depth range by reservoir part and season

To prevent NA category in labels
```
data_depth<-data_depth[complete.cases(data_depth), ]
```
```
ggplot(data_depth, aes(x = date, fill = pelg_zone)) +
                   geom_bar(mapping = aes(x = date, fill = pelg_zone), position = "fill") +
                   scale_fill_manual(values = cbPalette) +
                   theme_minimal()
```
![Mean_depth](/Plots/Mean_depth_range_date.png "Mean_depth")

```
ggplot(data_depth, aes(x = res_part, fill = pelg_zone)) +
                   geom_bar(res_part = "res_part") +
                   scale_fill_manual(values = cbPalette) +
                   theme_minimal()+
                   facet_wrap(~Species)
                   # + scale_fill_discrete(na.translate=FALSE)   ##not necessary as previously used complete.cases
```
![Mean_depth_date](/Plots/Mean_depth_range_res_part.png "Mean_depth_date")

```
ggplot(data_depth, aes(x = season, fill = pelg_zone)) +
                   geom_bar(res_part = "res_part") +
                   scale_fill_manual(values = cbPalette) +
                   theme_minimal()+
                   facet_wrap(~Species)
```
![Mean_depth_date](/Plots/Mean_depth_range_season.png "Mean_depth_date")

#### 6.1. Fit a model including the _Species x res_part_ interaction

```
m_pelg_zone_order<-clmm(pelg_zone ~ 1 + Species * res_part + (1|fi_fishid)+(1|date),data = data_depth, link="logit",Hess=T)
```
```
plot(Effect(c("Species", "res_part"), m_pelg_zone_order),lines=list(multiline=TRUE), rug = FALSE, layout=c(2, 2))
```
![Mean_depth_date](/Plots/Mean_depth_range_date_30.png "Mean_depth_date")

```
plot(allEffects(m_pelg_zone_order),ylab="Response",type="probability",style="stacked",colors=colorRampPalette(c("white","red"))(6))
```
![Mean_depth_date](/Plots/Mean_depth_range_date_3.png "Mean_depth_date")

#### 6.2. Fit a three way interaction model _Species x res_part x season_

In this case, to avoid errors computing vcov we need to specify a larger range for the ordered response variable

```
data_depth$pelg_zone1 = cut(data_depth$mean_depth,c(0,2,5,8,11,13))
levels(data_depth$pelg_zone1) = c("0-2","3-5","6-8","9-11",">11")
```
```
m_pelg_zone_order_3int<-clmm(pelg_zone1 ~ 1 + Species * res_part * season + (1|fi_fishid)+(1|date),data = data_depth, link="logit",Hess=T)
```
```
plot(allEffects(m_pelg_zone_order_3int),ylab="Response",type="probability",style="stacked",colors=colorRampPalette(c("white","red"))(6))
```
![Mean_depth_date](/Plots/Mean_depth_range_date_4.png "Mean_depth_date")

#### 6.3. Fit a model with the _Species x body_size_ interaction

```
m_pelg_zone_order_bs<-clmm(pelg_zone1 ~ 1 + Species * body_size + (1|fi_fishid)+(1|date),data = data_depth, link="logit",Hess=T)
```
```
summary(m_pelg_zone_order_bs)
```
```
Cumulative Link Mixed Model fitted with the Laplace approximation

formula: pelg_zone ~ 1 + Species * body_size + (1 | fi_fishid) + (1 |      date)
data:    data_depth

 link  threshold nobs  logLik    AIC      niter      max.grad cond.H
 logit flexible  11904 -11556.69 23133.39 1006(5632) 2.71e-02 4.6e+08

Random effects:
 Groups    Name        Variance Std.Dev.
 date      (Intercept) 3.5387   1.8811
 fi_fishid (Intercept) 0.7718   0.8785
Number of groups:  date 344,  fi_fishid 31

Coefficients:
                            Estimate Std. Error z value Pr(>|z|)
Speciespikeperch            3.127847   2.884353   1.084    0.278
Specieswels                 0.097272   1.631091   0.060    0.952
body_size                  -0.001575   0.001566  -1.006    0.314
Speciespikeperch:body_size -0.006711   0.005686  -1.180    0.238
Specieswels:body_size       0.001916   0.001832   1.046    0.296

Threshold coefficients:
         Estimate Std. Error z value
0-3|4-6   -0.7674     1.1537  -0.665
4-6|7-10   0.9081     1.1536   0.787
7-10|>10   2.7635     1.1540   2.395
```
```
plot(Effect(c("Species", "body_size"), m_pelg_zone_order_bs),lines=list(multiline=TRUE), rug = FALSE, layout=c(2, 2))
```
![Mean_depth_date](/Plots/Mean_depth_range_date_5.png "Mean_depth_date")


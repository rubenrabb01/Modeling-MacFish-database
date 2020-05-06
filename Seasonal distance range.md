# How does distance travel range seasonally change?

## Create a dataframe including seasonal range and reservoir parts

:books:`library(plyr)`  
:books:`library(data.table)`  

To do this, create a new seasonal range dataframe (see page "Common code") but this time including reservoir parts (the first three steps are the same as in previous code)
```
dist.range.season$season <- factor(dist.range.season$season, levels=c("spring_I","spring_II","autumn","summer","winter"))
data_total_res<- merge(mean.ranged2d, data.res.parts, by = c("fi_fishid", "date"))
data_total_res<- dcast(setDT(data_total_res), fi_fishid+season~res_part, length)
data_distr_season<- merge(dist.range.season, data_total_res, by = c("fi_fishid","season"))
```
Rename some variables
```
colnames(data_distr_season)[5] <- "body_size"
colnames(data_distr_season)[8] <- "Species"
```
Convert the variable _season_ into a time vector of repeated-measures
```
data_distr_season$season<-revalue(data_distr_season$season, c("spring_I"="0","spring_II"="1","autumn"="2","summer"="3","winter"="4"))
```
Convert variables including _season_ to integer to allow correct ordering (from months 10-12 2018)
```
data_distr_season$season <- as.integer(data_distr_season$season)
data_distr_season$res_part <- as.factor(data_distr_season$res_part)
data_distr_season$fi_fishid <- as.factor(data_distr_season$fi_fishid)
data_distr_season$Species <- as.factor(data_distr_season$Species)
```
Order by _fi_fishid_ and _season_
```
data_distr_season<-data_distr_season[with(data_distr_season, order(fi_fishid, season)),]
data_distr_season$season <- as.factor(data_distr_season$season)
data_distr_season
```
| fi_fishid | Species   | season | dist.range | dam | middle | tributary | upper | body_size | ca_weight_g | day_count |
|-----------|-----------|--------|------------|-----|--------|-----------|-------|-----------|-------------|-----------|
| T449202_1 | pikeperch | 0      | 349.268    | 0   | 4      | 0         | 3     | 430       | 605         | 4         |
| T449202_1 | pikeperch | 1      | 4878.4207  | 0   | 20     | 13        | 21    | 430       | 605         | 31        |
| T449202_1 | pikeperch | 2      | 1383.4471  | 0   | 0      | 29        | 4     | 430       | 605         | 29        |
| T449202_1 | pikeperch | 3      | 946.5576   | 0   | 0      | 31        | 0     | 430       | 605         | 31        |
| T449203_1 | pike      | 0      | 1862.7459  | 1   | 3      | 4         | 4     | 1160      | 10900       | 4         |
| T449203_1 | pike      | 1      | 5877.5175  | 1   | 18     | 19        | 25    | 1160      | 10900       | 31        |
| T449203_1 | pike      | 2      | 4247.3792  | 1   | 6      | 24        | 6     | 1160      | 10900       | 26        |
| T449203_1 | pike      | 3      | 2562.5952  | 0   | 1      | 20        | 7     | 1160      | 10900       | 21        |
| T449268_1 | wels      | 0      | 877.93632  | 0   | 4      | 0         | 4     | 1620      | 25100       | 4         |
| T449268_1 | wels      | 1      | 2348.48336 | 0   | 22     | 0         | 27    | 1620      | 25100       | 30        |
| T449268_1 | wels      | 2      | 1980.08735 | 0   | 29     | 0         | 13    | 1620      | 25100       | 29        |
| T449268_1 | wels      | 3      | 3025.401   | 0   | 23     | 0         | 17    | 1620      | 25100       | 31        |

Summary of the variable _dist_range_
```
ddply(data_distr_season,.(Species),summarize,mean=mean(dist.range),sd=sd(dist.range),nobs=length(unique(fi_fishid)))
```
```
  Species     mean       sd nobs
1      pike 3018.453 2055.960   10
2 pikeperch 2891.572 1928.581    7
3      wels 2873.156 1901.103   14
```
```
hist(data_distr_season$dist.range, breaks = 20)
```
![Dist_range](/Plots/Dist_range_season_hist.png "Dist_range")

Check for correlations between _distance_range_ and reservoir parts

:books:`library(corrplot)`
```
corr<-cor(data_distr_season[,c(3,9,10,11,12)], use="pairwise", method="spearman")
corrplot(corr,method="number")
```
![Dist_range_season](/Plots/Dist_range_corr.png "Dist_range_season")

## Fit Mixed-Effects Models (LMM) to the data of seasonal distance range and use of reservoir parts

### 1. Find the best unconditional model fitted via REML

#### 1.1. Fit nested intercept-only models including all potentially relevant random-effects and compare them with LRT

:books:`library(lmtest)`

```
m_int_1<-lmer(dist.range ~ 1  + (1| Species) + (1| fi_fishid),data = data_distr_season, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_int_2<-lmer(dist.range ~ 1  + (1| Species) + (1| season),data = data_distr_season, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_int_3<-lmer(dist.range ~ 1  + (1| fi_fishid) + (1| Species) + (1| season),data = data_distr_season, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
Since models **m_int_1** and **m_int_2** are not nested they are compared using their difference in LogLik as follows:
```
0.5*(1 - pchisq(3.5, 1)) + 0.5*(1 - pchisq(3.5, 2))
```
```
[1] 0.1175714
```
```
lrtest(m_int_1,m_int_3)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + (1 | Species) + (1 | fi_fishid)
Model 2: dist.range ~ 1 + (1 | fi_fishid) + (1 | Species) + (1 | season)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   4 -1167.4
2   5 -1166.6  1 1.5693     0.2103
```
```
lrtest(m_int_2,m_int_3)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + (1 | Species) + (1 | season)
Model 2: dist.range ~ 1 + (1 | fi_fishid) + (1 | Species) + (1 | season)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   4 -1170.9
2   5 -1166.6  1 8.5565   0.003443 **
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1```
```
Model **m_int_3** is chosen

#### 1.2. Fit nested slope models including all potentially relevant random-effects and compare them with LRT

```
m_sl_1<-lmer(dist.range ~ 1  + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_sl_2<-lmer(dist.range ~ 1  + (1 + season| Species:fi_fishid)+(1 + season| Species) + (1|season),data = data_distr_season, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
lrtest(m_sl_1,m_sl_2)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + (1 + season | Species:fi_fishid) + (1 + season |
    Species)
Model 2: dist.range ~ 1 + (1 + season | Species:fi_fishid) + (1 + season |
    Species) + (1 | season)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   8 -1164.9
2   9 -1164.8  1 0.0665     0.7965
```
Model **m_sl_1** is chosen

#### 1.3. Compare the best intercept and slope models

```
0.5*(1 - pchisq(13.3, 1)) + 0.5*(1 - pchisq(13.3, 2))
```
```
[1] 0.0007797141
```
- The Chi-square test p < 0.05 indicating there is significant evidence in support of the larger model **m_sl_1**

### 2. Find the best conditional LMMs fitted via ML including the RF structure of the previously selected unconditional model

:books:`library(AICcmodavg)`

Build a set of candidate models fitted with setting **REML=FALSE**

```
Cand.mod <- list()

Cand.mod[[1]]<-lmer(dist.range ~ 1 + season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[2]]<-lmer(dist.range ~ 1 + body_size + season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[3]]<-lmer(dist.range ~ 1 + body_size * season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[4]]<-lmer(dist.range ~ 1 + Species + season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[5]]<-lmer(dist.range ~ 1 + Species * season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[6]]<-lmer(dist.range ~ 1 + body_size + Species + season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[7]]<-lmer(dist.range ~ 1 + body_size + Species * season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[8]]<-lmer(dist.range ~ 1 + body_size * Species + season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[9]]<-lmer(dist.range ~ 1 + body_size * Species * season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[10]]<-lmer(dist.range ~ 1 + body_size * Species + Species * season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
Name models
```
Modnames <- c("Model 1", "Model 2", "Model 3", "Model 4", "Model 5",
              "Model 6", "Model 7", "Model 8","Model 9", "Model 10")
```
Create a model selection table based on AICc, better than AIC/BIC with small samples
```
aictab(cand.set = Cand.mod, modnames = Modnames)
```
```
Model selection based on AICc:

          K    AICc Delta_AICc AICcWt Cum.Wt       LL
Model 10 16 2358.40       0.00   0.44   0.44 -1160.81
Model 8  14 2359.86       1.46   0.21   0.65 -1164.12
Model 1   9 2360.52       2.12   0.15   0.80 -1170.52
Model 2  10 2362.28       3.88   0.06   0.87 -1170.22
Model 5  13 2363.57       5.17   0.03   0.90 -1167.23
Model 7  14 2363.57       5.17   0.03   0.93 -1165.97
Model 3  11 2364.56       6.16   0.02   0.95 -1170.17
Model 9  19 2364.85       6.46   0.02   0.97 -1160.00
Model 6  12 2365.10       6.70   0.02   0.99 -1169.23
Model 4  11 2365.16       6.76   0.01   1.00 -1170.47
```
Compute the evidence ratio
```
evidence(aictab(cand.set = Cand.mod, modnames = Modnames))
```
```
Evidence ratio between models 'Model 10' and 'Model 8': 2.08
```
Compute confidence set based on 'raw' method
```
confset(cand.set = Cand.mod, modnames = Modnames, second.ord = TRUE, method = "raw")
```
```
Confidence set for the best model

Method:	 raw sum of model probabilities

95% confidence set:
          K    AICc Delta_AICc AICcWt
Model 10 16 2358.40       0.00   0.44
Model 8  14 2359.86       1.46   0.21
Model 1   9 2360.52       2.12   0.15
Model 2  10 2362.28       3.88   0.06
Model 5  13 2363.57       5.17   0.03
Model 7  14 2363.57       5.17   0.03
Model 3  11 2364.56       6.16   0.02

Model probabilities sum to 0.95
```

### 3. Compare best-fit models of the confidence set

Compare the three best-fit models (within 2.5 Delta_AICc)

```
m10<-lmer(dist.range ~ 1 + body_size * Species + Species * season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m8<-lmer(dist.range ~ 1 + body_size * Species + season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m1<-lmer(dist.range ~ 1 + season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m2<-lmer(dist.range ~ 1 + body_size + season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m5<-lmer(dist.range ~ 1 + Species * season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m7<-lmer(dist.range ~ 1 + body_size + Species * season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m3<-lmer(dist.range ~ 1 + body_size * season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
lrtest(m10,m8)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size * Species + Species * season + (1 +
    season | Species:fi_fishid) + (1 + season | Species)
Model 2: dist.range ~ 1 + body_size * Species + season + (1 + season |
    Species:fi_fishid) + (1 + season | Species)
  #Df  LogLik Df Chisq Pr(>Chisq)
1  16 -1160.8
2  14 -1164.1 -2 6.613    0.03664 *
```
```
lrtest(m10,m1)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size * Species + Species * season + (1 +
    season | Species:fi_fishid) + (1 + season | Species)
Model 2: dist.range ~ 1 + season + (1 + season | Species:fi_fishid) +
    (1 + season | Species)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  16 -1160.8
2   9 -1170.5 -7 19.406   0.007006 **
```
```
lrtest(m10,m2)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size * Species + Species * season + (1 +
    season | Species:fi_fishid) + (1 + season | Species)
Model 2: dist.range ~ 1 + body_size + season + (1 + season | Species:fi_fishid) +
    (1 + season | Species)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  16 -1160.8
2  10 -1170.2 -6 18.823   0.004473 **
```
```
lrtest(m10,m5)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size * Species + Species * season + (1 +
    season | Species:fi_fishid) + (1 + season | Species)
Model 2: dist.range ~ 1 + Species * season + (1 + season | Species:fi_fishid) +
    (1 + season | Species)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  16 -1160.8
2  13 -1167.2 -3 12.832   0.005015 **
```
- The largest model **Model 10** significantly improves the model fit over any of the following models in the candidate set

Export table from confidence set models

:books:`library(sjPlot)`  
:books:`library(sjstats)`  
:books:`library(sjmisc)`  

```
tab_model(m10,m8,m1,m2,m5,m7,m3, transform = NULL, collapse.ci = F,  auto.label = FALSE,  show.se = TRUE,collapse.se = T,
              dv.labels = c("Model 1", "Model 2","Model 3","Model 4", "Model 5","Model 6","Model 7"),
               string.pred = "Variable",
               string.p = "P" ,file = "Season_dist.range.doc", use.viewer = TRUE)
```
|                              	| Model 1                              	|                     	|        	| Model 2                              	|                    	|        	| Model 3                              	|                    	|        	| Model 4                              	|                    	|       	| Model 5                              	|                     	|        	| Model 6                              	|                     	|        	| Model 7                              	|                    	|       	|
|------------------------------	|--------------------------------------	|---------------------	|--------	|--------------------------------------	|--------------------	|--------	|--------------------------------------	|--------------------	|--------	|--------------------------------------	|--------------------	|-------	|--------------------------------------	|---------------------	|--------	|--------------------------------------	|---------------------	|--------	|--------------------------------------	|--------------------	|-------	|
| Variable                     	| Estimates                            	| CI                  	| P      	| Estimates                            	| CI                 	| P      	| Estimates                            	| CI                 	| P      	| Estimates                            	| CI                 	| P     	| Estimates                            	| CI                  	| P      	| Estimates                            	| CI                  	| P      	| Estimates                            	| CI                 	| P     	|
| (Intercept)                  	| -2618.08 (1081.30)                   	| -4737.39 – -498.77  	| 0.015  	| -1065.26 (1221.00)                   	| -3458.38 – 1327.86 	| 0.383  	| 3669.26 (611.80)                     	| 2470.16 – 4868.36  	| <0.001 	| 2662.72 (832.87)                     	| 1030.33 – 4295.12  	| 0.001 	| 2368.06 (537.25)                     	| 1315.07 – 3421.04   	| <0.001 	| 495.32 (813.43)                      	| -1098.98 – 2089.62  	| 0.543  	| 2371.10 (1145.65)                    	| 125.67 – 4616.54   	| 0.038 	|
| body_size                    	| 6.85 (1.37)                          	| 4.17 – 9.54         	| <0.001 	| 6.93 (1.37)                          	| 4.24 – 9.61        	| <0.001 	|                                      	|                    	|        	| 1.23 (0.62)                          	| 0.01 – 2.44        	| 0.048 	|                                      	|                     	|        	| 2.59 (0.89)                          	| 0.83 – 4.34         	| 0.004  	| 1.60 (1.14)                          	| -0.63 – 3.83       	| 0.160 	|
| Speciespikeperch             	| 10566.83 (2716.88)                   	| 5241.83 – 15891.82  	| <0.001 	| 8209.13 (2685.90)                    	| 2944.86 – 13473.40 	| 0.002  	|                                      	|                    	|        	|                                      	|                    	|       	| 2379.96 (816.35)                     	| 779.94 – 3979.99    	| 0.004  	| 3010.25 (794.64)                     	| 1452.79 – 4567.70   	| <0.001 	|                                      	|                    	|       	|
| Specieswels                  	| 5350.46 (1536.30)                    	| 2339.36 – 8361.56   	| <0.001 	| 3207.00 (1461.22)                    	| 343.06 – 6070.95   	| 0.028  	|                                      	|                    	|        	|                                      	|                    	|       	| 1608.18 (691.04)                     	| 253.77 – 2962.59    	| 0.020  	| 396.04 (778.08)                      	| -1128.97 – 1921.06  	| 0.611  	|                                      	|                    	|       	|
| season1                      	| 1284.45 (663.78)                     	| -16.53 – 2585.44    	| 0.053  	| -1190.41 (1119.72)                   	| -3385.02 – 1004.20 	| 0.288  	| -1224.21 (1049.81)                   	| -3281.81 – 833.39  	| 0.244  	| -1254.99 (1060.75)                   	| -3334.03 – 824.04  	| 0.237 	| 1107.15 (636.21)                     	| -139.79 – 2354.09   	| 0.082  	| 1326.63 (635.08)                     	| 81.91 – 2571.36     	| 0.037  	| -2532.37 (1680.22)                   	| -5825.54 – 760.79  	| 0.132 	|
| season2                      	| 1122.76 (678.15)                     	| -206.39 – 2451.91   	| 0.098  	| -809.44 (839.12)                     	| -2454.09 – 835.21  	| 0.335  	| -856.90 (816.24)                     	| -2456.71 – 742.90  	| 0.294  	| -829.12 (816.88)                     	| -2430.17 – 771.93  	| 0.310 	| 1019.55 (680.59)                     	| -314.39 – 2353.48   	| 0.134  	| 1049.76 (679.73)                     	| -282.48 – 2382.01   	| 0.122  	| 120.52 (1346.75)                     	| -2519.07 – 2760.11 	| 0.929 	|
| season3                      	| 105.98 (655.62)                      	| -1179.01 – 1390.97  	| 0.872  	| -734.03 (549.53)                     	| -1811.10 – 343.03  	| 0.182  	| -784.42 (542.71)                     	| -1848.13 – 279.28  	| 0.148  	| -835.97 (587.40)                     	| -1987.25 – 315.32  	| 0.155 	| -7.82 (653.77)                       	| -1289.19 – 1273.55  	| 0.990  	| 24.53 (652.82)                       	| -1254.96 – 1304.03  	| 0.970  	| 481.15 (1353.90)                     	| -2172.44 – 3134.73 	| 0.722 	|
| season4                      	| 1661.38 (719.53)                     	| 251.14 – 3071.62    	| 0.021  	| -995.33 (1127.42)                    	| -3205.02 – 1214.37 	| 0.377  	| -1113.80 (1133.27)                   	| -3334.97 – 1107.37 	| 0.326  	| -1114.25 (1139.72)                   	| -3348.05 – 1119.55 	| 0.328 	| 1522.47 (720.90)                     	| 109.53 – 2935.41    	| 0.035  	| 1558.68 (706.76)                     	| 173.46 – 2943.90    	| 0.027  	| -1458.50 (1721.25)                   	| -4832.08 – 1915.09 	| 0.397 	|
| body_size:Speciespikeperch   	| -13.52 (5.26)                        	| -23.83 – -3.21      	| 0.010  	| -14.14 (5.24)                        	| -24.40 – -3.88     	| 0.007  	|                                      	|                    	|        	|                                      	|                    	|       	|                                      	|                     	|        	|                                      	|                     	|        	|                                      	|                    	|       	|
| body_size:Specieswels        	| -5.81 (1.62)                         	| -8.98 – -2.64       	| <0.001 	| -5.86 (1.62)                         	| -9.03 – -2.69      	| <0.001 	|                                      	|                    	|        	|                                      	|                    	|       	|                                      	|                     	|        	|                                      	|                     	|        	|                                      	|                    	|       	|
| Speciespikeperch:season1     	| -4348.37 (1020.26)                   	| -6348.05 – -2348.69 	| <0.001 	|                                      	|                    	|        	|                                      	|                    	|        	|                                      	|                    	|       	| -4145.84 (974.55)                    	| -6055.92 – -2235.76 	| <0.001 	| -4535.58 (969.40)                    	| -6435.56 – -2635.59 	| <0.001 	|                                      	|                    	|       	|
| Specieswels:season1          	| -3205.01 (839.86)                    	| -4851.10 – -1558.92 	| <0.001 	|                                      	|                    	|        	|                                      	|                    	|        	|                                      	|                    	|       	| -2903.03 (802.29)                    	| -4475.50 – -1330.57 	| <0.001 	| -3204.55 (803.21)                    	| -4778.81 – -1630.30 	| <0.001 	|                                      	|                    	|       	|
| Speciespikeperch:season2     	| -3005.69 (1026.75)                   	| -5018.09 – -993.30  	| 0.003  	|                                      	|                    	|        	|                                      	|                    	|        	|                                      	|                    	|       	| -2902.48 (1023.39)                   	| -4908.30 – -896.66  	| 0.005  	| -2932.69 (1023.84)                   	| -4939.39 – -926.00  	| 0.004  	|                                      	|                    	|       	|
| Specieswels:season2          	| -2717.92 (870.09)                    	| -4423.26 – -1012.58 	| 0.002  	|                                      	|                    	|        	|                                      	|                    	|        	|                                      	|                    	|       	| -2614.70 (869.06)                    	| -4318.03 – -911.37  	| 0.003  	| -2644.92 (868.99)                    	| -4348.11 – -941.73  	| 0.002  	|                                      	|                    	|       	|
| Speciespikeperch:season3     	| -2244.58 (1008.35)                   	| -4220.90 – -268.25  	| 0.026  	|                                      	|                    	|        	|                                      	|                    	|        	|                                      	|                    	|       	| -2130.77 (1001.74)                   	| -4094.14 – -167.41  	| 0.033  	| -2163.13 (1001.92)                   	| -4126.85 – -199.40  	| 0.031  	|                                      	|                    	|       	|
| Specieswels:season3          	| -652.25 (850.47)                     	| -2319.14 – 1014.65  	| 0.443  	|                                      	|                    	|        	|                                      	|                    	|        	|                                      	|                    	|       	| -538.44 (845.84)                     	| -2196.26 – 1119.37  	| 0.524  	| -570.80 (845.58)                     	| -2228.10 – 1086.51  	| 0.500  	|                                      	|                    	|       	|
| Speciespikeperch:season4     	| -4430.51 (1130.67)                   	| -6646.59 – -2214.42 	| <0.001 	|                                      	|                    	|        	|                                      	|                    	|        	|                                      	|                    	|       	| -4533.49 (1131.01)                   	| -6750.23 – -2316.75 	| <0.001 	| -4542.45 (1108.95)                   	| -6715.95 – -2368.95 	| <0.001 	|                                      	|                    	|       	|
| Specieswels:season4          	| -3536.99 (921.15)                    	| -5342.40 – -1731.57 	| <0.001 	|                                      	|                    	|        	|                                      	|                    	|        	|                                      	|                    	|       	| -3373.98 (917.11)                    	| -5171.49 – -1576.47 	| <0.001 	| -3376.80 (901.53)                    	| -5143.77 – -1609.83 	| <0.001 	|                                      	|                    	|       	|
| body_size:season1            	|                                      	|                     	|        	|                                      	|                    	|        	|                                      	|                    	|        	|                                      	|                    	|       	|                                      	|                     	|        	|                                      	|                     	|        	| 1.59 (1.43)                          	| -1.21 – 4.39       	| 0.266 	|
| body_size:season2            	|                                      	|                     	|        	|                                      	|                    	|        	|                                      	|                    	|        	|                                      	|                    	|       	|                                      	|                     	|        	|                                      	|                     	|        	| -1.14 (1.29)                         	| -3.67 – 1.40       	| 0.379 	|
| body_size:season3            	|                                      	|                     	|        	|                                      	|                    	|        	|                                      	|                    	|        	|                                      	|                    	|       	|                                      	|                     	|        	|                                      	|                     	|        	| -1.64 (1.39)                         	| -4.37 – 1.08       	| 0.237 	|
| body_size:season4            	|                                      	|                     	|        	|                                      	|                    	|        	|                                      	|                    	|        	|                                      	|                    	|       	|                                      	|                     	|        	|                                      	|                     	|        	| 0.47 (1.57)                          	| -2.61 – 3.56       	| 0.764 	|
| Random Effects               	|                                      	|                     	|        	|                                      	|                    	|        	|                                      	|                    	|        	|                                      	|                    	|       	|                                      	|                     	|        	|                                      	|                     	|        	|                                      	|                    	|       	|
| σ2                           	| 751096.60                            	|                     	|        	| 887523.66                            	|                    	|        	| 772723.06                            	|                    	|        	| 732292.27                            	|                    	|       	| 530944.81                            	|                     	|        	| 673250.82                            	|                     	|        	| 698977.19                            	|                    	|       	|
| τ00                          	| 1168809.95 Species:fi_fishid         	|                     	|        	| 1158169.13 Species:fi_fishid         	|                    	|        	| 1969063.78 Species:fi_fishid         	|                    	|        	| 1868815.44 Species:fi_fishid         	|                    	|       	| 2113654.68 Species:fi_fishid         	|                     	|        	| 1658624.71 Species:fi_fishid         	|                     	|        	| 1890664.59 Species:fi_fishid         	|                    	|       	|
|                              	| 0.00 Species                         	|                     	|        	| 1197450.69 Species                   	|                    	|        	| 843540.80 Species                    	|                    	|        	| 956583.88 Species                    	|                    	|       	| 0.01 Species                         	|                     	|        	| 0.00 Species                         	|                     	|        	| 1049669.82 Species                   	|                    	|       	|
| τ11                          	| 1009325.24 Species:fi_fishid.season1 	|                     	|        	| 1512753.41 Species:fi_fishid.season1 	|                    	|        	| 921889.23 Species:fi_fishid.season1  	|                    	|        	| 983016.79 Species:fi_fishid.season1  	|                    	|       	| 1006201.09 Species:fi_fishid.season1 	|                     	|        	| 945769.40 Species:fi_fishid.season1  	|                     	|        	| 1431957.69 Species:fi_fishid.season1 	|                    	|       	|
|                              	| 2658096.69 Species:fi_fishid.season2 	|                     	|        	| 2554548.96 Species:fi_fishid.season2 	|                    	|        	| 2705477.97 Species:fi_fishid.season2 	|                    	|        	| 2768932.44 Species:fi_fishid.season2 	|                    	|       	| 3027033.82 Species:fi_fishid.season2 	|                     	|        	| 2757051.69 Species:fi_fishid.season2 	|                     	|        	| 2749001.01 Species:fi_fishid.season2 	|                    	|       	|
|                              	| 2606293.93 Species:fi_fishid.season3 	|                     	|        	| 2605498.26 Species:fi_fishid.season3 	|                    	|        	| 2719370.72 Species:fi_fishid.season3 	|                    	|        	| 2711467.87 Species:fi_fishid.season3 	|                    	|       	| 2970533.38 Species:fi_fishid.season3 	|                     	|        	| 2697202.90 Species:fi_fishid.season3 	|                     	|        	| 2611043.40 Species:fi_fishid.season3 	|                    	|       	|
|                              	| 2959183.14 Species:fi_fishid.season4 	|                     	|        	| 2976953.47 Species:fi_fishid.season4 	|                    	|        	| 3059416.03 Species:fi_fishid.season4 	|                    	|        	| 3056979.15 Species:fi_fishid.season4 	|                    	|       	| 3243213.69 Species:fi_fishid.season4 	|                     	|        	| 2851440.42 Species:fi_fishid.season4 	|                     	|        	| 3074057.13 Species:fi_fishid.season4 	|                    	|       	|
|                              	| 0.02 Species.season1                 	|                     	|        	| 3249361.07 Species.season1           	|                    	|        	| 2866857.51 Species.season1           	|                    	|        	| 2953241.52 Species.season1           	|                    	|       	| 0.04 Species.season1                 	|                     	|        	| 0.00 Species.season1                 	|                     	|        	| 3835417.67 Species.season1           	|                    	|       	|
|                              	| 0.01 Species.season2                 	|                     	|        	| 1666382.68 Species.season2           	|                    	|        	| 1557979.77 Species.season2           	|                    	|        	| 1563560.59 Species.season2           	|                    	|       	| 0.01 Species.season2                 	|                     	|        	| 0.01 Species.season2                 	|                     	|        	| 1489385.20 Species.season2           	|                    	|       	|
|                              	| 0.02 Species.season3                 	|                     	|        	| 469311.03 Species.season3            	|                    	|        	| 457334.30 Species.season3            	|                    	|        	| 614922.75 Species.season3            	|                    	|       	| 0.10 Species.season3                 	|                     	|        	| 0.01 Species.season3                 	|                     	|        	| 1171467.08 Species.season3           	|                    	|       	|
|                              	| 0.00 Species.season4                 	|                     	|        	| 3271633.54 Species.season4           	|                    	|        	| 3314607.19 Species.season4           	|                    	|        	| 3371333.49 Species.season4           	|                    	|       	| 0.12 Species.season4                 	|                     	|        	| 0.01 Species.season4                 	|                     	|        	| 3293537.48 Species.season4           	|                    	|       	|
| ρ01                          	| -0.65                                	|                     	|        	| -0.76                                	|                    	|        	| -0.33                                	|                    	|        	| -0.47                                	|                    	|       	| -0.29                                	|                     	|        	| -0.59                                	|                     	|        	| -0.64                                	|                    	|       	|
|                              	| -0.57                                	|                     	|        	| -0.58                                	|                    	|        	| -0.48                                	|                    	|        	| -0.45                                	|                    	|       	| -0.49                                	|                     	|        	| -0.43                                	|                     	|        	| -0.47                                	|                    	|       	|
|                              	| -0.19                                	|                     	|        	| -0.15                                	|                    	|        	| -0.36                                	|                    	|        	| -0.34                                	|                    	|       	| -0.39                                	|                     	|        	| -0.24                                	|                     	|        	| -0.34                                	|                    	|       	|
|                              	| -0.69                                	|                     	|        	| -0.72                                	|                    	|        	| -0.62                                	|                    	|        	| -0.64                                	|                    	|       	| -0.62                                	|                     	|        	| -0.64                                	|                     	|        	| -0.66                                	|                    	|       	|
|                              	| 0.15                                 	|                     	|        	| -1.00                                	|                    	|        	| -1.00                                	|                    	|        	| -0.95                                	|                    	|       	| -0.64                                	|                     	|        	| 0.04                                 	|                     	|        	| -0.76                                	|                    	|       	|
|                              	| 0.42                                 	|                     	|        	| -1.00                                	|                    	|        	| -0.99                                	|                    	|        	| -0.88                                	|                    	|       	| -0.41                                	|                     	|        	| 0.77                                 	|                     	|        	| -0.92                                	|                    	|       	|
|                              	| -0.71                                	|                     	|        	| -0.95                                	|                    	|        	| -0.96                                	|                    	|        	| -0.99                                	|                    	|       	| -0.46                                	|                     	|        	| 0.32                                 	|                     	|        	| -0.91                                	|                    	|       	|
|                              	| -0.28                                	|                     	|        	| -1.00                                	|                    	|        	| -1.00                                	|                    	|        	| -0.94                                	|                    	|       	| -0.80                                	|                     	|        	| -0.60                                	|                     	|        	| -0.84                                	|                    	|       	|
| ICC                          	| 0.71                                 	|                     	|        	|                                      	|                    	|        	|                                      	|                    	|        	|                                      	|                    	|       	|                                      	|                     	|        	|                                      	|                     	|        	|                                      	|                    	|       	|
| N                            	| 3 Species                            	|                     	|        	| 3 Species                            	|                    	|        	| 3 Species                            	|                    	|        	| 3 Species                            	|                    	|       	| 3 Species                            	|                     	|        	| 3 Species                            	|                     	|        	| 3 Species                            	|                    	|       	|
|                              	| 31 fi_fishid                         	|                     	|        	| 31 fi_fishid                         	|                    	|        	| 31 fi_fishid                         	|                    	|        	| 31 fi_fishid                         	|                    	|       	| 31 fi_fishid                         	|                     	|        	| 31 fi_fishid                         	|                     	|        	| 31 fi_fishid                         	|                    	|       	|
| Observations                 	| 131                                  	|                     	|        	| 131                                  	|                    	|        	| 131                                  	|                    	|        	| 131                                  	|                    	|       	| 131                                  	|                     	|        	| 131                                  	|                     	|        	| 131                                  	|                    	|       	|
| Marginal R2 / Conditional R2 	| 0.345 / 0.808                        	|                     	|        	| 0.490 / NA                           	|                    	|        	| 0.192 / NA                           	|                    	|        	| 0.338 / NA                           	|                    	|       	| 0.559 / NA                           	|                     	|        	| 0.593 / NA                           	|                     	|        	| 0.422 / NA                           	|                    	|       	|

Plot marginal effects of **body_size** for each species

```
plot_model(m10, type = "pred", terms = c("body_size", "Species"))
```
![Dist_range_month](/Plots/Dist_range_season_0.png "Dist_range_month")

```
plot_model(m10,  mdrt.values = "meansd", type = "pred", terms = c("Species", "body_size"))
```
![Dist_range_month](/Plots/Dist_range_season_01.png "Dist_range_month")

### 4. Analysis of the effects of body size on seasonal distance range travelled by each species

Looking at the summary results for the best-fit model we see the _Species_ x _body_size_ interaction is significant
```
summ(m10,  center = TRUE, scale = TRUE, n.sd = 2)
```
```
MODEL INFO:
Observations: 131
Dependent Variable: dist.range
Type: Mixed effects linear regression

MODEL FIT:
AIC = 2353.62, BIC = 2399.63
Pseudo-R² (fixed effects) = 0.22
Pseudo-R² (total) = 0.28

FIXED EFFECTS:
------------------------------------------------------------------------------
                                       Est.      S.E.   t val.     d.f.      p
-------------------------------- ---------- --------- -------- -------- ------
(Intercept)                         4111.10    411.91     9.98    24.59   0.00
body_size                           4649.75   1155.03     4.03    23.01   0.00
Speciespikeperch                   -3854.07   2389.12    -1.61    24.51   0.12
Specieswels                        -1289.95    565.27    -2.28    24.64   0.03
season                               587.91    525.39     1.12   100.60   0.27
body_size:Speciespikeperch         -9323.79   4305.31    -2.17    25.26   0.04
body_size:Specieswels              -4510.98   1359.51    -3.32    23.41   0.00
Speciespikeperch:season            -2362.67    814.00    -2.90    97.21   0.00
Specieswels:season                 -1418.95    670.88    -2.12    98.20   0.04
------------------------------------------------------------------------------

p values calculated using Satterthwaite d.f.

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)    501.91
 Species:fi_fishid     season        84.83
      Species        (Intercept)     0.00
      Species          season        0.22
     Residual                       1640.48
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      0.09
      Species           3       0.00
-------------------------------------

Continuous predictors are mean-centered and scaled by 2 s.d.
```

#### Calculate Level-2 and Level-3 ICC indices

:books:`library(easystats)`

```
iccm.1st <- icc(m8)
print(iccm.1st)
print(iccm.1st, comp = "var")
```
```
# Intraclass Correlation Coefficient

     Adjusted ICC: 0.125
  Conditional ICC: 0.105

# Intraclass Correlation Coefficient

     Adjusted ICC: 0.125
  Conditional ICC: 0.105
```
Between levels 1 and 2
```
sum(get_re_var(m2)) / (sum(get_re_var(m2)) + get_re_var(m2, "sigma_2"))
```
Between levels 2 and 3
```
get_re_var(m2)[2] / sum(get_re_var(m2))
```

#### Pairwise comparisons

:books:`library(emmeans)`
```
emtrends(m10, pairwise ~ Species, var = "body_size",pbkrtest.limit = 10000, lmerTest.limit = 10000)
```
```
$emtrends
 Species   body_size.trend   SE   df lower.CL upper.CL
 pike                6.426 1.91 36.0     2.55    10.30
 pikeperch          -6.459 6.80 38.2   -20.22     7.31
 wels                0.192 1.18 37.4    -2.20     2.58

Degrees-of-freedom method: kenward-roger
Confidence level used: 0.95

$contrasts
 contrast         estimate   SE   df t.ratio p.value
 pike - pikeperch    12.89 7.06 38.0  1.824  0.1754
 pike - wels          6.23 2.25 36.4  2.776  0.0229
 pikeperch - wels    -6.65 6.90 38.1 -0.964  0.6039

Degrees-of-freedom method: kenward-roger
P value adjustment: tukey method for comparing a family of 3 estimates
```
```
emmeans(m10, pairwise ~ Species, pbkrtest.limit = 10000, lmerTest.limit = 10000)
```
```
$emmeans
 Species   emmean   SE  df lower.CL upper.CL
 pike        4111 5625 179    -6989    15211
 pikeperch    257 6263  40   -12401    12915
 wels        2821 5623 108    -8324    13966

Degrees-of-freedom method: kenward-roger
Confidence level used: 0.95

$contrasts
 contrast         estimate   SE    df t.ratio p.value
 pike - pikeperch     3854 8418  41.3  0.458  0.8912
 pike - wels          1290 7953 139.1  0.162  0.9856
 pikeperch - wels    -2564 8417  40.8 -0.305  0.9502

Degrees-of-freedom method: kenward-roger
P value adjustment: tukey method for comparing a family of 3 estimates
```
- Trends are significant between _pike_ and _wels_
- However, contrasts render non-significant as long as they estimate the least-square means but not trends/slopes (besides not accounting for random-effects and the involvement of the interaction)
- This is the meaning of further performing simple slope analysis

#### Plot main-effects

:books:`library(effects)`
```
plot(Effect(c("Species", "body_size"), m10),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Dist_range_season](/Plots/Dist_range_season_1.png "Dist_range_season")

```
plot(Effect(c("Species", "season"), m10),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Dist_range_season](/Plots/Dist_range_season_2.png "Dist_range_season")

**Note:** similar linear predictions can be obtained with the _emmip()_ function in _emmeans_ library as follows:
```
emmip(m10, Species ~ body_size, cov.reduce = range, pbkrtest.limit = 10000, lmerTest.limit = 10000)
```

#### Simple slope analysis

In this analysis we estimate the slopes of body size trends for each Species, i.e., the moderator effects on distance range indicating the values at which the slopes are significant
```
probe_interaction(m10, pred = body_size, modx = Species, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on travel distance")
```
```
SIMPLE SLOPES ANALYSIS

When Species = wels:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of body_size               0.19     0.99     0.19   0.85
Conditional intercept         2821.19   387.12     7.29   0.00

When Species = pikeperch:

                                Est.      S.E.   t val.      p
--------------------------- -------- --------- -------- ------
Slope of body_size             -6.46      5.73    -1.13   0.27
Conditional intercept         257.34   2353.42     0.11   0.91

When Species = pike:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of body_size               6.43     1.60     4.03   0.00
Conditional intercept         4111.07   411.92     9.98   0.00
```
- The slope of _body_size_ is positive and significant (p < 0.05) in _pike_ but not _wels_ and _pikeperch_ indicating that body size affected the mean travel distance only in the first species

#### Simple slope analysis of the interactions in the model

In this analysis we estimate the two interaction terms in the model and thus:

- The slopes of body size trends for each Species, i.e., the moderator effects on distance range indicating the values at which the slopes are significant
- The slopes of _season_ (i.e., the rate of change in _dist.range_ throughout seasonal time) for each species

We might want to consider re-fitting **Model 10** with a gamma distribution. Is it feasible?

- The distribution of _dist.range_ is right-skew and always positive (see histogram above)
- DV must have values > 0 but _dis.range_ contain 0s, thus we need to assign thoses cases values > 0 (e.g., 0.0001) before re-fitting the model
- Thus, we re-fit the model with gamma and log-link to be consistent with original model fit

```
data_distr_season_sub<-data_distr_season %>% mutate(dist.range = replace(dist.range, dist.range == 0, 0.0001))
data_distr_season_sub$season <- as.factor(data_distr_season_sub$season)
```
```
m10_gamma<-glmer(dist.range ~ 1 + body_size * Species + Species * season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season_sub, REML=T, control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```

**Slope of the Species x body_size interaction**

```
probe_interaction(m10_gamma, pred = body_size, modx = Species, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on travel distance")
```
```
SIMPLE SLOPES ANALYSIS

When Species = wels:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     0.19   0.85
Conditional intercept         7.79   0.22    35.12   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.00   0.00    -0.87   0.39
Conditional intercept          6.48   1.55     4.18   0.00

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     2.76   0.01
Conditional intercept         8.26   0.23    35.48   0.00
```
- The slopes of _body_size_ is significant (p < 0.05) and positive only for _pike_

```
interact_plot(m10_gamma, pred = body_size, modx = Species, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on travel distance")
```
![Dist_range_season](/Plots/Dist_range_season_3.png "Dist_range_season")

**Slope of the Species x season interaction**

```
probe_interaction(m10_gamma, pred = season, modx = Species, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Season", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Seasonal changes in travel distance")
```
```
SIMPLE SLOPES ANALYSIS

When Species = wels:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of season               -0.14   0.06    -2.15   0.03
Conditional intercept          7.79   0.22    35.12   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of season               -0.24   0.09    -2.64   0.01
Conditional intercept          6.48   1.55     4.18   0.00

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of season               0.08   0.08     1.03   0.30
Conditional intercept         8.26   0.23    35.48   0.00
```

- The slope of _season_ (i.e, the seasonal rate of change) is significant and negative in both _wels_ and _pikeperch_ suggesting that there is a tendency of a decreased distance range from Spring I to Winter
- For _pike_ the slope of season was not significant

**Plot the Species x month interaction**

```
interact_plot(m10_gamma, pred = season, modx = Species, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Season", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Seasonal rate of change in travel distance")
```
![Dist_range_season](/Plots/Dist_range_season_4.png "Dist_range_season")

### Does body size determine seasonal changes in distance range between species?

- For this analysis we may take **Model 9** including a three-way interaction between body size, month and species
- However, to test separate effects by season we need to convert the variable _season_ to factor and re-fit the model

```
data_distr_season_sub$season <- as.factor(data_distr_season_sub$season)
```
```
m9_gamma<-glmer(dist.range ~ 1 + body_size * Species * season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season_sub, REML=F, control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```
```
probe_interaction(m9_gamma, pred = body_size, modx = Species, mod2= season, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Season", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on travel distance")
```
```
¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While season (2nd moderator) = 1 ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     3.02   0.00
Conditional intercept         8.05   0.23    34.55   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.00   0.00    -0.57   0.57
Conditional intercept          7.73   1.26     6.12   0.00

When Species = wels:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     0.45   0.65
Conditional intercept         8.08   0.24    33.90   0.00

¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While season (2nd moderator) = 2 ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     1.96   0.05
Conditional intercept         8.35   0.32    26.25   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.01   0.01    -1.94   0.05
Conditional intercept          3.16   1.92     1.65   0.10

When Species = wels:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     1.93   0.05
Conditional intercept         6.73   0.30    22.25   0.00

¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While season (2nd moderator) = 3 ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     2.22   0.03
Conditional intercept         8.28   0.40    20.79   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.00   0.00    -0.01   0.99
Conditional intercept          7.59   1.99     3.80   0.00

When Species = wels:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.00   0.00    -2.05   0.04
Conditional intercept          7.89   0.30    26.60   0.00

¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While season (2nd moderator) = 4 ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     2.05   0.04
Conditional intercept         7.90   0.26    30.28   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.00   0.00    -0.30   0.76
Conditional intercept          7.17   1.66     4.33   0.00

When Species = wels:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.00   0.00    -0.58   0.56
Conditional intercept          8.05   0.25    31.62   0.00

¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While season (2nd moderator) = 5 ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     1.32   0.19
Conditional intercept         8.30   0.28    29.77   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.00   0.00    -0.95   0.34
Conditional intercept          5.48   1.80     3.04   0.00

When Species = wels:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     1.16   0.25
Conditional intercept         7.06   0.24    29.52   0.00
```
- For _pikeperch_, the slope of season is only signficant and negative in _Spring II_ suggesting that distance range decreases with smaller body size only during that season
- For _pike_, the slope of body_size is significant and positive in all seasons but _Winter_ when body size is about 528.21 (Mean-SD) suggesting a positive rate of change in distance range associated with larger sizes
- For _wels_, the slope of season is significant and positive in _Spring II_ and negative in _Autumn_ suggesting that body size influences distance range differently across seasons

```
interact_plot(m9_gamma, pred = body_size, modx = Species, mod2= season, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,x.label = "Season", y.label = "Mean distance range (m)",legend.main="Species", mod2.labels=c("Spring I","Spring II","Autumn","Summer","Winter"), modx.labels=c("pike","pikeperch","wels"),main.title = "Seasonal-effects of body size on travel distance")
```
![Dist_range_season](/Plots/Dist_range_season_5.png "Dist_range_season")

As above but using _Season_ as the main moderatos
```
interact_plot(m9_gamma, pred = body_size, modx = season, mod2= Species, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE, pred.labels = NULL, x.label = "Body size (cm)", y.label = "Mean distance range (m)", modx.labels=c("Spring I","Spring II","Autumn","Summer","Winter"), main.title = "Seasonal-effects of body size on travel distance")
```
![Dist_range_season](/Plots/Dist_range_season_51.png "Dist_range_season")


### How does stationarity affect mean distance range?

- This is probably not a frequentist approach to such an answer but let consider the proportion of dam "excursions" as a site-fidelity measure
- For that, let re-fit the previous best-fit model (with gamma distribution) including the variable _dam_ along with the _Species_ x _body_size_ interaction (three-way interaction)
```
m10_gamma_dam<-glmer(dist.range ~ 1 + body_size * Species * dam + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season_sub, REML=F, control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```
```
summ(m10_gamma_dam,  center = TRUE, scale = TRUE, n.sd = 2)
```
```
MODEL INFO:
Observations: 131
Dependent Variable: dist.range
Type: Mixed effects generalized linear regression
Error Distribution: Gamma
Link function: log

MODEL FIT:
AIC = 145.54, BIC = 269.18

FIXED EFFECTS:
-------------------------------------------------------------------
                                        Est.   S.E.   t val.      p
------------------------------------ ------- ------ -------- ------
(Intercept)                             8.05   0.29    27.99   0.00
body_size                               1.96   0.79     2.47   0.01
Speciespikeperch                       -1.04   1.67    -0.63   0.53
Specieswels                             0.08   0.36     0.22   0.82
dam                                    -0.14   0.68    -0.20   0.84
body_size:Speciespikeperch             -5.41   3.16    -1.71   0.09
body_size:Specieswels                  -1.95   0.88    -2.20   0.03
body_size:dam                          -1.39   2.00    -0.69   0.49
Speciespikeperch:dam                   -5.76   3.48    -1.65   0.10
Specieswels:dam                        -0.17   0.75    -0.22   0.83
body_size:Speciespikeperch:dam         -9.83   7.04    -1.40   0.16
body_size:Specieswels:dam               1.53   2.09     0.74   0.46
-------------------------------------------------------------------

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)     0.58
 Species:fi_fishid     season2       0.85
 Species:fi_fishid     season3       1.00
 Species:fi_fishid     season4       0.78
 Species:fi_fishid     season5       0.83
      Species        (Intercept)     0.00
      Species          season2       0.49
      Species          season3       0.35
      Species          season4       0.16
      Species          season5       0.39
     Residual                        0.00
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      1.00
      Species           3       0.00
-------------------------------------

Continuous predictors are mean-centered and scaled by 2 s.d.
```
Perform a simple slope analysis of the effects of dam use on mean distance range
```
probe_interaction(m10_gamma_dam, pred = dam, modx = Species, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of dam use on travel distance as subject to body size")
```
```
SIMPLE SLOPES ANALYSIS

When Species = wels:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.00   0.01    -0.15   0.88
Conditional intercept          8.09   0.22    37.51   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.14   0.05    -2.67   0.01
Conditional intercept          5.08   1.35     3.77   0.00

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of dam                  0.00   0.01     0.13   0.90
Conditional intercept         8.03   0.25    32.51   0.00
```
- The slope for _pikeperch_ is the only signficant and negative, indicating that a higher dam use tends to relate to lower distance range
```
interact_plot(m10_gamma_dam, pred = dam, modx = Species,plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Proportion of dam use", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("wels","pikeperch","pike"),main.title = "Effects of dam use on travel distance")
```
![Dist_range_season](/Plots/Dist_range_season_6.png "Dist_range_season")

Now let account for the moderator effects of body_size on the slopes of dam
```
probe_interaction(m10_gamma_dam, pred = body_size, modx = Species, mod2 = dam, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on travel distance as subject to dam use")
```
```
¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While dam (2nd moderator) = -8.12 (- 1 SD) ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     2.67   0.01
Conditional intercept         7.94   0.29    27.73   0.00

When Species = pikeperch:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     0.42   0.67
Conditional intercept         9.15   1.48     6.20   0.00

When Species = wels:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     1.44   0.15
Conditional intercept         7.91   0.25    31.22   0.00

¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While dam (2nd moderator) = 21.31 (Mean) ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     3.08   0.00
Conditional intercept         8.03   0.25    32.51   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.01   0.00    -2.50   0.01
Conditional intercept          5.08   1.35     3.77   0.00

When Species = wels:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     0.56   0.58
Conditional intercept         8.09   0.22    37.51   0.00

¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While dam (2nd moderator) = 50.75 (+ 1 SD) ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     1.04   0.30
Conditional intercept         8.05   0.54    14.80   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.02   0.01    -3.22   0.00
Conditional intercept         -0.48   2.40    -0.20   0.84

When Species = wels:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     0.10   0.92
Conditional intercept         8.17   0.27    29.98   0.00
```
- At mean values of dam use the slope of _body_size_ is significant and positive in _pike_ and negative in _pikeperch
- At low values of dam use the slope of _body_size_ is significant and positive only in _pike_
- At higher values of dam use the slope of _body_size_ is significant and negative only in _pikeperch_

```
interact_plot(m10_gamma_dam, pred = body_size, modx = Species, mod2 = dam, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("wels","pikeperch","pike"),main.title = "Effects of body size on travel distance as subject to dam use")
```
![Dist_range_season](/Plots/Dist_range_season_7.png "Dist_range_season")

As above but using _body_size_ as the secondary moderator

```
interact_plot(m10_gamma_dam, pred = dam, modx = Species, mod2 = body_size, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Rate of dam use", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("wels","pikeperch","pike"),main.title = "Effects of dam use on travel distance as subject to body size")
```
![Dist_range_season](/Plots/Dist_range_season_8.png "Dist_range_season")

### Are there seasonal changes in dam use?

Fit a model of the interaction _dam_ x _season_

```
m_dam_season<-lmer(dist.range ~ 1 + season * dam + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season_sub, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
summ(m_dam_season,  center = TRUE, scale = TRUE, n.sd = 2)
```
```
MODEL INFO:
Observations: 131
Dependent Variable: dist.range
Type: Mixed effects linear regression

MODEL FIT:
AIC = 2357.00, BIC = 2388.63
Pseudo-R² (fixed effects) = 0.07
Pseudo-R² (total) = 0.32

FIXED EFFECTS:
-------------------------------------------------------------
                        Est.     S.E.   t val.    d.f.      p
----------------- ---------- -------- -------- ------- ------
(Intercept)          3059.56   223.96    13.66   33.07   0.00
season               -898.85   500.74    -1.80    2.74   0.18
dam                   575.86   431.85     1.33   95.51   0.19
season:dam          -1900.61   760.41    -2.50   94.98   0.01
-------------------------------------------------------------

p values calculated using Satterthwaite d.f.

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)    923.02
 Species:fi_fishid     season       126.17
      Species        (Intercept)     2.63
      Species          season       667.02
     Residual                       1600.75
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      0.25
      Species           3       0.00
-------------------------------------

Continuous predictors are mean-centered and scaled by 2 s.d.
```
- The interaction is significant but how do the slopes change?
```
probe_interaction(m_dam_season, pred = dam, modx = season, plot.points = TRUE, cond.int = TRUE, interval = FALSE,jnplot = FALSE ,x.label = "Proportion of dam use", y.label = "Mean distance range (m)",legend.main="Season", modx.labels=c("Spring I","Spring II","Autumn","Summer","Winter"),main.title = "Seasonal effects of dam use on travel distance")
```
```
SIMPLE SLOPES ANALYSIS

When season = 4:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of dam                   -18.23     8.57    -2.13   0.04
Conditional intercept         2752.68   529.71     5.20   0.01

When season = 3:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of dam                     4.83     9.75     0.50   0.62
Conditional intercept         2850.15   404.56     7.05   0.00

When season = 2:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of dam                    16.86     7.51     2.25   0.04
Conditional intercept         2742.55   409.62     6.70   0.00

When season = 1:

                                 Est.      S.E.   t val.      p
--------------------------- --------- --------- -------- ------
Slope of dam                   125.55     42.59     2.95   0.01
Conditional intercept         4049.79   1087.57     3.72   0.02

When season = 0:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of dam                    37.38    16.08     2.32   0.03
Conditional intercept         4105.78   610.18     6.73   0.00
```
![Dist_range_season](/Plots/Dist_range_season_9.png "Dist_range_season")

### Are there conditional effects of dam use in distance range across seasons and between species ?

Now we test wether there are seasonal differences between species excluding the moderator effects of body size
```
m_dam_season_sp<-glmer(dist.range ~ 1 + season * Species * dam + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season_sub, REML=F, control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```
```
summ(m_dam_season_sp,  center = TRUE, scale = TRUE, n.sd = 2)
```
```
MODEL INFO:
Observations: 131
Dependent Variable: dist.range
Type: Mixed effects generalized linear regression
Error Distribution: Gamma
Link function: log

MODEL FIT:
AIC = 202.71, BIC = 378.09
Pseudo-R² (fixed effects) =  NA
Pseudo-R² (total) =  NA

FIXED EFFECTS:
-----------------------------------------------------------------
                                      Est.   S.E.   t val.      p
---------------------------------- ------- ------ -------- ------
(Intercept)                           7.62   0.23    33.70   0.00
season1                              -0.63   1.65    -0.38   0.70
season2                               0.22   0.43     0.52   0.61
season3                              -0.73   0.74    -0.99   0.32
season4                               0.51   0.68     0.75   0.45
Speciespikeperch                      1.09   0.35     3.12   0.00
Specieswels                           0.62   0.28     2.23   0.03
dam                                   0.53   0.63     0.83   0.41
season1:Speciespikeperch             -1.01   1.96    -0.51   0.61
season2:Speciespikeperch             -1.44   0.64    -2.25   0.02
season3:Speciespikeperch             -0.31   1.07    -0.29   0.77
season4:Speciespikeperch             -2.24   0.99    -2.25   0.02
season1:Specieswels                   0.23   1.91     0.12   0.90
season2:Specieswels                  -0.90   0.57    -1.56   0.12
season3:Specieswels                   0.04   1.03     0.04   0.97
season4:Specieswels                  -1.15   0.94    -1.22   0.22
season1:dam                          -4.76   5.64    -0.84   0.40
season2:dam                           1.41   0.76     1.85   0.06
season3:dam                          -0.64   0.69    -0.92   0.36
season4:dam                           1.16   1.05     1.10   0.27
Speciespikeperch:dam                  0.81   1.05     0.77   0.44
Specieswels:dam                      -0.10   0.78    -0.13   0.89
season1:Speciespikeperch:dam          3.71   6.20     0.60   0.55
season2:Speciespikeperch:dam         -1.68   1.12    -1.49   0.14
season3:Speciespikeperch:dam         -0.61   1.09    -0.56   0.58
season4:Speciespikeperch:dam         -2.29   1.37    -1.67   0.09
season1:Specieswels:dam               4.63   6.00     0.77   0.44
season2:Specieswels:dam              -1.58   0.94    -1.68   0.09
season3:Specieswels:dam               0.40   0.87     0.46   0.65
season4:Specieswels:dam              -1.97   1.18    -1.67   0.10
-----------------------------------------------------------------

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)     0.54
 Species:fi_fishid     season1       0.80
 Species:fi_fishid     season2       0.86
 Species:fi_fishid     season3       0.78
 Species:fi_fishid     season4       0.86
      Species        (Intercept)     0.00
      Species          season1       0.74
      Species          season2       0.29
      Species          season3       0.69
      Species          season4       0.59
     Residual                        0.00
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      1.00
      Species           3       0.00
-------------------------------------

Continuous predictors are mean-centered and scaled by 2 s.d.
```
```
probe_interaction(m_dam_season_sp, pred = dam, modx = season, mod2 = Species, plot.points = TRUE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Proportion of dam use", y.label = "Mean distance range (m)",legend.main="Season", modx.labels=c("Spring I","Spring II","Autumn","Summer","Winter"),main.title = "Seasonal effects of dam use on travel distance")
```
```
¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While Species (2nd moderator) = pike ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When season = 0:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of dam                  0.00   0.01     0.21   0.83
Conditional intercept         7.56   0.22    34.94   0.00

When season = 1:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of dam                  0.06   0.07     0.84   0.40
Conditional intercept         8.82   1.04     8.46   0.00

When season = 2:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of dam                  0.03   0.01     3.38   0.00
Conditional intercept         7.67   0.24    32.17   0.00

When season = 3:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.00   0.01    -0.44   0.66
Conditional intercept          7.50   0.20    37.62   0.00

When season = 4:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of dam                  0.01   0.01     0.64   0.52
Conditional intercept         8.04   0.26    31.39   0.00

¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While Species (2nd moderator) = pikeperch ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When season = 0:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of dam                  0.01   0.02     0.74   0.46
Conditional intercept         8.53   0.34    25.25   0.00

When season = 1:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.08   0.03    -2.97   0.00
Conditional intercept          5.59   0.49    11.30   0.00

When season = 2:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of dam                  0.01   0.01     0.86   0.39
Conditional intercept         7.54   0.30    25.50   0.00

When season = 3:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.00   0.01    -0.32   0.75
Conditional intercept          7.70   0.25    30.89   0.00

When season = 4:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.02   0.01    -1.86   0.06
Conditional intercept          7.37   0.31    24.09   0.00

¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While Species (2nd moderator) = wels ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When season = 0:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of dam                  0.01   0.01     1.16   0.25
Conditional intercept         8.25   0.17    48.65   0.00

When season = 1:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of dam                  0.05   0.02     2.26   0.02
Conditional intercept         7.80   0.37    20.96   0.00

When season = 2:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of dam                  0.01   0.01     1.28   0.20
Conditional intercept         7.24   0.22    33.09   0.00

When season = 3:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of dam                  0.00   0.01     0.31   0.75
Conditional intercept         7.90   0.18    43.92   0.00

When season = 4:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.01   0.00    -1.98   0.05
Conditional intercept          7.34   0.19    37.89   0.00
```
- For _pike_ the the slope of dam use is positive and significant in Autumn indicating that permanency at dam related to mean travel distance only during this season
- For _pikeperch_ this occurred in Spring I and in the opposite direction, i.e., staying at dam during this season was negatively related to distance range
- For _wels_ the relationship was positive in Spring I and negative in Winter

```
interact_plot(m_dam_season_sp, pred = dam, modx = season, mod2 = Species, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Proportion of dam use", y.label = "Mean distance range (m)", modx.labels=c("Spring I","Spring II","Autumn","Summer","Winter"), main.title = "Seasonal effects of dam use on travel distance in three fish species")
```
![Dist_range_season](/Plots/Dist_range_season_10.png "Dist_range_season")

# Is the rate of excursions to tributary related to the rate of dam use?

## 1. Fit a model using _tributary_ as DV and the Species x season interaction and other covariates if necessary

For the count variable _tributary_ we will fit models using a Poisson distribution. Alternatively, we might want to use a negative binomial regression instead (see below).
**Note** that even if fitting a gamma model is not totally correct for discrete data, otherwise a good fit would mean a likely good fit of the negative binomial (here we ommit the gamma adjustment though).

### 1.1. Find the best unconditional nested intercept-only models fitted via REML

We fit a model using the same random-effects structure selected above, which is valid for the analysis of the rate of change in time.

```
m_trib_in1<-glmer(tributary ~ 1 + (1| Species:fi_fishid)+(1| Species),data = data_distr_season, REML=T, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit, family="poisson")
m_trib_in2<-glmer(tributary ~ 1 + (1| Species:fi_fishid)+(1| Species)+(1| season),data = data_distr_season, REML=T, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit, family="poisson")
m_trib_sl<-glmer(tributary ~ 1 + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=T, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit, family="poisson")
```
- **m_trib_sl** is preferred
- **Note** that if we peform the same selection procedure as before, the result is the same as the preferred model is the most complex

### 1.2. Find the best fixed-effects and fit a model including all relevant random-effects

We conduct a hypothesis testing using a top-down approach about the effects of dam use on the rate of tributary excursions.
**Alternatively**, conduct model-selection (see point 1.2a below)

#### 1.2.1. Fit a model of seasonal differences in tributary use between species (Species x Season interaction)

```
m_trib_int<- glmer(tributary ~ 1 + season * Species + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit, family="poisson")
```
Is the model over-dispersed?

```
overdisp_fun <- function(m_trib_int) {
    rdf <- df.residual(m_trib_int)
    rp <- residuals(m_trib_int,type="pearson")
    Pearson.chisq <- sum(rp^2)
    prat <- Pearson.chisq/rdf
    pval <- pchisq(Pearson.chisq, df=rdf, lower.tail=FALSE)
    c(chisq=Pearson.chisq,ratio=prat,rdf=rdf,p=pval)
}
```
```
overdisp_fun(m_trib_int)
```
```
        chisq         ratio           rdf             p
 1.063486e+03  8.936859e+00  1.190000e+02 2.066785e-151
```
The test is significant indicating the model is over-dispersed

#### 1.2.2. Would a negative binomial be more appropiate given our dataset?

```
ddply(data_distr_season,.(Species),summarize,mean=mean(tributary),sd=sd(tributary),nobs=length(unique(fi_fishid)))
```
```
    Species     mean        sd nobs
1      pike 3.302326  7.686611   10
2 pikeperch 9.870968 11.498373    7
3      wels 5.776978 10.817353   14
```
- We see that conditional variances of the variable _tributary_ appear to vary more within each species than the mean (SD > mean) suggesting that over-dispersion of the variable is present
- In this case fitting models with Negative Binomial would be recommended as there is more variability between units than Poisson suggests
- Another option would be fitting zero-inflated models as it seems that the distribution of 0s is high and skewed (however, we ommit this analysis if nb fitting goes well)

Fit the model adjusting for a negative binomial distribution
```
m_trib_int_nb<-glmer.nb(tributary ~ 1 + season * Species + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```

How is Poisson compared to Negative Binomial?

```
anova(m_trib_int_nb, m_trib_int)
```
```
Models:
m_trib_int: tributary ~ 1 + season * Species + (1 + season | Species:fi_fishid) +
m_trib_int:     (1 + season | Species)
m_trib_int_nb: tributary ~ 1 + season * Species + (1 + season | Species:fi_fishid) +
m_trib_int_nb:     (1 + season | Species)
              Df     AIC     BIC  logLik deviance  Chisq Chi Df Pr(>Chisq)
m_trib_int    12 1687.97 1722.47 -831.98  1663.97
m_trib_int_nb 13  686.05  723.43 -330.03   660.05 1003.9      1  < 2.2e-16 ***
```

- Model **m_trib_int_nb** is preferred since the log-likelihood of the Poisson model is lower indicating a poorer fit
**Note:** If both models had the same d.f., comparing their IC would be equivalent to comparing their LL value which is lower in the Poisson model

#### 1.2.3. Compare the best-fit model with a nested model including also the rate of dam use

```
m_trib_int_nb_dam<-update(m_trib_int_nb,.~.+ dam)
```
```
anova(m_trib_int_nb, m_trib_int_nb_dam)
```
```
Models:
m_trib_int_nb: tributary ~ 1 + season * Species + (1 + season | Species:fi_fishid) +
m_trib_int_nb:     (1 + season | Species)
m_trib_int_nb_dam: tributary ~ season + Species + (1 + season | Species:fi_fishid) +
m_trib_int_nb_dam:     (1 + season | Species) + dam + season:Species
                  Df    AIC    BIC  logLik deviance  Chisq Chi Df Pr(>Chisq)
m_trib_int_nb     13 686.05 723.43 -330.03   660.05
m_trib_int_nb_dam 14 675.91 716.16 -323.95   647.91 12.144      1  0.0004924 ***
```

- The addition of _dam_ improves the model fit and we can say **m_trib_int_nb_dam** is a better model
```
summ(m_trib_int_nb_dam,  center = TRUE, scale = TRUE, n.sd = 2)
```
```
MODEL INFO:
Observations: 131
Dependent Variable: tributary
Type: Mixed effects generalized linear regression
Error Distribution: Negative Binomial(0.4038)
Link function: log

MODEL FIT:
AIC = 675.91, BIC = 716.16
Pseudo-R² (fixed effects) = 0.36
Pseudo-R² (total) = 0.93

FIXED EFFECTS:
------------------------------------------------------------
                                 Est.   S.E.   z val.      p
----------------------------- ------- ------ -------- ------
(Intercept)                     -0.81   0.81    -1.00   0.32
season                          -0.39   0.97    -0.40   0.69
Speciespikeperch                 2.67   1.14     2.34   0.02
Specieswels                      1.39   0.98     1.42   0.15
dam                             -2.62   0.69    -3.77   0.00
season:Speciespikeperch          0.43   1.11     0.39   0.70
season:Specieswels               0.33   0.97     0.35   0.73
------------------------------------------------------------

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)     1.97
 Species:fi_fishid     season        0.49
      Species        (Intercept)     0.00
      Species          season        0.00
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      0.28
      Species           3       0.00
-------------------------------------

Continuous predictors are mean-centered and scaled by 2 s.d.
```

- The rate of dam use is highly significant and likely highest in relative importance
- In fact, _dam_ is significantly related to _tributary_ whilst weakly correlated (r=-0.32, see multicolinearity tests above)
- The interaction Species x Season is not significant. We might want to remove the interaction term and re-fit the model

```
m_trib_nb_dam<-update(m_trib_int_nb_dam,.~.- season * Species)
```
```
anova(m_trib_nb_dam, m_trib_int_nb_dam)
```
```
Models:
m_trib_nb_dam: tributary ~ (1 + season | Species:fi_fishid) + (1 + season |
m_trib_nb_dam:     Species) + dam
m_trib_int_nb_dam: tributary ~ season + Species + (1 + season | Species:fi_fishid) +
m_trib_int_nb_dam:     (1 + season | Species) + dam + season:Species
                  Df    AIC    BIC  logLik deviance  Chisq Chi Df Pr(>Chisq)
m_trib_nb_dam      9 671.32 697.20 -326.66   653.32
m_trib_int_nb_dam 14 675.91 716.16 -323.95   647.91 5.4146      5     0.3674
```
The model without the interaction is preferred

Plot the Species x Season interaction from negative binomial model

```
plot(Effect(c("season","Species"),m_trib_int_nb_dam),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Dist_range_season](/Plots/Dist_range_season_11.png "Dist_range_season")


#### 1.2.4. Are there conditional effects of the dam use on tributary excursions

Fit a model including the 3-way interaction _Species * season * dam_
```
m_trib_3int_nb_dam<-glmer.nb(tributary ~ 1 + season * Species * dam + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
Summary of nested models comparisons between this and previous fitted models
```
lrtest(m_trib_int_nb, m_trib_int_nb_dam,m_trib_3int_nb_dam)
```
```
Likelihood ratio test

Model 1: tributary ~ 1 + season * Species + (1 + season | Species:fi_fishid) +
    (1 + season | Species)
Model 2: tributary ~ season + Species + (1 + season | Species:fi_fishid) +
    (1 + season | Species) + dam + season:Species
Model 3: tributary ~ 1 + season * Species * dam + (1 + season | Species:fi_fishid) +
    (1 + season | Species)
  #Df  LogLik Df   Chisq Pr(>Chisq)
1  13 -330.03
2  14 -323.95  1 12.1443  0.0004924 ***
3  19 -320.01  5  7.8786  0.1630533
```
**m_trib_3int_nb_dam** is preferred over any of the reduced nested forms
```
summ(m_trib_3int_nb_dam,  center = TRUE, scale = TRUE, n.sd = 2)
```
```
MODEL INFO:
Observations: 131
Dependent Variable: tributary
Type: Mixed effects generalized linear regression
Error Distribution: Negative Binomial(0.4656)
Link function: log

MODEL FIT:
AIC = 678.03, BIC = 732.66
Pseudo-R² (fixed effects) = 0.43
Pseudo-R² (total) = 0.94

FIXED EFFECTS:
----------------------------------------------------------------
                                     Est.   S.E.   z val.      p
--------------------------------- ------- ------ -------- ------
(Intercept)                         -0.69   0.84    -0.81   0.42
season                              -0.67   1.04    -0.64   0.52
Speciespikeperch                     2.47   1.19     2.08   0.04
Specieswels                          1.20   1.01     1.18   0.24
dam                                  0.88   1.47     0.60   0.55
season:Speciespikeperch              0.76   1.30     0.58   0.56
season:Specieswels                   0.32   1.13     0.28   0.78
season:dam                           1.07   3.03     0.35   0.73
Speciespikeperch:dam                -4.35   2.24    -1.94   0.05
Specieswels:dam                     -4.10   1.72    -2.39   0.02
season:Speciespikeperch:dam         -1.29   3.68    -0.35   0.73
season:Specieswels:dam              -2.44   3.41    -0.72   0.47
----------------------------------------------------------------

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)     2.01
 Species:fi_fishid     season        0.66
      Species        (Intercept)     0.00
      Species          season        0.00
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      0.31
      Species           3       0.00
-------------------------------------

Continuous predictors are mean-centered and scaled by 2 s.d.
```

- There is significant effects of the Species x dam interaction in _pikeperch_ and _wels_
- This is shown in the plot below where the line for _wels_ start to diverge and interact with that of _pikeperch_ at higher dam use
- Since the Species x Season interaction is irrelevant in the second plot we show only the Species x dam interaction

Plot the Species x Season x dam interaction

```
plot(Effect(c("season","Species", "dam"),m_trib_3int_nb_dam),lines=list(multiline=TRUE), rug = FALSE, layout=c(3, 2))
```
![Dist_range_season](/Plots/Dist_range_season_12.png "Dist_range_season")

```
plot(Effect(c("Species", "dam"),m_trib_3int_nb_dam),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Dist_range_season](/Plots/Dist_range_season_13.png "Dist_range_season")


```
probe_interaction(m_trib_3int_nb_dam, pred = dam, modx = Species, plot.points = TRUE, jnplot = FALSE)
```
```
SIMPLE SLOPES ANALYSIS

Slope of dam when Species = wels:

   Est.   S.E.   z val.      p
------- ------ -------- ------
  -0.05   0.01    -3.68   0.00

Slope of dam when Species = pikeperch:

   Est.   S.E.   z val.      p
------- ------ -------- ------
  -0.06   0.01    -9.37   0.00

Slope of dam when Species = pike:

  Est.   S.E.   z val.      p
------ ------ -------- ------
  0.02   0.02     0.62   0.54
```
```
interact_plot(m_trib_3int_nb_dam, pred = dam, modx = Species, plot.points = TRUE, cond.int = TRUE, interval = FALSE,jnplot = FALSE ,x.label = "Rate of dam use", y.label = "Rate of tributary excursions", main.title = "Conditional effects of dam use on tributary excursions")
```
![Dist_range_season](/Plots/Dist_range_season_14.png "Dist_range_season")

### 1.2a. Model-selection on the rate of tributary excursions

```
Cand.mod <- list()

Cand.mod[[1]]<-glmer.nb(tributary ~ 1 + dam + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[2]]<-glmer.nb(tributary ~ 1 + dam + season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[3]]<-glmer.nb(tributary ~ 1 + dam + season + Species + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[4]]<-glmer.nb(tributary ~ 1 + dam * season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[5]]<-glmer.nb(tributary ~ 1 + dam * season + Species + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[6]]<-glmer.nb(tributary ~ 1 + dam * season * Species + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[7]]<-glmer.nb(tributary ~ 1 + dam * Species + season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[8]]<-glmer.nb(tributary ~ 1 + dam * Species + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[9]]<-glmer.nb(tributary ~ 1 + Species + season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[10]]<-glmer.nb(tributary ~ 1 + Species * season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[11]]<-glmer.nb(tributary ~ 1 + Species + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[12]]<-glmer.nb(tributary ~ 1 + season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
Name models
```
Modnames <- c("Model 1", "Model 2", "Model 3", "Model 4", "Model 5",
              "Model 6", "Model 7", "Model 8","Model 9", "Model 10", "Model 11", "Model 12")
```
Create a model selection table based on AICc, better than AIC/BIC with small samples
```
aictab(cand.set = Cand.mod, modnames = Modnames)
```
```
Model selection based on AICc:

          K   AICc Delta_AICc AICcWt Cum.Wt      LL
Model 8  13 671.14       0.00   0.45   0.45 -321.01
Model 1   9 672.72       1.57   0.21   0.66 -326.61
Model 7  14 673.63       2.49   0.13   0.79 -321.01
Model 3  12 674.62       3.48   0.08   0.87 -323.99
Model 2  10 674.93       3.79   0.07   0.94 -326.55
Model 5  13 676.62       5.48   0.03   0.97 -323.76
Model 4  11 676.78       5.64   0.03   1.00 -326.28
Model 12  9 684.23      13.09   0.00   1.00 -332.37
Model 9  11 684.46      13.32   0.00   1.00 -330.12
Model 11 10 684.47      13.33   0.00   1.00 -331.32
Model 6  19 684.84      13.70   0.00   1.00 -320.00
Model 10 13 689.16      18.02   0.00   1.00 -330.03
```
Compute the evidence ratio
```
evidence(aictab(cand.set = Cand.mod, modnames = Modnames))
```
```
Evidence ratio between models 'Model 8' and 'Model 1': 2.2
```
Compute confidence set based on 'raw' method
```
confset(cand.set = Cand.mod, modnames = Modnames, second.ord = TRUE, method = "raw")
```
```
Confidence set for the best model

Method:	 raw sum of model probabilities

95% confidence set:
         K   AICc Delta_AICc AICcWt
Model 8 13 671.14       0.00   0.45
Model 1  9 672.72       1.57   0.21
Model 7 14 673.63       2.49   0.13
Model 3 12 674.62       3.48   0.08
Model 2 10 674.93       3.79   0.07
Model 5 13 676.62       5.48   0.03

Model probabilities sum to 0.97
```

Compare the three best-fit models (within 2.5 Delta_AICc) of the confidence

```
m8<-glmer.nb(tributary ~ 1 + dam * Species + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m1<-glmer.nb(tributary ~ 1 + dam + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m7<-glmer.nb(tributary ~ 1 + dam * Species + season + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
lrtest(m8,m1)
```
```
Likelihood ratio test

Model 1: tributary ~ 1 + dam * Species + (1 + season | Species:fi_fishid) +
    (1 + season | Species)
Model 2: tributary ~ 1 + dam + (1 + season | Species:fi_fishid) + (1 +
    season | Species)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  13 -321.01
2   9 -326.61 -4 11.198    0.02443 *
```
```
lrtest(m8,m7)
```
```
Likelihood ratio test

Model 1: tributary ~ 1 + dam * Species + (1 + season | Species:fi_fishid) +
    (1 + season | Species)
Model 2: tributary ~ 1 + dam * Species + season + (1 + season | Species:fi_fishid) +
    (1 + season | Species)
  #Df  LogLik Df Chisq Pr(>Chisq)
1  13 -321.01
2  14 -321.01  1 0.019     0.8903
```
Model **m8** is preferred to either a model without the interaction and to a model with the variable _season_

Simple slope analysis
```
probe_interaction(m8, pred = dam, modx = Species, plot.points = TRUE, jnplot = FALSE)
```
```
SIMPLE SLOPES ANALYSIS

Slope of dam when Species = wels:

   Est.   S.E.   z val.      p
------- ------ -------- ------
  -0.06   0.01    -4.04   0.00

Slope of dam when Species = pikeperch:

   Est.   S.E.   z val.      p
------- ------ -------- ------
  -0.06   0.02    -2.30   0.02

Slope of dam when Species = pike:

  Est.   S.E.   z val.      p
------ ------ -------- ------
  0.01   0.02     0.65   0.52
```
The results are nearly the same as those of the 3-way model **m_trib_3int_nb_dam** excluding _season_; however, we can say this the current model is the correct fit to our data

```
interact_plot(m8, pred = dam, modx = Species, plot.points = TRUE, cond.int = TRUE, interval = FALSE,jnplot = FALSE ,x.label = "Rate of dam use", y.label = "Rate of tributary excursions", main.title = "Conditional effects of dam use on tributary excursions")
```

![Dist_range_season](/Plots/Dist_range_season_15.png "Dist_range_season")






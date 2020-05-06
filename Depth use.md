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


## 1. Fit Mixed-Effects Models (LMM) to the data of depth use

- To analyse the depth use made by the three species we fit a series of LMMs using _mean_depth_ as DV and _Species_, _season_, _body_size_ and _res_part_ as predictors
- We use two approaches, one consisting of model selection based on AICc for selection of mixed models and other specifically testing our hypothesis based on the nested structure of the random effects
- The aim of this is just evidencing some sort of model fitting singularities, either due to perfect separation probems, too low sample size or overdispersion. Actually, in our case most singulatiry issues appear to be due to
the existence of duplicated measures of the variable _mean_depth_ between single days. The aggregating nature of these measures might indicate an incorrect sampling

### 1.1. Find the best onditional model for the Species x season interaction

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

### 1.2. Find the best conditional model of Species x season controlling for body size and reservoir parts

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

### 1.3. Compare best-fit models

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
tab_model(m4,m3,m1,m2, transform = NULL, collapse.ci = F,  auto.label = FALSE,  show.se = TRUE,collapse.se = T,
              dv.labels = c("Model 1", "Model 2","Model 3","Model 4"),
               string.pred = "Variable",
               string.p = "P" , use.viewer = TRUE)
```
|                                  	| Model 1        	|               	|        	| Model 2        	|               	|        	| Model 3        	|               	|        	| Model 4        	|               	|        	|
|----------------------------------	|----------------	|---------------	|--------	|----------------	|---------------	|--------	|----------------	|---------------	|--------	|----------------	|---------------	|--------	|
| Variable                         	| Estimates      	| CI            	| P      	| Estimates      	| CI            	| P      	| Estimates      	| CI            	| P      	| Estimates      	| CI            	| P      	|
| (Intercept)                      	| 6.32 (1.84)    	| 2.72 – 9.93   	| 0.001  	| 3.80 (0.80)    	| 2.23 – 5.37   	| <0.001 	| 3.18 (0.69)    	| 1.82 – 4.54   	| <0.001 	| 5.18 (1.62)    	| 1.99 – 8.36   	| 0.001  	|
| Speciespikeperch                 	| 0.70 (1.26)    	| -1.77 – 3.16  	| 0.579  	| 2.09 (0.92)    	| 0.29 – 3.88   	| 0.023  	| 2.84 (0.80)    	| 1.27 – 4.41   	| <0.001 	| 1.74 (1.11)    	| -0.44 – 3.92  	| 0.117  	|
| Specieswels                      	| 6.77 (0.94)    	| 4.93 – 8.60   	| <0.001 	| 6.24 (0.94)    	| 4.40 – 8.08   	| <0.001 	| 6.90 (0.82)    	| 5.29 – 8.50   	| <0.001 	| 7.31 (0.83)    	| 5.69 – 8.94   	| <0.001 	|
| seasonspring_I                   	| -2.32 (0.24)   	| -2.78 – -1.86 	| <0.001 	| -2.32 (0.24)   	| -2.78 – -1.86 	| <0.001 	| -1.82 (0.25)   	| -2.31 – -1.33 	| <0.001 	| -1.82 (0.25)   	| -2.31 – -1.33 	| <0.001 	|
| seasonspring_II                  	| -2.55 (0.44)   	| -3.40 – -1.70 	| <0.001 	| -2.56 (0.44)   	| -3.41 – -1.70 	| <0.001 	| -1.85 (0.47)   	| -2.76 – -0.93 	| <0.001 	| -1.84 (0.47)   	| -2.76 – -0.93 	| <0.001 	|
| seasonsummer                     	| -0.28 (0.21)   	| -0.69 – 0.13  	| 0.175  	| -0.28 (0.21)   	| -0.69 – 0.13  	| 0.175  	| -0.20 (0.22)   	| -0.64 – 0.24  	| 0.365  	| -0.20 (0.22)   	| -0.64 – 0.24  	| 0.363  	|
| seasonwinter                     	| -0.91 (0.22)   	| -1.35 – -0.47 	| <0.001 	| -0.91 (0.22)   	| -1.35 – -0.47 	| <0.001 	| -0.40 (0.24)   	| -0.87 – 0.07  	| 0.095  	| -0.40 (0.24)   	| -0.87 – 0.07  	| 0.097  	|
| body_size                        	| -0.00 (0.00)   	| -0.01 – 0.00  	| 0.134  	|                	|               	|        	|                	|               	|        	| -0.00 (0.00)   	| -0.00 – 0.00  	| 0.180  	|
| res_partmiddle                   	| 0.41 (0.12)    	| 0.17 – 0.65   	| 0.001  	| 0.41 (0.12)    	| 0.17 – 0.65   	| 0.001  	|                	|               	|        	|                	|               	|        	|
| res_parttributary                	| -1.61 (0.13)   	| -1.87 – -1.35 	| <0.001 	| -1.62 (0.13)   	| -1.88 – -1.35 	| <0.001 	|                	|               	|        	|                	|               	|        	|
| res_partupper                    	| 0.14 (0.14)    	| -0.13 – 0.41  	| 0.299  	| 0.13 (0.14)    	| -0.13 – 0.40  	| 0.325  	|                	|               	|        	|                	|               	|        	|
| Speciespikeperch:seasonspring_I  	| -1.06 (0.27)   	| -1.58 – -0.54 	| <0.001 	| -1.06 (0.27)   	| -1.58 – -0.54 	| <0.001 	| -2.23 (0.28)   	| -2.77 – -1.68 	| <0.001 	| -2.23 (0.28)   	| -2.77 – -1.68 	| <0.001 	|
| Specieswels:seasonspring_I       	| -5.47 (0.27)   	| -6.00 – -4.95 	| <0.001 	| -5.47 (0.27)   	| -6.00 – -4.95 	| <0.001 	| -6.78 (0.28)   	| -7.32 – -6.23 	| <0.001 	| -6.78 (0.28)   	| -7.32 – -6.23 	| <0.001 	|
| Speciespikeperch:seasonspring_II 	| -2.03 (0.52)   	| -3.04 – -1.02 	| <0.001 	| -2.02 (0.52)   	| -3.04 – -1.01 	| <0.001 	| -2.77 (0.55)   	| -3.85 – -1.69 	| <0.001 	| -2.78 (0.55)   	| -3.85 – -1.70 	| <0.001 	|
| Specieswels:seasonspring_II      	| -1.90 (0.49)   	| -2.86 – -0.93 	| <0.001 	| -1.89 (0.49)   	| -2.86 – -0.93 	| <0.001 	| -2.89 (0.52)   	| -3.91 – -1.86 	| <0.001 	| -2.89 (0.52)   	| -3.92 – -1.86 	| <0.001 	|
| Speciespikeperch:seasonsummer    	| -2.44 (0.24)   	| -2.90 – -1.98 	| <0.001 	| -2.44 (0.24)   	| -2.90 – -1.98 	| <0.001 	| -3.24 (0.25)   	| -3.73 – -2.75 	| <0.001 	| -3.24 (0.25)   	| -3.73 – -2.75 	| <0.001 	|
| Specieswels:seasonsummer         	| -6.08 (0.24)   	| -6.55 – -5.62 	| <0.001 	| -6.08 (0.24)   	| -6.55 – -5.62 	| <0.001 	| -7.03 (0.25)   	| -7.52 – -6.54 	| <0.001 	| -7.03 (0.25)   	| -7.51 – -6.54 	| <0.001 	|
| Speciespikeperch:seasonwinter    	| -0.77 (0.25)   	| -1.27 – -0.27 	| 0.002  	| -0.77 (0.25)   	| -1.27 – -0.27 	| 0.002  	| -1.48 (0.27)   	| -2.01 – -0.95 	| <0.001 	| -1.48 (0.27)   	| -2.01 – -0.95 	| <0.001 	|
| Specieswels:seasonwinter         	| -0.89 (0.25)   	| -1.38 – -0.39 	| <0.001 	| -0.88 (0.25)   	| -1.38 – -0.39 	| <0.001 	| -1.59 (0.27)   	| -2.12 – -1.07 	| <0.001 	| -1.60 (0.27)   	| -2.12 – -1.07 	| <0.001 	|
| Random Effects                   	|                	|               	|        	|                	|               	|        	|                	|               	|        	|                	|               	|        	|
| σ2                               	| 3.18           	|               	|        	| 3.18           	|               	|        	| 3.64           	|               	|        	| 3.64           	|               	|        	|
| τ00                              	| 0.22 date      	|               	|        	| 0.22 date      	|               	|        	| 0.27 date      	|               	|        	| 0.27 date      	|               	|        	|
|                                  	| 1.03 fi_fishid 	|               	|        	| 1.22 fi_fishid 	|               	|        	| 0.92 fi_fishid 	|               	|        	| 0.80 fi_fishid 	|               	|        	|
| ICC                              	| 0.28           	|               	|        	| 0.31           	|               	|        	| 0.25           	|               	|        	| 0.23           	|               	|        	|
| N                                	| 13 fi_fishid   	|               	|        	| 13 fi_fishid   	|               	|        	| 13 fi_fishid   	|               	|        	| 13 fi_fishid   	|               	|        	|
|                                  	| 344 date       	|               	|        	| 344 date       	|               	|        	| 344 date       	|               	|        	| 344 date       	|               	|        	|
| Observations                     	| 3671           	|               	|        	| 3671           	|               	|        	| 3671           	|               	|        	| 3671           	|               	|        	|
| Marginal R2 / Conditional R2     	| 0.659 / 0.756  	|               	|        	| 0.644 / 0.755  	|               	|        	| 0.613 / 0.708  	|               	|        	| 0.626 / 0.711  	|               	|        	|

## 2. Analysis of the Species x season interaction

#### Summarise of best-fit model

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

#### Calculate Level-2 and Level-3 ICC indices

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

#### Pairwise comparisons

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

#### Plot main-effects

:books:`library(effects)`
```
plot(Effect(c("Species", "season"), m3),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Mean_depth_date](/Plots/Mean_depth_date_00.png "Mean_depth_date")

#### Plot marginal effects

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

#### Plot mean depth as subject to distance range against daily date

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
- Wether this is indicating complete lack of vertical movement or stationarity is unlikely so it must related to some error during the sampling process/dataset creation



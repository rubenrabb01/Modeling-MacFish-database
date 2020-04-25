# Is there selection on the use of reservoir parts?

## Create a dataframe including reservoir parts

:books:`library(plyr)`

Subset preference of reservoir parts and merge with the full 'data' dataset
```
data.parts.pref.info <- subset(parts.pref.full,  select=c(fi_fishid, date, res_part))
data_poglm <- full_join(data, data.parts.pref.info, by = c("fi_fishid", "date"))
```
Count use of reservoir parts by season
```
data_poglm <- data_poglm %>% add_count(season, fi_fishid, res_part)
```
Create a new ordered response variable for res_part with a four-point scale (0-3) and rename the variable "ca_tl_mm" into "body_size"
```
data_poglm$res_part_order <- revalue(data_poglm$res_part,c("dam"="0", "middle"="1", "upper"="2", "tributary"="3"))
colnames(data_poglm)[5] <- "body_size"
```
| fi_fishid | date       | season | species   | res_part  | res_part_order | ranged2d  | meand2d   | mean_depth | body_size | ca_lat_catch c | ca_lon_catch |
|-----------|------------|--------|--------------------|-----------|----------------|-----------|-----------|------------|-----------|----------------|--------------|
| T449202_1 | 18/08/2017 | summer | pikeperch          | middle    | 1              | 784.0089  | 8159.387  | 1.4189065  | 430       | 48.82551       | 14.4848      |
| T449202_1 | 25/01/2018 | winter | pikeperch          | dam       | 0              | 0         | 8551.391  | 1.6873192  | 430       | 48.82551       | 14.4848      |
| T449202_1 | 24/01/2018 | winter | pikeperch          | upper     | 2              | 2301.8921 | 7400.445  | 5.5925592  | 430       | 48.82551       | 14.4848      |
| T449276_1 | 19/08/2017 | summer | wels               | middle    | 1              | 784.0089  | 8159.387  | 0.6493313  | 1030      | 48.81964       | 14.48151     |
| T449276_1 | 20/08/2017 | summer | wels               | dam       | 0              | 2129.6148 | 7486.584  | 0.6721918  | 1030      | 48.81964       | 14.48151     |
| T449276_1 | 21/08/2017 | summer | wels               | tributary | 3              | 2093.8532 | 7504.465  | 0.8017406  | 1030      | 48.81964       | 14.48151     |
| T449271_1 | 17/08/2017 | summer | pike               | upper     | 2              | 12:17:24  | 2402.5503 | 5.6171613  | 915       | 48.83096       | 14.47816     |
| T449271_1 | 18/08/2017 | summer | pike               | tributary | 3              | 22:07:05  | 2371.9243 | 5.7136752  | 915       | 48.83096       | 14.47816     |
| T449271_1 | 23/12/2017 | winter | pike               | upper     | 2              | 17:04:54  | 6905.8552 | 3.0325587  | 915       | 48.83096       | 14.47816     |

Convert variables for analysis
```
data_poglm$season <- as.factor(data_poglm$season)
data_poglm$res_part <- as.factor(data_poglm$res_part)
data_poglm$res_part_order <- as.factor(data_poglm$res_part_order)
data_poglm$fi_fishid <- as.factor(data_poglm$fi_fishid)
data_poglm$fi_species <- as.factor(data_poglm$fi_species)
```
Create five datasets for each of the five time periods
```
spring_I <- subset(data_poglm,season=="spring_I")
spring_II <- subset(data_poglm,season=="spring_II")
summer <- subset(data_poglm,season=="summer")
autumn <- subset(data_poglm,season=="autumn")
winter <- subset(data_poglm,season=="winter")
```
## Fit Proportional Odds Model (_POGLMM_) to  the data of daily use of reservoir parts (FULL DATASET)

_The fitted models will serve to test the hypothesis that pike, pikeperch and wels make different use of the different reservoir parts and that there are seasonal preferences_

- We will consider a logit model with four factors for the ordered response in reservoir parts use fitted using the _clmm_ function for mixed-effects models in library **ordinal**

- Our logic is as follows:

  - Dam: "0" (Fish show closeness to source and low displacement from it)
  - Middle: "1" (Fish move from dam)
  - Upper: "2" (Fish cover long distance from dam)
  - Tributary: "3" (Maximum displacement from dam (high prey activity - predation))

### 1. Fit a series of null (intercepts-only) _POGLMMs_ and compare their random-effects structure

:books:`library(ART)`
:books:`library(mlogit)`
:books:`library(AICcmodavg)`
:books:`library(MASS)`
:books:`library(effects)`
:books:`library(lme4)`
:books:`library(languageR)`
:books:`library(ordinal)`

To prevent the error "models were not all fitted to the same size of dataset" upon performing a Log-likelihood Ratio test (LRT) we need to fit the first model to a dataset without missing data including the "fi_species" variable
```
data_poglm_sub <- data_poglm[which(complete.cases(data_poglm[,c('fi_fishid', 'fi_species')])),]
```
Fit models using the subseted dataset
```
m1<-clmm(res_part_order ~ 1  + (1| fi_fishid),data = data_poglm_sub, link="logit",Hess=T)
m2<-clmm(res_part_order ~ 1  + (1| fi_species) + (1| fi_fishid),data = data_poglm_sub, link="logit",Hess=T)
m3<-clmm(res_part_order ~ 1  + (1| season) + (1| fi_fishid),data = data_poglm_sub, link="logit",Hess=T)
m4<-clmm(res_part_order ~ 1  + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T)
```
Order models from best to worst fitting in terms of Akaike values (i.e., BIC)

:books:`library(dLagM)`

```
bic = BIC(m1,m2,m3,m4)
sortScore(bic , score = "bic")
```
```
  df      BIC
m3  5 12153.67
m4  6 12162.26
m1  4 12226.89
m2  5 12235.47
```
The four models are very close in term of BIC values but we keep Model **m3** for further analysis of full models

### 2. Fit a series of conditional _POGLMMs_ (incl. covariates) with the RFs structure of selected model (m4)

Build a candidate models set and perform model selection based on BIC
```
Cand.mod<-matrix(ncol=1,nrow=13)
colnames(Cand.mod)<-c("BIC")
rownames(Cand.mod)<-c(1:13)

Cand.mod[[1]]<-AIC(clmm(res_part_order ~ 1 + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[2]]<-AIC(clmm(res_part_order ~ 1 + body_size + fi_species + season + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[3]]<-AIC(clmm(res_part_order ~ 1 + body_size + fi_species * season + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[4]]<-AIC(clmm(res_part_order ~ 1 + body_size * fi_species + season + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[5]]<-AIC(clmm(res_part_order ~ 1 + body_size + fi_species + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[6]]<-AIC(clmm(res_part_order ~ 1 + body_size + season + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[7]]<-AIC(clmm(res_part_order ~ 1 + fi_species + season + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[8]]<-AIC(clmm(res_part_order ~ 1 + body_size * fi_species + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[9]]<-AIC(clmm(res_part_order ~ 1 + body_size * season + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[10]]<-AIC(clmm(res_part_order ~ 1 + fi_species * season + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[11]]<-AIC(clmm(res_part_order ~ 1 + body_size + (1| fi_species) + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[12]]<-AIC(clmm(res_part_order ~ 1 + season + (1| fi_species) + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[13]]<-AIC(clmm(res_part_order ~ 1 + fi_species + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T))

Cand.mod
sort(Cand.mod)
both<-data.frame(1:13,Cand.mod)
names(both)<-c("model","BIC")
both[do.call(order, both[c("BIC")]), ]
```
```
 model      BIC
10    10 11983.18
3      3 11984.15
9      9 12099.24
6      6 12115.47
12    12 12115.77
7      7 12117.45
2      2 12118.31
4      4 12120.86
1      1 12122.72
13    13 12126.40
11    11 12126.43
5      5 12127.27
8      8 12129.83
```
Rename and order models from best to worst fitting in terms of Akaike values
```
m10<-clmm(res_part_order ~ 1 + fi_species * season + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T)
m3<-clmm(res_part_order ~ 1 + body_size + fi_species * season + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T)
m9<-clmm(res_part_order ~ 1 + body_size * season + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T)
```
Perform a LRT comparison between the first best-fit Model (**m10**) and subsequent best-fit Models **m3** and **m9**. We drop Models **m6** and forward as their difference with the first best model (i.e., DeltaBIC) are above two higher units
```
lrtest(m10,m3)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + fi_species * season + (1 | fi_species) +
    (1 | fi_fishid) + (1 | season)
Model 2: res_part_order ~ 1 + body_size + fi_species * season + (1 | fi_species) +
    (1 | fi_fishid) + (1 | season)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  20 -5971.6
2  21 -5971.1  1 1.0368     0.3086
```
The LRT is not significant indicating that the reduced Model **m10** is preferred to the expanded Model **m3**, i.e., the addition of body_size does not significantly to explaining the variability in the use of reservoir parts
On the other hand, **m10** and **m9** can not be compared using LRT as they do not have nested fixed-effects. We pick the one with higher lohLikelihood and lower BIC
```
anova(m10,m9)
```
```
Likelihood ratio tests of cumulative link models:

    formula:                                                                                     link: threshold:
m9  res_part_order ~ 1 + body_size * season + (1 | fi_species) + (1 | fi_fishid) + (1 | season)  logit flexible
m10 res_part_order ~ 1 + fi_species * season + (1 | fi_species) + (1 | fi_fishid) + (1 | season) logit flexible

    no.par   AIC  logLik LR.stat df Pr(>Chisq)
m9      15 12099 -6034.6
m10     20 11983 -5971.6  126.05  5  < 2.2e-16 ***
```
We retain **m10** but will use **m9** for further hypothesis testing of body size variation across seasons
```
summary(m10)
```
```
 Cumulative Link Mixed Model fitted with the Laplace approximation

formula: res_part_order ~ 1 + fi_species * season + (1 | fi_species) +      (1 | fi_fishid) + (1 | season)
data:    data_poglm_sub

 link  threshold nobs logLik   AIC      niter       max.grad cond.H
 logit flexible  5375 -5971.59 11983.18 2471(12430) 1.96e-03 1.0e+04

Random effects:
 Groups     Name        Variance Std.Dev.
 fi_fishid  (Intercept) 2.499    1.581
 season     (Intercept) 0.000    0.000
 fi_species (Intercept) 0.000    0.000
Number of groups:  fi_fishid 13,  season 5,  fi_species 3

Coefficients:
                                    Estimate Std. Error z value Pr(>|z|)
fi_speciespikeperch                  0.32682    1.30010   0.251 0.801522
fi_specieswels                       0.15392    1.33047   0.116 0.907902
seasonspring_I                      -0.22010    0.17550  -1.254 0.209798
seasonspring_II                     -1.85036    0.31830  -5.813 6.12e-09 ***
seasonsummer                        -0.61568    0.16130  -3.817 0.000135 ***
seasonwinter                        -0.03944    0.17559  -0.225 0.822272
fi_speciespikeperch:seasonspring_I   0.51969    0.22108   2.351 0.018737 *
fi_specieswels:seasonspring_I        1.09825    0.21666   5.069 4.00e-07 ***
fi_speciespikeperch:seasonspring_II  2.46086    0.42576   5.780 7.47e-09 ***
fi_specieswels:seasonspring_II       1.64848    0.37642   4.379 1.19e-05 ***
fi_speciespikeperch:seasonsummer     0.50378    0.20008   2.518 0.011804 *
fi_specieswels:seasonsummer          1.47234    0.19680   7.481 7.36e-14 ***
fi_speciespikeperch:seasonwinter    -0.30180    0.21932  -1.376 0.168792
fi_specieswels:seasonwinter         -0.37172    0.20741  -1.792 0.073106 .
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Threshold coefficients:
    Estimate Std. Error z value
0|1 -2.22428    1.12494  -1.977
1|3  0.02067    1.12429   0.018
3|2  1.63703    1.12449   1.456
```
Plot body size x season
```
plot(allEffects(m10,xlevels=list(fi_species=c("pike","pikeperch","wels"))), rug = FALSE, style = "stacked",col=cm.colors(5))
```
![Res_part_use](/Plots/Res_part_use_1.png "Res_part_use")

Taking Model **m9**, is there a body-size dependent seasonal effect on the use of reservoir parts?
```
summary(m9)
```
```
Cumulative Link Mixed Model fitted with the Laplace approximation

formula: res_part_order ~ 1 + body_size * season + (1 | fi_species) +      (1 | fi_fishid) + (1 | season)
data:    data_poglm_sub

 link  threshold nobs logLik   AIC      niter       max.grad cond.H
 logit flexible  5375 -6034.62 12099.24 2321(12731) 7.88e+02 1.5e+09

Random effects:
 Groups     Name        Variance Std.Dev.
 fi_fishid  (Intercept) 2.558249 1.59945
 season     (Intercept) 0.000263 0.01622
 fi_species (Intercept) 0.191663 0.43779
Number of groups:  fi_fishid 13,  season 5,  fi_species 3

Coefficients:
                            Estimate Std. Error z value Pr(>|z|)
body_size                  0.0006956  0.0012675   0.549  0.58313
seasonspring_I             1.3408623  0.2112536   6.347 2.19e-10 ***
seasonspring_II            1.7203238  0.4187490   4.108 3.99e-05 ***
seasonsummer               0.4805728  0.1861680   2.581  0.00984 **
seasonwinter               0.0482188  0.2054357   0.235  0.81443
body_size:seasonspring_I  -0.0009762  0.0002112  -4.621 3.82e-06 ***
body_size:seasonspring_II -0.0019996  0.0003917  -5.104 3.32e-07 ***
body_size:seasonsummer    -0.0002949  0.0001888  -1.562  0.11829
body_size:seasonwinter    -0.0003959  0.0002045  -1.935  0.05294 .
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Threshold coefficients:
    Estimate Std. Error z value
0|1  -1.5337     1.2469  -1.230
1|3   0.6195     1.2469   0.497
3|2   2.2290     1.2472   1.787
```
```
plot(allEffects(m9,xlevels=list(fi_species=c("pike","pikeperch","wels"))), rug = FALSE, style = "stacked",col=cm.colors(5))
```
![Res_part_use](/Plots/Res_part_use_2.png "Res_part_use")

The worst fit-model, **m8** may help us to see the relationship between the use of reservoir parts by each species

```
m8<-clmm(res_part_order ~ 1 + body_size * fi_species + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T)
```
```
summary(m8)
```
```
Cumulative Link Mixed Model fitted with the Laplace approximation

formula: res_part_order ~ 1 + body_size * fi_species + (1 | fi_species) +
    (1 | fi_fishid) + (1 | season)
data:    data_poglm_sub

 link  threshold nobs logLik   AIC      niter      max.grad cond.H
 logit flexible  5375 -6053.92 12129.83 1193(7050) 9.83e-03 3.2e+09

Random effects:
 Groups     Name        Variance Std.Dev.
 fi_fishid  (Intercept) 2.04462  1.4299
 season     (Intercept) 0.09973  0.3158
 fi_species (Intercept) 0.00000  0.0000
Number of groups:  fi_fishid 13,  season 5,  fi_species 3

Coefficients:
                               Estimate Std. Error z value Pr(>|z|)
body_size                      0.008725   0.008283   1.053    0.292
fi_speciespikeperch           13.162174   9.945927   1.323    0.186
fi_specieswels                 6.477671   9.201870   0.704    0.481
body_size:fi_speciespikeperch -0.016380   0.013328  -1.229    0.219
body_size:fi_specieswels      -0.006073   0.008635  -0.703    0.482

Threshold coefficients:
    Estimate Std. Error z value
0|1    7.238      8.655   0.836
1|3    9.405      8.656   1.087
3|2   11.000      8.656   1.271
```
```
plot(allEffects(m8,xlevels=list(fi_species=c("pike","pikeperch","wels"))), rug = FALSE, style = "stacked",col=cm.colors(5))
```
![Res_part_use](/Plots/Res_part_use_3.png "Res_part_use")

- Fitting two additional models of the interactions Species x Depth and Species X Size, and accounting for variation in the slope of mean_depth beetween species
```
m71<-clmm(res_part_order ~ 1 + fi_species*mean_depth+(1| fi_species:fi_fishid) + (1 + mean_depth | fi_species),data = data_poglm, link="logit",Hess=T)
```
```
plot(allEffects(m71,xlevels=list(res_part_order=seq(0,3,length=2))),rug = FALSE)
```
![Res_part_use](/Plots/Res_part_use_4.png "Res_part_use")

```
m72<-clmm(res_part_order ~ 1 + fi_species*body_size+(1| fi_species:fi_fishid) + (1 + mean_depth | fi_species),data = data_poglm, link="logit",Hess=T)
```
```
plot(allEffects(m72,xlevels=list(res_part_order=seq(0,3,length=2))),rug = FALSE)
```
![Res_part_use](/Plots/Res_part_use_5.png "Res_part_use")

- If we fit a model of the Species x Size interaction dropping the slope variance between species 
```
m_id_sp<-clmm(res_part_order ~ body_size*fi_species+(1| fi_fishid)+(1|fi_species),data = data_poglm, link="logit",Hess=T)
```
```
plot(allEffects(para_plot,xlevels=list( seq(405,1660,length=2),fi_species=c("pike","pikeperch","wels"))), rug = FALSE, style = "stacked", main="Selection of reservoir parts by pike, pikerpech and wels",xlab="Body size",ylab="Response probability",colors = c("white", "grey", "red","green"))
```
![Res_part_use](/Plots/Res_part_use_6.png "Res_part_use")

Finally, let's fit some other interaction models
```
m_int_size_depth_sp<-clmm(res_part_order ~ body_size*mean_depth*fi_species+(1| fi_fishid),data = data_poglm, link="logit",Hess=T)
```
```
plot(allEffects(m_int_size_depth_sp,xlevels=list( seq(405,1660,length=2),fi_species=c("pike","pikeperch","wels"))), rug = FALSE, style = "stacked", main="Selection of reservoir parts by pike, pikerpech and wels",xlab="mean depth",ylab="Response probability",colors = c("white", "grey", "red","green"))
```
![Res_part_use](/Plots/Res_part_use_7.png "Res_part_use")
```
m_int_size_depth_season<-clmm(res_part_order ~ body_size*mean_depth*season+(1| fi_fishid),data = data_poglm, link="logit",Hess=T)
```
```
plot(allEffects(m_int_size_depth_season,xlevels=list(seq(405,1660,length=2),season=c("spring_I","spring_II","autumn","summer","winter"))), rug = FALSE, style = "stacked", main="Selection of reservoir parts for different body size",xlab="mean depth",ylab="Response probability",colors = c("white", "grey", "red","green"))
```
![Res_part_use](/Plots/Res_part_use_8.png "Res_part_use")

A model to allow daily variation of horizontal activity 
```
m_int_depth_sp<-clmm(res_part_order ~ 1 + body_size+fi_species*mean_depth+(1| fi_fishid) + (1 + ranged2d | date),data = data_poglm, link="logit",Hess=T)
```
```
plot(allEffects(m_int_depth_sp,xlevels=list(mean_depth=seq(0.35,11,length=30),fi_species=c("pike","pikeperch","wels"))), rug = FALSE, style = "stacked", xlab=c("Body size","Mean depth"),ylab="Response probability",colors = c("white", "grey", "red","green"))
```
![Res_part_use](/Plots/Res_part_use_101.png "Res_part_use")
```
plot(allEffects(m_int_depth_sp,xlevels=list(mean_depth=seq(1,20,length=3),fi_species=c("pike","pikeperch","wels"))), rug = FALSE, style = "stacked", xlab="Mean depth",ylab="Response probability",colors = c("white", "grey", "red","green"))
```
![Res_part_use](/Plots/Res_part_use_102.png "Res_part_use")

## Model fit by season: SPRING I

```
Cand.mod<-matrix(ncol=1,nrow=11)
colnames(Cand.mod)<-c("BIC")
rownames(Cand.mod)<-c(1:11)

Cand.mod[1]<-AIC(clmm(res_part_order ~ 1  + (1| fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[2]<-AIC(clmm(res_part_order ~ 1  + (1| fi_species/fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[3]<-AIC(clmm(res_part_order ~ 1  + (1 + fi_species| fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[4]<-AIC(clmm(res_part_order ~ 1  + (1 + fi_species| fi_fishid) + (0 + fi_species| fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[5]<-AIC(clmm(res_part_order ~ 1  + (1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[6]<-AIC(clmm(res_part_order ~ 1  + (1 + mean_depth| fi_species) + (1 | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[7]<-AIC(clmm(res_part_order ~ 1  + (1 + mean_depth| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[8]<-AIC(clmm(res_part_order ~ 1  + (1| fi_species) + (1 + body_size | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[9]<-AIC(clmm(res_part_order ~ 1  + (1 + body_size| fi_species) + (1 | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[10]<-AIC(clmm(res_part_order ~ 1  + (1 + body_size| fi_species) + (1 + body_size | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[11]<-AIC(clmm(res_part_order ~ 1  + (1| fi_species) + (1 + date | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
```
```
Cand.mod
sort(Cand.mod)
both<-data.frame(1:11,Cand.mod)
names(both)<-c("model","BIC")
both[do.call(order, both[c("BIC")]), ]
```
```
  model      BIC
5      5 2259.284
7      7 2262.461
6      6 2273.037
1      1 2294.766
2      2 2296.766
8      8 2300.609
9      9 2300.766
3      3 2303.333
10    10 2304.850
4      4 2315.333
11    11 2333.408
```
```
m5<-clmm(res_part_order ~ 1  + (1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T)
m7<-clmm(res_part_order ~ 1  + (1 + mean_depth| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T)
m6<-clmm(res_part_order ~ 1  + (1 + mean_depth| fi_species) + (1 | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T)
m1<-clmm(res_part_order ~ 1  + (1| fi_fishid),data = spring_I, link="logit",Hess=T)
m2<-clmm(res_part_order ~ 1  + (1| fi_species/fi_fishid),data = spring_I, link="logit",Hess=T)
```
**m5** is the best model

```
Cand.mod<-matrix(ncol=1,nrow=11)
colnames(Cand.mod)<-c("BIC")
rownames(Cand.mod)<-c(1:11)

Cand.mod[[1]]<-AIC(clmm(res_part_order ~ 1 + (1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[[2]]<-AIC(clmm(res_part_order ~ 1 + body_size+mean_depth+fi_species+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[[3]]<-AIC(clmm(res_part_order ~ 1 + body_size*mean_depth+fi_species+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[[4]]<-AIC(clmm(res_part_order ~ 1 + body_size*fi_species+mean_depth+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[[5]]<-AIC(clmm(res_part_order ~ 1 + body_size+fi_species+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[[6]]<-AIC(clmm(res_part_order ~ 1 + body_size+mean_depth+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[[7]]<-AIC(clmm(res_part_order ~ 1 + body_size*mean_depth+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[[8]]<-AIC(clmm(res_part_order ~ 1 + body_size+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[[9]]<-AIC(clmm(res_part_order ~ 1 + mean_depth+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[[10]]<-AIC(clmm(res_part_order ~ 1 + mean_depth*fi_species+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
Cand.mod[[11]]<-AIC(clmm(res_part_order ~ 1 + mean_depth+fi_species+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T))
```
```
Cand.mod
sort(Cand.mod)
both<-data.frame(1:11,Cand.mod)
names(both)<-c("model","BIC")
both[do.call(order, both[c("BIC")]), ]
```
```
  model      BIC
4      4 2258.554
1      1 2259.284
9      9 2260.413
5      5 2260.493
11    11 2261.103
8      8 2261.280
2      2 2261.494
3      3 2262.201
10    10 2262.213
6      6 2262.405
7      7 2262.766
```
```
m4<-clmm(res_part_order ~ 1 + body_size*fi_species+mean_depth+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T)
m1<-clmm(res_part_order ~ 1 + (1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T)
m9<-clmm(res_part_order ~ 1 + mean_depth+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T)
m5<-clmm(res_part_order ~ 1 + body_size+fi_species+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T)
m11<-clmm(res_part_order ~ 1 + mean_depth+fi_species+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = spring_I, link="logit",Hess=T)
```
```
lrtest(m4,m1)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + body_size * fi_species + mean_depth + (1 |
    fi_species) + (1 + mean_depth | fi_species:fi_fishid)
Model 2: res_part_order ~ 1 + (1 | fi_species) + (1 + mean_depth | fi_species:fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  13 -1116.3
2   7 -1122.6 -6 12.729    0.04754 *
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
```
lrtest(m4,m9)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + body_size * fi_species + mean_depth + (1 |
    fi_species) + (1 + mean_depth | fi_species:fi_fishid)
Model 2: res_part_order ~ 1 + mean_depth + (1 | fi_species) + (1 + mean_depth |
    fi_species:fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  13 -1116.3
2   8 -1122.2 -5 11.858    0.03678 *
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
```
lrtest(m4,m5)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + body_size * fi_species + mean_depth + (1 |
    fi_species) + (1 + mean_depth | fi_species:fi_fishid)
Model 2: res_part_order ~ 1 + body_size + fi_species + (1 | fi_species) +
    (1 + mean_depth | fi_species:fi_fishid)
  #Df  LogLik Df Chisq Pr(>Chisq)
1  13 -1116.3
2  10 -1120.2 -3 7.939    0.04729 *
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
```
lrtest(m4,m11)              
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + body_size * fi_species + mean_depth + (1 |
    fi_species) + (1 + mean_depth | fi_species:fi_fishid)
Model 2: res_part_order ~ 1 + mean_depth + fi_species + (1 | fi_species) +
    (1 + mean_depth | fi_species:fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  13 -1116.3
2  10 -1120.5 -3 8.5484    0.03594 *
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
- m4 is the absolute best model
```
plot(allEffects(m4,xlevels=list(body_size=seq(405,1660,length=1),fi_species=c("pike","pikeperch","wels"))), rug = FALSE, style = "stacked", ylab="Response probability",colors = c("white", "grey", "red","green"))
```
![Res_part_use](/Plots/Res_part_use_103.png "Res_part_use")

How the probability of use certain reservoir parts change according to changes in daily horizontal movement?
Fit two additional models with different random-effects structure
```
m_id_sp_inter<-clmm(res_part_order ~ 1 + body_size*fi_species+(1| fi_species) + (1 | fi_fishid),data = spring_I, link="logit",Hess=T)
```
```
plot(allEffects(m41,xlevels=list(body_size=seq(405,1660,length=1),fi_species=c("pike","pikeperch","wels"))), rug = FALSE, style = "stacked", ylab="Response probability",colors = c("white", "grey", "red","green"))
```
![Res_part_use](/Plots/Res_part_use_104.png "Res_part_use")
```
m_range_slope<-clmm(res_part_order ~ 1 + body_size*fi_species+(1 + ranged2d | fi_fishid),data = spring_I, link="logit",Hess=T)
```
```
plot(allEffects(m42,xlevels=list(body_size=seq(405,1660,length=1),fi_species=c("pike","pikeperch","wels"))), rug = FALSE, style = "stacked", ylab="Response probability",colors = c("white", "grey", "red","green"))
```
![Res_part_use](/Plots/Res_part_use_105.png "Res_part_use")

_pikeperch_ show increased use of dam with larger body size. However, including the slope of _ranged2d_, the probability of using the tributary part increases with body size. This means that the variability in horizontal movement allows for larger travels independently of body size 

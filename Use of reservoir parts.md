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
Fit models using the subseted dataset to explore which RF fits the data better
```
m_id<-clmm(res_part_order ~ 1  + (1| fi_fishid),data = data_poglm_sub, link="logit",Hess=T)
m_id_sp<-clmm(res_part_order ~ 1  + (1| fi_species) + (1| fi_fishid),data = data_poglm_sub, link="logit",Hess=T)
m_id_season<-clmm(res_part_order ~ 1  + (1| season) + (1| fi_fishid),data = data_poglm_sub, link="logit",Hess=T)
m_id_sp_season<-clmm(res_part_order ~ 1  + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T)
```
Order models from best to worst fitting in terms of Akaike values (i.e., BIC)

:books:`library(dLagM)`

```
bic = BIC(m_id,m_id_sp,m_id_season,m_id_sp_season)
sortScore(bic , score = "bic")
```
```
                df   BIC
m_id_season     5 12153.67
m_id_sp_season  6 12162.26
m_id            4 12226.89
m_id_sp         5 12235.47
```
The four models are very close in term of BIC values but we keep Model **m_id_sp_season** for further analysis of full models

### 2. Fit a series of conditional _POGLMMs_ (incl. covariates) with the RFs structure of selected model (m4)

Build a candidate models set and perform model selection based on BIC
```
Cand.mod<-matrix(ncol=1,nrow=13)
colnames(Cand.mod)<-c("BIC")
rownames(Cand.mod)<-c(1:13)

Cand.mod[[1]]<-AIC(clmm(res_part_order ~ 1 + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[2]]<-AIC(clmm(res_part_order ~ 1 + body_size + fi_species + season + (1| fi_species) + (1| fi_fishid) + (1| season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[3]]<-AIC(clmm(res_part_order ~ 1 + body_size + fi_species * season + (1| fi_species) + (1| fi_fishid) + (1| season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[4]]<-AIC(clmm(res_part_order ~ 1 + body_size * fi_species + season + (1| fi_species) + (1| fi_fishid) + (1| season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[5]]<-AIC(clmm(res_part_order ~ 1 + body_size + fi_species + (1| fi_species) + (1| fi_fishid) + (1| season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[6]]<-AIC(clmm(res_part_order ~ 1 + body_size + season + (1| fi_species) + (1| fi_fishid) + (1| season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[7]]<-AIC(clmm(res_part_order ~ 1 + fi_species + season + (1| fi_species) + (1| fi_fishid) + (1| season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[8]]<-AIC(clmm(res_part_order ~ 1 + body_size * fi_species + (1| fi_species) + (1| fi_fishid) + (1| season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[9]]<-AIC(clmm(res_part_order ~ 1 + body_size * season + (1| fi_species) + (1| fi_fishid) + (1| season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[10]]<-AIC(clmm(res_part_order ~ 1 + fi_species * season + (1| fi_species) + (1| fi_fishid) + (1| season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[11]]<-AIC(clmm(res_part_order ~ 1 + body_size + (1| fi_species) + (1| fi_fishid) + (1| season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[12]]<-AIC(clmm(res_part_order ~ 1 + season + (1| fi_species) + (1| fi_fishid) + (1| season),data = data_poglm_sub, link="logit",Hess=T))
Cand.mod[[13]]<-AIC(clmm(res_part_order ~ 1 + fi_species + (1| fi_species) + (1| fi_fishid) + (1| season),data = data_poglm_sub, link="logit",Hess=T))

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
12    12 12113.77
6      6 12115.47
7      7 12117.45
2      2 12118.31
4      4 12120.86
1      1 12122.72
11    11 12124.43
13    13 12126.40
5      5 12127.27
8      8 12129.83
```
Rename and order models from best to worst fitting in terms of Akaike values
```
m10<-clmm(res_part_order ~ 1 + fi_species * season + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T)
m3<-clmm(res_part_order ~ 1 + body_size + fi_species * season + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T)
m9<-clmm(res_part_order ~ 1 + body_size * season + (1| fi_species) + (1| fi_fishid) + (1 | season),data = data_poglm_sub, link="logit",Hess=T)
```
Perform a LRT comparison between the first best-fit Model (**m10**) and subsequent best-fit models **Model 3** and **Model 9**. We drop **Model 6**  and forward as their difference with the first best model (i.e., DeltaBIC) are above two higher units
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
The LRT is not significant indicating that the reduced **Model 10** is preferred to the expanded **Model 3**, i.e., the addition of body_size does not significantly to explaining the variability in the use of reservoir parts
On the other hand, **Model 10** and **Model 9** can not be compared using LRT as they do not have nested fixed-effects. We pick the one with higher log-Likelihood and lower BIC
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

The worst fit-model, **m8** may help us to see the relationship of the use of reservoir parts by each species

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


## Model fit by season: SPRING_I

Subset data for season Spring_I
```
spring_I <- subset(data_poglm_sub,season=="spring_I")
```
Model selection. Note that now we use the RF structure of Model **m_id_sp** instead of **m_id_sp_season**
```
m1<-clmm(res_part_order ~ 1 + (1| fi_species) + (1| fi_fishid),data = spring_I, link="logit",Hess=T)
m2<-clmm(res_part_order ~ 1 + body_size + fi_species + (1| fi_species) + (1| fi_fishid),data = spring_I, link="logit",Hess=T)
m3<-clmm(res_part_order ~ 1 + body_size * fi_species + (1| fi_species) + (1| fi_fishid),data = spring_I, link="logit",Hess=T)
m4<-clmm(res_part_order ~ 1 + body_size + (1| fi_species) + (1| fi_fishid),data = spring_I, link="logit",Hess=T)
m5<-clmm(res_part_order ~ 1 + fi_species + (1| fi_species) + (1| fi_fishid),data = spring_I, link="logit",Hess=T)
```
```
bic = BIC(m1,m2,m3,m4,m5)
sortScore(bic , score = "bic")
```
```
   df      BIC
m1  5 2396.650
m4  6 2403.566
m5  7 2407.895
m2  8 2414.831
m3 10 2423.235
```
LRT comparisons
```
lrtest(m1,m2)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + (1 | fi_species) + (1 | fi_fishid)
Model 2: res_part_order ~ 1 + body_size + fi_species + (1 | fi_species) +
    (1 | fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   5 -1181.0
2   8 -1179.7  3 2.6397     0.4506
```
```
lrtest(m1,m3)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + (1 | fi_species) + (1 | fi_fishid)
Model 2: res_part_order ~ 1 + body_size * fi_species + (1 | fi_species) +
    (1 | fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   5 -1181.0
2  10 -1176.9  5 8.1165     0.1499
```
```
lrtest(m2,m3)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + body_size + fi_species + (1 | fi_species) +
    (1 | fi_fishid)
Model 2: res_part_order ~ 1 + body_size * fi_species + (1 | fi_species) +
    (1 | fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   8 -1179.7
2  10 -1176.9  2 5.4767    0.06468 .
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
```
lrtest(m4,m3)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + body_size + (1 | fi_species) + (1 | fi_fishid)
Model 2: res_part_order ~ 1 + body_size * fi_species + (1 | fi_species) +
    (1 | fi_fishid)
  #Df  LogLik Df Chisq Pr(>Chisq)
1   6 -1181.0
2  10 -1176.9  4 8.092    0.08826 .
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
```
lrtest(m5,m3)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + fi_species + (1 | fi_species) + (1 | fi_fishid)
Model 2: res_part_order ~ 1 + body_size * fi_species + (1 | fi_species) +
    (1 | fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   7 -1179.7
2  10 -1176.9  3 5.4812     0.1398
```
These results show that a high proportion of the variability in the use of reserovir is random as denoted by Model 1.
However, we see that the comparisons between Model 3 and Models 2 and 4 are marginally significant indicating that the inclusion of the interaction tends to improve the model fit over models without it.
Since we are interested in the interaction term, we finally keep Model 3.
```
summary(m3)
```
```
Cumulative Link Mixed Model fitted with the Laplace approximation

formula: res_part_order ~ 1 + body_size * fi_species + (1 | fi_species) + (1 | fi_fishid)
data:    spring_I

 link  threshold nobs logLik   AIC     niter     max.grad cond.H
 logit flexible  1033 -1176.92 2373.83 970(2912) 8.10e-03 3.4e+09

Random effects:
 Groups     Name        Variance Std.Dev.
 fi_fishid  (Intercept) 0.587    0.7662
 fi_species (Intercept) 0.000    0.0000
Number of groups:  fi_fishid 13,  fi_species 3

Coefficients:
                               Estimate Std. Error z value Pr(>|z|)
body_size                      0.011224   0.004587   2.447  0.01440 *
fi_speciespikeperch           14.475429   5.525490   2.620  0.00880 **
fi_specieswels                14.172077   5.108375   2.774  0.00553 **
body_size:fi_speciespikeperch -0.014970   0.007392  -2.025  0.04285 *
body_size:fi_specieswels      -0.012109   0.004783  -2.532  0.01136 *
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Threshold coefficients:
    Estimate Std. Error z value
0|1    9.337      4.789   1.950
1|3   11.988      4.803   2.496
3|2   13.814      4.807   2.874
```
```
plot(allEffects(m3,xlevels=list(res_part_order=seq(0,3,length=2))),rug = FALSE)
```
![Res_part_use](/Plots/Res_part_use_41.png "Res_part_use")
```
plot(allEffects(m3,xlevels=list(fi_species=c("pike","pikeperch","wels"))), rug = FALSE, style = "stacked",col=cm.colors(5))
```
![Res_part_use](/Plots/Res_part_use_42.png "Res_part_use")

Now, we account for the distance range to explore any relationship between this variable and the likelihood of using specific parts of the reservoir
```
m1<-clmm(res_part_order ~ 1 + (1| fi_species) + (1| fi_fishid),data = spring_I, link="logit",Hess=T)
m2<-clmm(res_part_order ~ 1 + body_size + fi_species + (1| fi_species) + (1| fi_fishid),data = spring_I, link="logit",Hess=T)
m3<-clmm(res_part_order ~ 1 + body_size * fi_species + (1| fi_species) + (1| fi_fishid),data = spring_I, link="logit",Hess=T)
m4<-clmm(res_part_order ~ 1 + body_size + (1| fi_species) + (1| fi_fishid),data = spring_I, link="logit",Hess=T)
m5<-clmm(res_part_order ~ 1 + fi_species + (1| fi_species) + (1| fi_fishid),data = spring_I, link="logit",Hess=T)
m6<-clmm(res_part_order ~ 1 + ranged2d + (1| fi_species) + (1| fi_fishid),data = spring_I, link="logit",Hess=T)
m7<-clmm(res_part_order ~ 1 + body_size + ranged2d + (1| fi_species) + (1| fi_fishid),data = spring_I, link="logit",Hess=T)
m8<-clmm(res_part_order ~ 1 + body_size * ranged2d + (1| fi_species) + (1| fi_fishid),data = spring_I, link="logit",Hess=T)
m9<-clmm(res_part_order ~ 1 + fi_species * ranged2d + (1| fi_species) + (1| fi_fishid),data = spring_I, link="logit",Hess=T)
m10<-clmm(res_part_order ~ 1 + fi_species + ranged2d + (1| fi_species) + (1| fi_fishid),data = spring_I, link="logit",Hess=T)
```
```
bic = BIC(m1,m2,m3,m4,m5,m6,m7,m8,m9,m10)
sortScore(bic , score = "bic")
```
```
 df       BIC
m1   5  2396.650
m6   6  2403.437
m4   6  2403.566
m5   7  2407.895
m7   7  2410.355
m9  10  2414.460
m10  8  2414.661
m2   8  2414.831
m3  10  2423.235
m8  11 12202.318
```
Again, the null itercept model is the first best model. The variable **ranged2d* is only significant in the presence of an interaction so we focus on Model 9, which is the only relevant for our hypothesis testing
If we want to be certain as to wether **Model 9** explains well the variability in the use of reservoir, check the resulta of the two-by-two LRT comparisons
```
lrtest(m1,m9)
```
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + (1 | fi_species) + (1 | fi_fishid)
Model 2: res_part_order ~ 1 + fi_species * ranged2d + (1 | fi_species) +
    (1 | fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   5 -1181.0
2  10 -1172.5  5 16.892   0.004709 **
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
```
lrtest(m2,m9)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + body_size + fi_species + (1 | fi_species) +
    (1 | fi_fishid)
Model 2: res_part_order ~ 1 + fi_species * ranged2d + (1 | fi_species) +
    (1 | fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   8 -1179.7
2  10 -1172.5  2 14.252  0.0008039 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
```
lrtest(m3,m9)
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + body_size * fi_species + (1 | fi_species) +
    (1 | fi_fishid)
Model 2: res_part_order ~ 1 + fi_species * ranged2d + (1 | fi_species) +
    (1 | fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  10 -1176.9
2  10 -1172.5  0 8.7754  < 2.2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
```
lrtest(m4,m9)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + body_size + (1 | fi_species) + (1 | fi_fishid)
Model 2: res_part_order ~ 1 + fi_species * ranged2d + (1 | fi_species) +
    (1 | fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   6 -1181.0
2  10 -1172.5  4 16.867   0.002051 **
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
```
lrtest(m5,m9)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + fi_species + (1 | fi_species) + (1 | fi_fishid)
Model 2: res_part_order ~ 1 + fi_species * ranged2d + (1 | fi_species) +
    (1 | fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   7 -1179.7
2  10 -1172.5  3 14.257   0.002576 **
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
```
lrtest(m6,m9)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + ranged2d + (1 | fi_species) + (1 | fi_fishid)
Model 2: res_part_order ~ 1 + fi_species * ranged2d + (1 | fi_species) +
    (1 | fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   6 -1180.9
2  10 -1172.5  4 16.738   0.002173 **
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
```
lrtest(m7,m9)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + body_size + ranged2d + (1 | fi_species) +
    (1 | fi_fishid)
Model 2: res_part_order ~ 1 + fi_species * ranged2d + (1 | fi_species) +
    (1 | fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   7 -1180.9
2  10 -1172.5  3 16.716  0.0008086 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
We see that the interaction **Model 9** is preferred over any of the reduced forms
```
summary(m9)
```
```
Cumulative Link Mixed Model fitted with the Laplace approximation

formula: res_part_order ~ 1 + fi_species * ranged2d + (1 | fi_species) +      (1 | fi_fishid)
data:    spring_I

 link  threshold nobs logLik   AIC     niter     max.grad cond.H
 logit flexible  1033 -1172.53 2365.06 840(2518) 7.24e+01 1.7e+09

Random effects:
 Groups     Name        Variance Std.Dev.
 fi_fishid  (Intercept) 0.9834   0.9917
 fi_species (Intercept) 0.0000   0.0000
Number of groups:  fi_fishid 13,  fi_species 3

Coefficients:
                               Estimate Std. Error z value Pr(>|z|)
fi_speciespikeperch           4.767e-01  8.485e-01   0.562 0.574268
fi_specieswels                1.287e+00  8.674e-01   1.484 0.137878
ranged2d                     -1.522e-04  9.947e-05  -1.531 0.125882
fi_speciespikeperch:ranged2d  4.508e-04  1.351e-04   3.337 0.000847 ***
fi_specieswels:ranged2d       8.666e-05  1.207e-04   0.718 0.472822
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Threshold coefficients:
    Estimate Std. Error z value
0|1  -2.5798     0.7415  -3.479
1|3   0.1111     0.7339   0.151
3|2   1.9550     0.7354   2.659
```
The distance range is significant in pikeperch as we can see in the following plot
```
plot(allEffects(m9,xlevels=list(fi_species=c("pike","pikeperch","wels"))), rug = FALSE, style = "stacked",col=cm.colors(5))
```
![Res_part_use](/Plots/Res_part_use_5.png "Res_part_use")




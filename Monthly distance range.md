# How does distance range change monthly?

## Create a dataframe including monthly range and reservoir parts

:books:`library(plyr)`

To do this, create a new monthly range dataframe (see page "Common code") but this time including reservoir parts (the first three steps are the same as in previous code)
```
mean.ranged2d[, mon_yr := paste(month(date),"_",year(date), sep = "")]
dist.range.month <- mean.ranged2d[, .(dist.range = max(meand2d)-min(meand2d), day_count = length(date)), by = .(fi_fishid, mon_yr, month)]
dist.range.month <- merge(dist.range.month, fish.info, by = c("fi_fishid"))
dist.range.month$mon_yr <- factor(dist.range.month$mon_yr, levels=c("4_2017","5_2017","6_2017","7_2017","8_2017","9_2017","10_2017","11_2017", "12_2017","1_2018","2_2018","3_2018","4_2018"))
data_total_res<- merge(mean.ranged2d, data.res.parts, by = c("fi_fishid", "date"))
data_total_res<- dcast(setDT(data_total_res), fi_fishid+mon_yr~res_part, length)
data_distr<- merge(dist.range.month, data_total_res, by = c("fi_fishid","mon_yr"))
```

Delete months with < 20 day counts (i.e., create a new dataframe filtering by _day_count_ >= 20)
```
data_distr<-data_distr[data_distr$day_count >= 20, ]
```
Rename some variables
```
colnames(data_distr)[6] <- "body_size"
colnames(data_distr)[3] <- "mon"
colnames(data_distr)[2] <- "month"
colnames(data_distr)[9] <- "Species"
```
### Convert variables

Convert _month_ into a time vector of repeated-measures and code it as integer to allow correct ordering (from months 10-12 2018). In addition, we will include this time variable both as random-effects and fixed-effects and given its number of time points vector it should be coded as integer
```
data_distr$month<- revalue(data_distr$month, c("5_2017"="0","6_2017"="1","7_2017"="2","8_2017"="3","9_2017"="4","10_2017"="5","11_2017"="6","12_2017"="7","1_2018"="8","2_2018"="9","3_2018"="10"))
```
```
data_distr$fi_fishid <- as.factor(data_distr$fi_fishid)
data_distr$Species <- as.factor(data_distr$Species)
data_distr$month <- as.integer(data_distr$month)
```
Order by _fi_fishid_ and _month_ and drop missing variables
```
data_distr<- data_distr[with(data_distr, order(fi_fishid, month)),]
data_distr
```
| fi_fishid | Species | month | dist.range | dam | middle | tributary | upper | body_size | ca_weight_g | day_count |
|-----------|------------|------|------------|-----|--------|-----------|-------|-----------|-------------|-----------|
| T449202_1 | pikeperch  | 0    | 349.268    | 0   | 4      | 0         | 3     | 430       | 605         | 4         |
| T449202_1 | pikeperch  | 1    | 4878.4207  | 0   | 20     | 13        | 21    | 430       | 605         | 31        |
| T449202_1 | pikeperch  | 2    | 1383.4471  | 0   | 0      | 29        | 4     | 430       | 605         | 29        |
| T449202_1 | pikeperch  | 3    | 946.5576   | 0   | 0      | 31        | 0     | 430       | 605         | 31        |
| T449203_1 | pike       | 0    | 1862.7459  | 1   | 3      | 4         | 4     | 1160      | 10900       | 4         |
| T449203_1 | pike       | 1    | 5877.5175  | 1   | 18     | 19        | 25    | 1160      | 10900       | 31        |
| T449203_1 | pike       | 2    | 4247.3792  | 1   | 6      | 24        | 6     | 1160      | 10900       | 26        |
| T449203_1 | pike       | 3    | 2562.5952  | 0   | 1      | 20        | 7     | 1160      | 10900       | 21        |
| T449268_1 | wels       | 0    | 877.93632  | 0   | 4      | 0         | 4     | 1620      | 25100       | 4         |
| T449268_1 | wels       | 1    | 2348.48336 | 0   | 22     | 0         | 27    | 1620      | 25100       | 30        |
| T449268_1 | wels       | 2    | 1980.08735 | 0   | 29     | 0         | 13    | 1620      | 25100       | 29        |
| T449268_1 | wels       | 3    | 3025.401   | 0   | 23     | 0         | 17    | 1620      | 25100       | 31        |

Summary of the variable _dist_range_
```
ddply(data_distr,.(Species),summarize,mean=mean(dist.range),sd=sd(dist.range),nobs=length(unique(fi_fishid)))
```
```
  Species     mean       sd nobs
1       pike 2001.874 1759.052   10
2  pikeperch 1757.787 1598.057    7
3       wels 1781.509 1618.366   14
```
```
hist(data_distr$dist.range, breaks = 20)
```
![Dist_range](/Plots/Dist_range_month_hist.png "Dist_range")

Check for correlations between _distance_range_ and reservoir parts

:books:`library(corrplot)`
```
corr<-cor(data_distr[,c(4,10,11,12,13)], use="pairwise", method="spearman")
corrplot(corr,method="number")
```
![Dist_range](/Plots/Dist_range_corr.png "Dist_range")

## Fit Mixed-Effects Models (LMM) to the data of monthly distance range and use of reservoir parts

### 1. Find the best unconditional model fitted via REML

#### 1.1. Fit nested intercept-only models including all potentially relevant random-effects and compare them with LRT

```
m_id<-lmer(dist.range ~ 1  + (1| fi_fishid),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_sp<-lmer(dist.range ~ 1  + (1| Species) + (1| fi_fishid),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_mon<-lmer(dist.range ~ 1  + (1| month) + (1| fi_fishid),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_sp_mon<-lmer(dist.range ~ 1  + (1| Species) + (1| month),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_sp_mon<-lmer(dist.range ~ 1  + (1| month) + (1| fi_fishid) + (1| Species),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
lrtest(m_id,m_id_sp)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + (1 | fi_fishid)
Model 2: dist.range ~ 1 + (1 | Species) + (1 | fi_fishid)
  #Df LogLik Df Chisq Pr(>Chisq)
1   3  -2522
2   4  -2522  1     0     0.9999
```
```
lrtest(m_id,m_id_mon)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + (1 | fi_fishid)
Model 2: dist.range ~ 1 + (1 | month) + (1 | fi_fishid)
  #Df  LogLik Df Chisq Pr(>Chisq)
1   3 -2522.0
2   4 -2511.8  1 20.27  6.723e-06 ***
```

```
lrtest(m_id_mon,m_id_sp_mon)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + (1 | month) + (1 | fi_fishid)
Model 2: dist.range ~ 1 + (1 | month) + (1 | fi_fishid) + (1 | Species)
  #Df  LogLik Df Chisq Pr(>Chisq)
1   4 -2511.8
2   5 -2511.8  1     0     0.9999
```
```
anova(m_id_mon,m_sp_mon)
0.5*(1 - pchisq(0, 1)) + 0.5*(1 - pchisq(0, 2))
```

**Model m_id_mon** is preferred

#### 1.2. Fit nested slope models including all potentially relevant random-effects and compare them with LRT.

```
m_sl_1<-lmer(dist.range ~ 1  + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_sl_2<-lmer(dist.range ~ 1  + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
lrtest(m_sl_1,m_sl_2)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + (1 + month | Species:fi_fishid) + (1 + month |
    Species)
Model 2: dist.range ~ 1 + (1 + month | Species:fi_fishid) + (1 + month |
    Species) + (1 | month)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   8 -2509.2
2   9 -2498.8  1 20.731  5.284e-06 ***
```
Model **m_sl_2** is preferred

#### 1.3. Compare the best intercept and slope models

```
anova(m_sl_2,m_id_mon)
```
```
Models:
m_id_mon: dist.range ~ 1 + (1 | month) + (1 | fi_fishid)
m_sl_2: dist.range ~ 1 + (month | Species:fi_fishid) + (month | Species) +
m_sl_2:     (1 | month)
         Df    AIC    BIC  logLik deviance  Chisq Chi Df Pr(>Chisq)
m_id_mon  4 5044.4 5059.1 -2518.2   5036.4
m_sl_2    9 5028.5 5061.4 -2505.2   5010.5 25.975      5  9.023e-05 ***
```
- The Chi-square test p < 0.05 indicating there is significant evidence in support of the larger model **m_sl_2** (lower BIC and hiher logLik)

### 2. Find the best conditional LMMs fitted via ML including the RF structure of the previously selected unconditional model

:books:`library(AICcmodavg)`

Build a set of candidate models fitted with setting **REML=FALSE**

```
Cand.mod <- list()

Cand.mod[[1]] <- lmer(dist.range ~ 1 + body_size + month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[2]] <- lmer(dist.range ~ 1 + body_size * month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[3]] <- lmer(dist.range ~ 1 + Species + month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[4]] <- lmer(dist.range ~ 1 + Species * month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[5]] <- lmer(dist.range ~ 1 + body_size + Species + month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[6]] <- lmer(dist.range ~ 1 + body_size + Species * month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[7]] <- lmer(dist.range ~ 1 + body_size * Species + month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[8]] <- lmer(dist.range ~ 1 + body_size * Species * month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[9]] <- lmer(dist.range ~ 1 + body_size * Species + Species * month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[10]] <- lmer(dist.range ~ 1 + body_size + month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[11]] <- lmer(dist.range ~ 1 + body_size * month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[12]] <- lmer(dist.range ~ 1 + Species + month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[13]] <- lmer(dist.range ~ 1 + Species * month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[14]] <- lmer(dist.range ~ 1 + body_size + Species + month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[15]] <- lmer(dist.range ~ 1 + body_size + Species * month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[16]] <- lmer(dist.range ~ 1 + body_size * Species + month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[17]] <- lmer(dist.range ~ 1 + body_size * Species * month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[18]] <- lmer(dist.range ~ 1 + body_size * Species + Species * month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
Name models
```
Modnames <- c("Model 1", "Model 2", "Model 3", "Model 4", "Model 5",
              "Model 6", "Model 7", "Model 8","Model 9", "Model 10",
              "Model 11", "Model 12", "Model 13","Model 14",
              "Model 15", "Model 16", "Model 17", "Model 18")
```
Create a model selection table based on AICc, better than AIC/BIC with small samples
```
aictab(cand.set = Cand.mod, modnames = Modnames)
```
```
Model selection based on AICc:

          K    AICc Delta_AICc AICcWt Cum.Wt       LL
Model 18 18 5015.86       0.00   0.76   0.76 -2488.65
Model 16 16 5018.83       2.97   0.17   0.93 -2492.41
Model 17 21 5022.19       6.33   0.03   0.96 -2488.35
Model 9  17 5022.46       6.61   0.03   0.99 -2493.09
Model 15 16 5027.94      12.09   0.00   0.99 -2496.96
Model 8  20 5028.79      12.93   0.00   1.00 -2492.82
Model 7  15 5028.81      12.95   0.00   1.00 -2498.52
Model 13 15 5028.98      13.12   0.00   1.00 -2498.60
Model 10 12 5029.35      13.49   0.00   1.00 -2502.10
Model 14 14 5031.27      15.41   0.00   1.00 -2500.86
Model 11 13 5031.40      15.54   0.00   1.00 -2502.03
Model 6  15 5031.63      15.77   0.00   1.00 -2499.93
Model 4  14 5031.92      16.06   0.00   1.00 -2501.19
Model 12 13 5032.28      16.42   0.00   1.00 -2502.47
Model 1  11 5032.68      16.82   0.00   1.00 -2504.86
Model 2  12 5034.72      18.87   0.00   1.00 -2504.79
Model 5  13 5035.14      19.28   0.00   1.00 -2503.90
Model 3  12 5035.39      19.53   0.00   1.00 -2505.12
```
Compute the evidence ratio
```
evidence(aictab(cand.set = Cand.mod, modnames = Modnames))
```
```
Evidence ratio between models 'Model 18' and 'Model 16': 4.42
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
Model 18 18 5015.86       0.00   0.76
Model 16 16 5018.83       2.97   0.17
Model 17 21 5022.19       6.33   0.03

Model probabilities sum to 0.96
```
Compute importance value for _day_count_ (same number of models), i.e., models with and models without the variable
```
importance(cand.set = Cand.mod, modnames = Modnames, parm = "day_count")
```
```
Importance values of 'day_count':

w+ (models including parameter): 0.96
w- (models excluding parameter): 0.04
```
Compute model-averaged estimate of _day_count_ using the natural average
```
modavg(cand.set = Cand.mod, modnames = Modnames, parm = "day_count")
```
```
Multimodel inference on "day_count" based on AICc

AICc table used to obtain model-averaged estimate:

          K    AICc Delta_AICc AICcWt Estimate    SE
Model 10 12 5029.35      13.49   0.00   137.41 57.57
Model 11 13 5031.40      15.54   0.00   137.49 57.56
Model 12 13 5032.28      16.42   0.00   134.97 57.77
Model 13 15 5028.98      13.12   0.00   132.22 57.34
Model 14 14 5031.27      15.41   0.00   144.20 57.80
Model 15 16 5027.94      12.09   0.00   141.29 57.36
Model 16 16 5018.83       2.97   0.18   177.39 57.09
Model 17 21 5022.19       6.33   0.03   174.33 56.88
Model 18 18 5015.86       0.00   0.78   173.26 56.68

Model-averaged estimate: 173.86
Unconditional SE: 56.84
95% Unconditional confidence interval: 62.45, 285.27
```
From these results we can see that _day_count_ is an important variable controlling for the varying sampling days per month. In addition, it is significant in best-fit models (see model summary below)

### 3. Compare best-fit models of the confidence set

We compare the first two best models (within 3 Delta_AICc) and the third one (even if is already much above this treshold)

```
m18<-lmer(dist.range ~ 1 + body_size * Species + Species * month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m16<-lmer(dist.range ~ 1 + body_size * Species + month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m17<-lmer(dist.range ~ 1 + body_size * Species * month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
lrtest(m18,m16)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size * Species + Species * month + day_count +
    (1 + month | Species:fi_fishid) + (1 + month | Species) +
    (1 | month)
Model 2: dist.range ~ 1 + body_size * Species + month + day_count + (1 +
    month | Species:fi_fishid) + (1 + month | Species) + (1 |
    month)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  18 -2488.7
2  16 -2492.4 -2 7.5108    0.02339 *
```
```
lrtest(m18,m17)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size * Species + Species * month + day_count +
    (1 + month | Species:fi_fishid) + (1 + month | Species) +
    (1 | month)
Model 2: dist.range ~ 1 + body_size * Species * month + day_count + (1 +
    month | Species:fi_fishid) + (1 + month | Species) + (1 |
    month)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  18 -2488.7
2  21 -2488.3  3 0.6038     0.8956
```
- **Model 18** is preferred to both reduced **Model 16** and larger **Model 17**. All models included _day_count_ as a covariate to control for possible effects of the varying number of days per month

Export table from confidence set models

:books:`library(sjPlot)`
:books:`library(sjstats)`
:books:`library(sjmisc)`
```
tab_model(m18,m16,m17, transform = NULL, collapse.ci = F,  auto.label = FALSE,  show.se = TRUE,collapse.se = T,
              dv.labels = c("Model 1", "Model 2","Model 3"),
               string.pred = "Variable",
               string.p = "P" ,file = "Season_dist.range.doc", use.viewer = TRUE)
```
```
|  Model 1                         	|                                  	|                      	| Model 2 	|                                  	|                      	| Model 3 	|                                  	|                      	| ca_weight_g 	| day_count 	|
|----------------------------------	|----------------------------------	|----------------------	|---------	|----------------------------------	|----------------------	|---------	|----------------------------------	|----------------------	|-------------	|-----------	|
| Variable                         	| Estimates                        	| CI                   	| P       	| Estimates                        	| CI                   	| P       	| Estimates                        	| CI                   	| P           	| 4         	|
| (Intercept)                      	| -7750.94 (1978.23)               	| -11628.20 – -3873.68 	| <0.001  	| -6728.89 (2036.82)               	| -10720.97 – -2736.80 	| 0.001   	| -7681.40 (2429.12)               	| -12442.39 – -2920.42 	| 0.002       	| 31        	|
| body_size                        	| 5.70 (1.06)                      	| 3.63 – 7.78          	| <0.001  	| 5.75 (1.06)                      	| 3.67 – 7.82          	| <0.001  	| 5.57 (2.17)                      	| 1.32 – 9.81          	| 0.010       	| 29        	|
| Speciespikeperch                 	| 7683.79 (2069.83)                	| 3627.00 – 11740.59   	| <0.001  	| 6042.11 (2041.50)                	| 2040.85 – 10043.38   	| 0.003   	| 5266.08 (3958.04)                	| -2491.54 – 13023.71  	| 0.183       	| 31        	|
| Specieswels                      	| 5181.77 (1193.44)                	| 2842.66 – 7520.88    	| <0.001  	| 3450.42 (1112.43)                	| 1270.11 – 5630.74    	| 0.002   	| 5338.47 (2250.09)                	| 928.38 – 9748.57     	| 0.018       	| 4         	|
| month                            	| 182.71 (79.62)                   	| 26.66 – 338.77       	| 0.022   	| -30.44 (103.63)                  	| -233.55 – 172.67     	| 0.769   	| 167.15 (238.47)                  	| -300.24 – 634.54     	| 0.483       	| 31        	|
| day_count                        	| 173.24 (56.68)                   	| 62.15 – 284.34       	| 0.002   	| 177.42 (57.09)                   	| 65.53 – 289.30       	| 0.002   	| 174.30 (56.88)                   	| 62.81 – 285.79       	| 0.002       	| 26        	|
| body_size:Speciespikeperch       	| -10.83 (3.98)                    	| -18.64 – -3.02       	| 0.007   	| -11.01 (3.98)                    	| -18.81 – -3.22       	| 0.006   	| -5.90 (7.77)                     	| -21.13 – 9.33        	| 0.448       	| 21        	|
| body_size:Specieswels            	| -5.65 (1.24)                     	| -8.09 – -3.22        	| <0.001  	| -5.71 (1.24)                     	| -8.14 – -3.28        	| <0.001  	| -5.74 (2.52)                     	| -10.67 – -0.80       	| 0.023       	| 4         	|
| Speciespikeperch:month           	| -315.04 (96.06)                  	| -503.33 – -126.76    	| 0.001   	|                                  	|                      	|         	| 111.25 (592.39)                  	| -1049.80 – 1272.31   	| 0.851       	| 30        	|
| Specieswels:month                	| -321.74 (80.08)                  	| -478.70 – -164.79    	| <0.001  	|                                  	|                      	|         	| -351.47 (329.35)                 	| -996.98 – 294.04     	| 0.286       	| 29        	|
| body_size:month                  	|                                  	|                      	|         	|                                  	|                      	|         	| 0.02 (0.31)                      	| -0.59 – 0.63         	| 0.947       	| 31        	|
| body_size:Speciespikeperch:month 	|                                  	|                      	|         	|                                  	|                      	|         	| -0.86 (1.15)                     	| -3.12 – 1.40         	| 0.454       	|           	|
| body_size:Specieswels:month      	|                                  	|                      	|         	|                                  	|                      	|         	| 0.02 (0.36)                      	| -0.70 – 0.73         	| 0.960       	|           	|
| Random Effects                   	|                                  	|                      	|         	|                                  	|                      	|         	|                                  	|                      	|             	|           	|
| σ2                               	| 1629335.64                       	|                      	|         	| 1630228.77                       	|                      	|         	| 1633185.76                       	|                      	|             	|           	|
| τ00                              	| 943121.76 Species:fi_fishid      	|                      	|         	| 1036304.05 Species:fi_fishid     	|                      	|         	| 920726.04 Species:fi_fishid      	|                      	|             	|           	|
|                                  	| 256379.87 month                  	|                      	|         	| 276546.32 month                  	|                      	|         	| 258631.13 month                  	|                      	|             	|           	|
|                                  	| 0.16 Species                     	|                      	|         	| 616597.26 Species                	|                      	|         	| 0.00 Species                     	|                      	|             	|           	|
| τ11                              	| 14672.15 Species:fi_fishid.month 	|                      	|         	| 17400.34 Species:fi_fishid.month 	|                      	|         	| 13646.06 Species:fi_fishid.month 	|                      	|             	|           	|
|                                  	| 0.00 Species.month               	|                      	|         	| 20443.57 Species.month           	|                      	|         	| 0.01 Species.month               	|                      	|             	|           	|
| ρ01                              	| -0.92 Species:fi_fishid          	|                      	|         	| -0.93 Species:fi_fishid          	|                      	|         	| -0.93 Species:fi_fishid          	|                      	|             	|           	|
|                                  	| -0.10 Species                    	|                      	|         	| -1.00 Species                    	|                      	|         	| -1.00 Species                    	|                      	|             	|           	|
| ICC                              	| 0.28                             	|                      	|         	|                                  	|                      	|         	|                                  	|                      	|             	|           	|
| N                                	| 3 Species                        	|                      	|         	| 3 Species                        	|                      	|         	| 3 Species                        	|                      	|             	|           	|
|                                  	| 31 fi_fishid                     	|                      	|         	| 31 fi_fishid                     	|                      	|         	| 31 fi_fishid                     	|                      	|             	|           	|
|                                  	| 11 month                         	|                      	|         	| 11 month                         	|                      	|         	| 11 month                         	|                      	|             	|           	|
| Observations                     	| 287                              	|                      	|         	| 287                              	|                      	|         	| 287                              	|                      	|             	|           	|
| Marginal R2 / Conditional R2     	| 0.231 / 0.450                    	|                      	|         	| 0.245 / NA                       	|                      	|         	| 0.293 / NA                       	|                      	|             	|           	|
```

Plot marginal effects of **body_size** for each species

```
plot_model(m18, type = "pred", terms = c("body_size", "Species"))
```
![Dist_range_month](/Plots/Dist_range_month_0.png "Dist_range_month")

```
plot_model(m18,  mdrt.values = "meansd", type = "pred", terms = c("Species", "body_size"))
```
![Dist_range_month](/Plots/Dist_range_month_01.png "Dist_range_month")

### 4. Analysis of the effects of body size on monthly distance range travelled by each species

Looking at the summary results for the best-fit model we see the _Species_ x _body_size_ interaction is significant
```
summ(m18)
```
```
MODEL INFO:
Observations: 287
Dependent Variable: dist.range
Type: Mixed effects linear regression

MODEL FIT:
AIC = 5013.31, BIC = 5079.18
Pseudo-R² (fixed effects) = 0.23
Pseudo-R² (total) = 0.45

FIXED EFFECTS:
------------------------------------------------------------------------------
                                       Est.      S.E.   t val.     d.f.      p
-------------------------------- ---------- --------- -------- -------- ------
(Intercept)                        -7750.94   1978.23    -3.92   153.90   0.00
body_size                              5.70      1.06     5.38    24.17   0.00
Speciespikeperch                    7683.79   2069.83     3.71    28.78   0.00
Specieswels                         5181.77   1193.44     4.34    32.66   0.00
month                                182.71     79.62     2.29    27.93   0.03
day_count                            173.24     56.68     3.06   158.13   0.00
body_size:Speciespikeperch           -10.83      3.98    -2.72    25.61   0.01
body_size:Specieswels                 -5.65      1.24    -4.56    24.30   0.00
Speciespikeperch:month              -315.04     96.06    -3.28    26.34   0.00
Specieswels:month                   -321.74     80.08    -4.02    26.59   0.00
------------------------------------------------------------------------------

p values calculated using Satterthwaite d.f.

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)    971.14
 Species:fi_fishid      month       121.13
       month         (Intercept)    506.34
      Species        (Intercept)     0.40
      Species           month        0.04
     Residual                       1276.45
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      0.33
       month            11      0.09
      Species           3       0.00
-------------------------------------
```

#### Calculate Level-2 and Level-3 ICC indices

:books:`library(easystats)`
```
iccm.1st <- icc(m18)
print(iccm.1st)
print(iccm.1st, comp = "var")
```
```
# Intraclass Correlation Coefficient

     Adjusted ICC: 0.285
  Conditional ICC: 0.219

# Intraclass Correlation Coefficient

     Adjusted ICC: 0.285
  Conditional ICC: 0.219
```
Between levels 1 and 2
```
sum(get_re_var(m18)) / (sum(get_re_var(m18)) + get_re_var(m18, "sigma_2"))
```
Between levels 2 and 3
```
get_re_var(m18)[2] / sum(get_re_var(m18))
```

#### Pairwise comparisons

:books:`library(emmeans)`
```
emtrends(m18, pairwise ~ Species, var = "body_size",pbkrtest.limit = 10000, lmerTest.limit = 10000)
```
```
$emtrends
 Species   body_size.trend    SE   df lower.CL upper.CL
 pike               5.7027 1.263 33.7     3.14     8.27
 pikeperch         -5.1263 4.569 34.6   -14.41     4.15
 wels               0.0484 0.774 33.3    -1.53     1.62

Degrees-of-freedom method: kenward-roger
Confidence level used: 0.95

$contrasts
 contrast         estimate   SE   df t.ratio p.value
 pike - pikeperch    10.83 4.74 34.6  2.285  0.0714
 pike - wels          5.65 1.48 33.6  3.827  0.0015
 pikeperch - wels    -5.17 4.63 34.6 -1.117  0.5104

Degrees-of-freedom method: kenward-roger
P value adjustment: tukey method for comparing a family of 3 estimates
```
```
emmeans(m16, pairwise ~ Species, pbkrtest.limit = 10000, lmerTest.limit = 10000)
```
```
$emmeans
 Species   emmean   SE   df lower.CL upper.CL
 pike        3510 2310 30.9    -1202     8222
 pikeperch   -329 2946 36.7    -6301     5643
 wels        1836 2343 20.2    -3048     6721

Degrees-of-freedom method: kenward-roger
Confidence level used: 0.95

$contrasts
 contrast         estimate   SE    df t.ratio p.value
 pike - pikeperch     3839 3589  37.0  1.069  0.5387
 pike - wels          1673 3211 127.3  0.521  0.8611
 pikeperch - wels    -2165 3653  36.5 -0.593  0.8249

Degrees-of-freedom method: kenward-roger
P value adjustment: tukey method for comparing a family of 3 estimates
```

#### Plot main-effects

:books:`library(effects)`
```
plot(Effect(c("Species", "body_size"), m18),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Dist_range_month](/Plots/Dist_range_month_1.png "Dist_range_month")

```
plot(Effect(c("Species", "month"), m18),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Dist_range_month](/Plots/Dist_range_month_2.png "Dist_range_month")

**Note:** similar linear predictions can be obtained with the _emmip()_ function in _emmeans_ library as follows:
```
emmip(m18, Species ~ body_size, cov.reduce = range, pbkrtest.limit = 10000, lmerTest.limit = 10000)
```

#### Simple slope analysis of the interactions in the model

In this analysis we estimate the two interaction terms in the model and thus:

- The slopes of body size trends for each Species, i.e., the moderator effects on distance range indicating the values at which the slopes are significant
- The slopes of _month_ (i.e., the rate of change in _dist.range_ throughout monthly time) for each species

We might want to consider re-fitting **Model 18** with a gamma distribution. Is it feasible?

- The distribution of _dist.range_ is right-skew and always positive (see histogram above)
- DV must have values > 0 but _dis.range_ contain 0s, thus we need to assign thoses cases values > 0 (e.g., 0.0001) before re-fitting the model
- Thus, we re-fit the model with gamma and log-link to be consistent with original model fit

```
data_distr_sub<-data_distr %>% mutate(dist.range = replace(dist.range, dist.range == 0, 0.0001))
```
```
m18_gamma<-glmer(dist.range ~ 1 + body_size * Species + Species * month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr_sub, REML=T, control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```

**Slope of the Species x body_size interaction**

```
probe_interaction(m18_gamma, pred = body_size, modx = Species, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on travel distance")
```
```
SIMPLE SLOPES ANALYSIS

When Species = wels:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.00   0.00    -0.22   0.82
Conditional intercept          7.41   0.21    34.49   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.00   0.00    -1.33   0.18
Conditional intercept          6.13   0.97     6.33   0.00

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     3.22   0.00
Conditional intercept         7.99   0.24    33.78   0.00
```

- The slopes of _body_size_ is significant (p < 0.05) and positive only for _pike_

```
interact_plot(m18_gamma, pred = body_size, modx = Species, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on travel distance")
```
![Dist_range_month](/Plots/Dist_range_month_3.png "Dist_range_month")

**Slope of the Species x month interaction**

```
probe_interaction(m18_gamma, pred = month, modx = Species, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Time (11 months)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Monthly  changes in travel distance")
```
```
When Species = wels:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of month                -0.08   0.04    -2.18   0.03
Conditional intercept          7.41   0.21    34.49   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of month                -0.08   0.04    -1.95   0.05
Conditional intercept          6.13   0.97     6.33   0.00

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of month                0.10   0.04     2.38   0.02
Conditional intercept         7.99   0.24    33.78   0.00
```

- The slope for _month_ (i.e, the monthly rate of change) is only significant and positive for _pike_ suggesting that there is a tendency of increased distance range towards the end month
**Plot the Species x month interaction**

```
interact_plot(m18_gamma, pred = month, modx = Species, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Time (months)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Monthly rate of change in travel distance")
```
![Dist_range_month](/Plots/Dist_range_month_4.png "Dist_range_month")

### Does body size determine monthly changes in distance range between species?

- For this analysis we may take **Model 17** including a three-way interaction between body size, month and species
- However, to test separate effects by month we need to convert the variable _month_ to factor and re-fit the model
- Computing a model with so many levels in both RF and FE terms requires pretty much computation power and fitting time considerably extends
- This is not the object of these LMM analyses (i.e., assessing the RF covariance) but it is ok here for further exploring purposes

```
data_distr_sub$month <- as.factor(data_distr_sub$month)
```
```
m17_gamma<-glmer(dist.range ~ 1 + body_size * Species * month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr_sub, REML=F, control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```
```
probe_interaction(m17_gamma, pred = month, modx = Species, mod2= body_size, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Time (11 months)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on travel distance")
```
```
¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While body_size (2nd moderator) =  536.80 (- 1 SD) ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of month                0.14   0.05     2.80   0.01
Conditional intercept         6.99   0.24    29.34   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of month                -0.10   0.05    -2.18   0.03
Conditional intercept          7.23   0.34    21.44   0.00

When Species = wels:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of month                -0.09   0.07    -1.17   0.24
Conditional intercept          7.44   0.38    19.81   0.00

¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While body_size (2nd moderator) =  897.18 (Mean) ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of month                0.07   0.05     1.51   0.13
Conditional intercept         7.99   0.23    34.25   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of month                -0.34   0.14    -2.53   0.01
Conditional intercept          5.94   0.96     6.18   0.00

When Species = wels:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of month                -0.08   0.05    -1.76   0.08
Conditional intercept          7.41   0.21    34.57   0.00

¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While body_size (2nd moderator) = 1257.55 (+ 1 SD) ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of month                0.01   0.09     0.12   0.90
Conditional intercept         9.00   0.50    17.87   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of month                -0.58   0.21    -2.72   0.01
Conditional intercept          4.65   2.30     2.02   0.04

When Species = wels:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of month                -0.07   0.04    -2.09   0.04
Conditional intercept          7.39   0.16    47.24   0.00
```
- For _pikeperch_, the slope of month is always signficant and negative thereby suggesting a negative rate of change in time independently of body size
- For _pike_, the slope of month is only significant and positive when body size is about 536.80 (Mean-SD) suggesting a positive rate of change in time associated with smaller sizes
- For _wels_, the slope of month is only significant and negative when body size is about 1257.55 (Mean+SD) suggesting a negative rate of change in time associated with larger sizes

```
interact_plot(m17_gamma, pred = month, modx = Species, mod2= body_size, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Time (months)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Time-effects of body size on travel distance")
```
![Dist_range_month](/Plots/Dist_range_month_5.png "Dist_range_month")

# Is the rate of excursions to tributary related to the rate of dam use?

## 1. Fit a model using _tributary_ as DV and the Species x month interaction and other covariates if necessary

For the count variable _tributary_ we will fit models using a Poisson distribution. Alternatively, we might want to use a negative binomial regression instead (see below).
**Note** that even if fitting a gamma model is not totally correct for discrete data, otherwise a good fit would mean a likely good fit of the negative binomial (here we ommit the gamma adjustment though).

### 1.1. Find the best unconditional nested intercept-only models fitted via REML

We fit a model using the same random-effects structure selected above, which is valid for the analysis of the rate of change in time.

```
m_trib_in1<-glmer(tributary ~ 1 + (1| Species:fi_fishid)+(1| Species),data = data_distr, REML=T, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit, family="poisson")
m_trib_in2<-glmer(tributary ~ 1 + (1| Species:fi_fishid)+(1| Species)+(1| month),data = data_distr, REML=T, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit, family="poisson")
m_trib_sl<-glmer(tributary ~ 1 + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=T, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit, family="poisson")
```
- **m_trib_sl** is preferred
- **Note** that if we peform the same selection procedure as before, the result is the same as the preferred model is the most complex

### 1.2. Find the best fixed-effects and fit a model including all relevant random-effects

We conduct a hypothesis testing using a top-down approach about the effects of dam use on the rate of tributary excursions.
**Alternatively**, conduct model-selection (see point 1.2a below)

#### 1.2.1. Fit a model of monthal differences in tributary use between species (Species x month interaction)

```
m_trib_int<- glmer(tributary ~ 1 + month * Species + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit, family="poisson")
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
       chisq        ratio          rdf            p
7.062185e+02 2.568067e+00 2.750000e+02 1.037162e-39
```
The test is significant indicating the model is over-dispersed

#### 1.2.2. Would a negative binomial be more appropiate given our dataset?

```
ddply(data_distr,.(Species),summarize,mean=mean(tributary),sd=sd(tributary),nobs=length(unique(fi_fishid)))
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
m_trib_int_nb<-glmer.nb(tributary ~ 1 + month * Species + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```

How is Poisson compared to Negative Binomial?

```
anova(m_trib_int_nb, m_trib_int)
```
```
Models:
m_trib_int: tributary ~ 1 + month * Species + (1 + month | Species:fi_fishid) +
m_trib_int:     (1 + month | Species)
m_trib_int_nb: tributary ~ 1 + month * Species + (1 + month | Species:fi_fishid) +
m_trib_int_nb:     (1 + month | Species)
              Df    AIC    BIC  logLik deviance  Chisq Chi Df Pr(>Chisq)
m_trib_int    12 1384.6 1428.5 -680.29   1360.6
m_trib_int_nb 13 1047.6 1095.1 -510.78   1021.6 339.01      1  < 2.2e-16 ***
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
m_trib_int_nb: tributary ~ 1 + month * Species + (1 + month | Species:fi_fishid) +
m_trib_int_nb:     (1 + month | Species)
m_trib_int_nb_dam: tributary ~ month + Species + (1 + month | Species:fi_fishid) +
m_trib_int_nb_dam:     (1 + month | Species) + dam + month:Species
                  Df    AIC    BIC  logLik deviance  Chisq Chi Df Pr(>Chisq)
m_trib_int_nb     13 1047.6 1095.1 -510.78  1021.56
m_trib_int_nb_dam 14 1025.4 1076.6 -498.69   997.39 24.169      1  8.822e-07 ***
```

- The addition of _dam_ improves the model fit and we can say **m_trib_int_nb_dam** is a better model
```
summ(m_trib_int_nb_dam,  center = TRUE, scale = TRUE, n.sd = 2)
```
```
MODEL INFO:
Observations: 287
Dependent Variable: tributary
Type: Mixed effects generalized linear regression
Error Distribution: Negative Binomial(1.0475)
Link function: log

MODEL FIT:
AIC = 1025.39, BIC = 1076.62
Pseudo-R² (fixed effects) = 0.32
Pseudo-R² (total) = 0.91

FIXED EFFECTS:
-----------------------------------------------------------
                                Est.   S.E.   z val.      p
---------------------------- ------- ------ -------- ------
(Intercept)                    -2.20   0.82    -2.70   0.01
month                           0.91   1.10     0.82   0.41
Speciespikeperch                2.42   1.17     2.06   0.04
Specieswels                     1.05   1.00     1.05   0.29
dam                            -2.45   0.51    -4.79   0.00
month:Speciespikeperch         -2.84   1.43    -1.99   0.05
month:Specieswels              -2.72   1.25    -2.17   0.03
-----------------------------------------------------------

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)     2.10
 Species:fi_fishid      month        2.10
      Species        (Intercept)     0.00
      Species           month        0.00
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      0.51
      Species           3       0.00
-------------------------------------

Continuous predictors are mean-centered and scaled by 2 s.d.
```

- The rate of dam use is highly significant and likely highest in relative importance
- In fact, _dam_ is significantly related to _tributary_ whilst weakly correlated (r=-0.32, see multicolinearity tests above)
- The interaction Species x month is significant

Plot the Species x month interaction from negative binomial model

```
plot(Effect(c("month","Species"),m_trib_int_nb_dam),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Dist_range_month](/Plots/Dist_range_month_6.png "Dist_range_month")


#### 1.2.4. Are there conditional effects of the dam use on tributary excursions

Fit a model including the 3-way interaction _Species * month * dam_
```
m_trib_3int_nb_dam<-glmer.nb(tributary ~ 1 + month * Species * dam + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
Summary of nested models comparisons between this and previous fitted models
```
lrtest(m_trib_int_nb, m_trib_int_nb_dam,m_trib_3int_nb_dam)
```
```
Likelihood ratio test

Model 1: tributary ~ 1 + month * Species + (1 + month | Species:fi_fishid) +
    (1 + month | Species)
Model 2: tributary ~ month + Species + (1 + month | Species:fi_fishid) +
    (1 + month | Species) + dam + month:Species
Model 3: tributary ~ 1 + month * Species * dam + (month | Species:fi_fishid) +
    (month | Species)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  13 -510.78
2  14 -498.69  1 24.169  8.822e-07 ***
3  19 -493.31  5 10.777    0.05599 .
```
**m_trib_3int_nb_dam** is preferred to model **m_trib_int_nb** but only marginally significant with respect to the 3-way interaction model
```
summ(m_trib_3int_nb_dam,  center = TRUE, scale = TRUE, n.sd = 2)
```
```
MODEL INFO:
Observations: 287
Dependent Variable: tributary
Type: Mixed effects generalized linear regression
Error Distribution: Negative Binomial(1.3929)
Link function: log

MODEL FIT:
AIC = 1024.61, BIC = 1094.14
Pseudo-R² (fixed effects) = 0.41
Pseudo-R² (total) = 0.93

FIXED EFFECTS:
---------------------------------------------------------------
                                    Est.   S.E.   z val.      p
-------------------------------- ------- ------ -------- ------
(Intercept)                        -2.16   0.88    -2.45   0.01
month                               0.70   1.12     0.63   0.53
Speciespikeperch                    2.21   1.25     1.76   0.08
Specieswels                         0.85   1.08     0.79   0.43
dam                                -1.07   1.12    -0.95   0.34
month:Speciespikeperch             -2.32   1.49    -1.56   0.12
month:Specieswels                  -2.88   1.27    -2.26   0.02
month:dam                           2.07   1.83     1.14   0.26
Speciespikeperch:dam               -2.10   1.73    -1.21   0.23
Specieswels:dam                    -2.28   1.44    -1.58   0.11
month:Speciespikeperch:dam         -0.39   2.80    -0.14   0.89
month:Specieswels:dam              -4.46   2.31    -1.93   0.05
---------------------------------------------------------------

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)     2.21
 Species:fi_fishid      month        2.08
      Species        (Intercept)     0.00
      Species           month        0.00
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      0.58
      Species           3       0.00
-------------------------------------

Continuous predictors are mean-centered and scaled by 2 s.d.
```

- There is significant effects of the Species x month x dam interaction

Plot the Species x month x dam interaction

```
plot(Effect(c("month","Species", "dam"),m_trib_3int_nb_dam),lines=list(multiline=TRUE), rug = FALSE, layout=c(3, 2))
```
![Dist_range_month](/Plots/Dist_range_month_7.png "Dist_range_month")

```
plot(Effect(c("Species", "dam"),m_trib_3int_nb_dam),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Dist_range_month](/Plots/Dist_range_month_8.png "Dist_range_month")

```
plot(Effect(c("Species", "month"),m_trib_3int_nb_dam),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Dist_range_month](/Plots/Dist_range_month_9.png "Dist_range_month")

```
probe_interaction(m_trib_3int_nb_dam, pred = dam, modx = Species, plot.points = TRUE, jnplot = FALSE)
```
```
SIMPLE SLOPES ANALYSIS

Slope of dam when Species = wels:

   Est.   S.E.   z val.      p
------- ------ -------- ------
  -0.13   0.03    -4.02   0.00

Slope of dam when Species = pikeperch:

   Est.   S.E.   z val.      p
------- ------ -------- ------
  -0.13   0.01   -22.40   0.00

Slope of dam when Species = pike:

   Est.   S.E.   z val.      p
------- ------ -------- ------
  -0.04   0.05    -0.93   0.35
```
```
interact_plot(m_trib_3int_nb_dam, pred = dam, modx = Species, plot.points = TRUE, cond.int = TRUE, interval = FALSE,jnplot = FALSE ,x.label = "Rate of dam use", y.label = "Rate of tributary excursions", main.title = "Conditional effects of dam use on tributary excursions")
```
![Dist_range_month](/Plots/Dist_range_month_10.png "Dist_range_month")

### 1.2a. Model-selection on the rate of tributary excursions

```
Cand.mod <- list()

Cand.mod[[1]]<-glmer.nb(tributary ~ 1 + dam + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[2]]<-glmer.nb(tributary ~ 1 + dam + month + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[3]]<-glmer.nb(tributary ~ 1 + dam + month + Species + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[4]]<-glmer.nb(tributary ~ 1 + dam * month + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[5]]<-glmer.nb(tributary ~ 1 + dam * month + Species + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[6]]<-glmer.nb(tributary ~ 1 + dam * month * Species + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[7]]<-glmer.nb(tributary ~ 1 + dam * Species + month + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[8]]<-glmer.nb(tributary ~ 1 + dam * Species + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[9]]<-glmer.nb(tributary ~ 1 + Species + month + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[10]]<-glmer.nb(tributary ~ 1 + Species * month + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[11]]<-glmer.nb(tributary ~ 1 + Species + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
Cand.mod[[12]]<-glmer.nb(tributary ~ 1 + month + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
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

          K    AICc Delta_AICc AICcWt Cum.Wt      LL
Model 1   9 1024.62       0.00   0.24   0.24 -502.99
Model 8  13 1025.02       0.40   0.20   0.44 -498.84
Model 7  14 1025.06       0.44   0.19   0.64 -497.76
Model 2  10 1025.61       0.99   0.15   0.78 -502.41
Model 3  12 1027.00       2.37   0.07   0.86 -500.93
Model 6  19 1027.46       2.84   0.06   0.92 -493.31
Model 4  11 1027.56       2.94   0.06   0.97 -502.30
Model 5  13 1028.92       4.30   0.03   1.00 -500.79
Model 12  9 1047.37      22.74   0.00   1.00 -514.36
Model 10 13 1048.89      24.27   0.00   1.00 -510.78
Model 9  11 1049.41      24.78   0.00   1.00 -513.22
Model 11 10 1049.65      25.03   0.00   1.00 -514.43
```
Compute the evidence ratio
```
evidence(aictab(cand.set = Cand.mod, modnames = Modnames))
```
```
Evidence ratio between models 'Model 1' and 'Model 8': 1.22
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
Model 1  9 1024.62       0.00   0.24
Model 8 13 1025.02       0.40   0.20
Model 7 14 1025.06       0.44   0.19
Model 2 10 1025.61       0.99   0.15
Model 3 12 1027.00       2.37   0.07
Model 6 19 1027.46       2.84   0.06
Model 4 11 1027.56       2.94   0.06

Model probabilities sum to 0.97
```

Compare the best-fit models (within 2.5 Delta_AICc) of the confidence

```
m1<-glmer.nb(tributary ~ 1 + dam + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m8<-glmer.nb(tributary ~ 1 + dam * Species + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m7<-glmer.nb(tributary ~ 1 + dam * Species + month + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m2<-glmer.nb(tributary ~ 1 + dam + month + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m3<-glmer.nb(tributary ~ 1 + dam + month + Species + (1 + month| Species:fi_fishid)+(1 + month| Species),data = data_distr, REML=F, control=glmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
lrtest(m1,m8,m7,m2,m3 )
```
```
Likelihood ratio test

Model 1: tributary ~ 1 + dam + (1 + month | Species:fi_fishid) + (1 +
    month | Species)
Model 2: tributary ~ 1 + dam * Species + (1 + month | Species:fi_fishid) +
    (1 + month | Species)
Model 3: tributary ~ 1 + dam * Species + month + (1 + month | Species:fi_fishid) +
    (1 + month | Species)
Model 4: tributary ~ 1 + dam + month + (1 + month | Species:fi_fishid) +
    (1 + month | Species)
Model 5: tributary ~ 1 + dam + month + Species + (1 + month | Species:fi_fishid) +
    (1 + month | Species)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   9 -502.99
2  13 -498.84  4 8.2875    0.08160 .
3  14 -497.76  1 2.1712    0.14061
4  10 -502.41 -4 9.2982    0.05406 .
5  12 -500.93  2 2.9547    0.22825
```

Model **m1** is marginally better, however, for the simple slope analysis we keep **m8** which has lower LogLik, deviance and AIC

Simple slope analysis
```
probe_interaction(m8, pred = dam, modx = Species, plot.points = TRUE, jnplot = FALSE)
```
```
SIMPLE SLOPES ANALYSIS

Slope of dam when Species = wels:

   Est.   S.E.   z val.      p
------- ------ -------- ------
  -0.12   0.01   -12.57   0.00

Slope of dam when Species = pikeperch:

   Est.   S.E.   z val.      p
------- ------ -------- ------
  -0.15   0.05    -3.12   0.00

Slope of dam when Species = pike:

   Est.   S.E.   z val.      p
------- ------ -------- ------
  -0.02   0.01    -1.90   0.06
```
The results are nearly the same as those of the 3-way model **m_trib_3int_nb_dam** excluding _month_; however, we can say this the current model is the correct fit to our data

```
interact_plot(m8, pred = dam, modx = Species, plot.points = TRUE, cond.int = TRUE, interval = FALSE,jnplot = FALSE ,x.label = "Rate of dam use", y.label = "Rate of tributary excursions", main.title = "Conditional effects of dam use on tributary excursions")
```
![Dist_range_month](/Plots/Dist_range_month_11.png "Dist_range_month")




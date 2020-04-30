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
data_distr$month <- as.integer(data_distr$month)
```
```
data_distr$fi_fishid <- as.factor(data_distr$fi_fishid)
data_distr$Species <- as.factor(data_distr$Species)
data_distr$month <- as.numeric(data_distr$month)
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

Export table from confidence set models

:books:`library(sjPlot)`
```
tab_model(m1,m10,m16,m2,m11,m12, transform = NULL, collapse.ci = F,  show.adj.icc = TRUE, auto.label = FALSE,  show.se = TRUE,collapse.se = T,
              dv.labels = c("Model 1", "Model 2","Model 3","Model 4","Model 5", "Model 6"),
               string.pred = "Variable",
               string.p = "P" ,file = "Season_dist.range.doc", use.viewer = TRUE)
```
:warning:
- I get the following error: "Error in gsub("Statistic", gsub("-statistic", , attr(statistic, "statistic",  :invalid replacement argument"
 - This is probably related to incorrect loading of the _sjPlot_ library as there is also failure running the _sjp.lmer(m2)_ function

Compare best-fit models of the confidence set

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

### 3. Analysis of the effects of body size on monthly distance range travelled by each species

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

:books:`library(sjstats)`
```
iccm.1st <- icc(m18)
print(iccm.1st)
print(iccm.1st, comp = "var")
```
```
# Intraclass Correlation Coefficient

     Adjusted ICC: 0.285
  Conditional ICC: 0.219
> print(iccm.1st, comp = "var")
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
:warning: There is an issue with the _sjstats_ package

:new: It seems that the _get_re_var_ function is no longer in library _sjstats_ but in library _insight_ (insight::get_variance())

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
emmip(m18, Species ~ body_size, cov.reduce = range, pbkrtest.limit = 10000, lmerTest.limit = 10000))
```

#### Simple slope analysis of the interactions in the model

In this analysis we estimate the two interaction terms in the model and thus:

- The slopes of body size trends for each Species, i.e., the moderator effects on distance range indicating the values at which the slopes are significant
- The slopes of _month_ (i.e., the rate of change in _dist.range_ throughout time) for each Species

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
probe_interaction(m18_gamma, pred = month, modx = Species, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Time (11 months)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on travel distance")
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

- The slope for _month_ (i.e, the rate of change in time) is only significant and positive for _pike_ suggesting that there is a tendency of increased distance range towards the end month
**Plot the Species x month interaction**
```
interact_plot(m18_gamma, pred = month, modx = Species, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Time (months)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Rate of change in travel distance across time")
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
██████████████████████████████████████████ While body_size (2nd moderator) =  536.80 (- 1 SD) █████████████████████████████████████████

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

███████████████████████████████████████████ While body_size (2nd moderator) =  897.18 (Mean) ██████████████████████████████████████████

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

██████████████████████████████████████████ While body_size (2nd moderator) = 1257.55 (+ 1 SD) █████████████████████████████████████████

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

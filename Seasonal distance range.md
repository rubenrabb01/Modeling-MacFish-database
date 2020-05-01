# How does distance travel range seasonally change?

## Create a dataframe including seasonal range and reservoir parts

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
data_distr_season$month <- as.factor(data_distr_season$month)
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
hist(data_distr$dist.range, breaks = 20)
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
Export table of the six best models

:books:`library(sjPlot)`
```
tab_model(m10,m8,m1,m2,m5,m7,m3, transform = NULL, collapse.ci = F,  show.adj.icc = TRUE, auto.label = FALSE,  show.se = TRUE,collapse.se = T,
              dv.labels = c("Model 1", "Model 2","Model 3","Model 4","Model 5", "Model 6"),
               string.pred = "Variable",
               string.p = "P" ,file = "Season_dist.range.doc", use.viewer = TRUE)

```
:warning:
- I get the following error: "Error in gsub("Statistic", gsub("-statistic", , attr(statistic, "statistic",  :invalid replacement argument"
 - This is probably related to incorrect loading of the _sjPlot_ library as there is also failure running the _sjp.lmer(m2)_ function

Compare the three best-fit models (within 2.5 Delta_AICc) of the confidence

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

### 3. Analysis of the effects of body size on seasonal distance range travelled by each species

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

:books:`library(sjstats)`
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
:warning:
- Cant compute random effect variances (probably because random slopes not present as fixed effects, hence, some variance components equal zero)
- Re-fit the model
- There is also some issue with the _sjstats_ package. Try the following for next session:

```
:books:`library(devtools)`
install_local("~/Teri_longit_move/scripts/sjstats_0.17.8.tar.gz")
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

- The slope for _season_ (i.e, the seasonal rate of change) is significant and negative in both _wels_ and _pikeperch_ _pike_ suggesting that there is a tendency of decreased distance range from Spring I towards Winter.
- For _pike_ there the slope of season was not significant

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
████████████████████████████████████████████████████ While season (2nd moderator) = 1 ███████████████████████████████████████████████████

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

████████████████████████████████████████████████████ While season (2nd moderator) = 2 ███████████████████████████████████████████████████

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

████████████████████████████████████████████████████ While season (2nd moderator) = 3 ███████████████████████████████████████████████████

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

████████████████████████████████████████████████████ While season (2nd moderator) = 4 ███████████████████████████████████████████████████

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

████████████████████████████████████████████████████ While season (2nd moderator) = 5 ███████████████████████████████████████████████████

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
██████████████████████████████████████████ While dam (2nd moderator) = -8.12 (- 1 SD) █████████████████████████████████████████

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

███████████████████████████████████████████ While dam (2nd moderator) = 21.31 (Mean) ██████████████████████████████████████████

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

██████████████████████████████████████████ While dam (2nd moderator) = 50.75 (+ 1 SD) █████████████████████████████████████████

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
m_dam_season_sp<-glmer(dist.range ~ 1 + season * Species * dam + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```
```
summ(m_dam_season_sp)
```
```
MODEL INFO:
Observations: 131
Dependent Variable: dist.range
Type: Mixed effects generalized linear regression
Error Distribution: Gamma
Link function: log

MODEL FIT:
AIC = 94.70, BIC = 270.09
Pseudo-R² (fixed effects) =  NA
Pseudo-R² (total) =  NA

FIXED EFFECTS:
-----------------------------------------------------------------
                                      Est.   S.E.   t val.      p
---------------------------------- ------- ------ -------- ------
(Intercept)                           7.48   0.25    29.77   0.00
season1                               0.65   0.65     1.00   0.32
season2                              -0.23   0.42    -0.54   0.59
season3                               0.07   0.45     0.16   0.87
season4                               0.20   0.42     0.48   0.63
Speciespikeperch                      0.83   0.39     2.13   0.03
Specieswels                           0.64   0.34     1.90   0.06
dam                                   0.00   0.01     0.35   0.73
season1:Speciespikeperch             -1.25   0.82    -1.52   0.13
season2:Speciespikeperch             -0.48   0.63    -0.77   0.44
season3:Speciespikeperch             -0.71   0.68    -1.05   0.29
season4:Speciespikeperch             -1.26   0.64    -1.96   0.05
season1:Specieswels                  -1.25   0.74    -1.67   0.09
season2:Specieswels                  -0.63   0.57    -1.09   0.27
season3:Specieswels                  -0.36   0.63    -0.57   0.57
season4:Specieswels                  -0.89   0.56    -1.58   0.11
season1:dam                          -0.01   0.08    -0.17   0.87
season2:dam                           0.02   0.01     1.28   0.20
season3:dam                          -0.01   0.01    -1.00   0.32
season4:dam                           0.01   0.02     0.52   0.60
Speciespikeperch:dam                  0.01   0.02     0.39   0.70
Specieswels:dam                      -0.00   0.01    -0.09   0.93
season1:Speciespikeperch:dam         -0.06   0.09    -0.70   0.48
season2:Speciespikeperch:dam         -0.02   0.02    -1.06   0.29
season3:Speciespikeperch:dam         -0.00   0.02    -0.12   0.90
season4:Speciespikeperch:dam         -0.03   0.02    -1.27   0.20
season1:Specieswels:dam               0.00   0.09     0.04   0.97
season2:Specieswels:dam              -0.02   0.02    -0.91   0.37
season3:Specieswels:dam               0.01   0.01     0.75   0.45
season4:Specieswels:dam              -0.02   0.02    -0.90   0.37
-----------------------------------------------------------------

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)     0.61
 Species:fi_fishid     season1       0.68
 Species:fi_fishid     season2       0.88
 Species:fi_fishid     season3       0.73
 Species:fi_fishid     season4       0.84
      Species        (Intercept)     0.11
      Species          season1       0.18
      Species          season2       0.22
      Species          season3       0.36
      Species          season4       0.17
     Residual                        0.00
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      0.97
      Species           3       0.03
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
![Dist_range_season](/Plots/Dist_range_season_7.png "Dist_range_season")

### 4. Are there differences in excursion rates to tributary between species and across seasons?

#### Fit a model using _tributary_ as DV and the Species x Sesion interaction as a covariate for testing our main hypothesis

The variable _tributary_ is associated with count data and thus models will be fitted using Poisson regression or negative binomial if dispersion is high (previous overdispersion test)

For selecting the best random-effects terms, compare three unconditional models
```
m_trib_in1<-lmer(tributary ~ 1 + (1| Species:fi_fishid)+(1| Species),data = data_distr_season, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_trib_in2<-lmer(tributary ~ 1 + (1| Species:fi_fishid)+(1| Species)+(1| season),data = data_distr_season, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_trib_sl<-lmer(tributary ~ 1 + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
lrtest(m_trib_in1, m_trib_in2)
lrtest(m_trib_in1, m_trib_sl)
lrtest(m_trib_in2, m_trib_sl)
```
The slope model **m_trib_sl** is preferred

#### Include the Species x Season interaction and the _dam_ covariate if necessary

```
m_trib_sl<-lmer(tributary ~ 1 + season * Species + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_trib_sl_dam<-update(m_trib_sl,.~.+ dam)
```
```
anova(m_trib_sl,m_trib_sl_dam)
```
```
Data: data_distr_season
Models:
m_trib_sl: tributary ~ 1 + season * Species + (season | Species:fi_fishid) +
m_trib_sl:     (season | Species)
m_trib_sl_dam: tributary ~ season + Species + (season | Species:fi_fishid) +
m_trib_sl_dam:     (season | Species) + dam + season:Species
              Df    AIC    BIC  logLik deviance  Chisq Chi Df Pr(>Chisq)
m_trib_sl     46 1081.8 1214.0 -494.88   989.76
m_trib_sl_dam 47 1074.3 1209.4 -490.13   980.27 9.4942      1   0.002061 **
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
The addition of _dam_ improves the model fit. In fact, _dam_ is significantly related to _tributary_ but they are weakly correleated (r=-0.32, see multicolinearity tests above)

```
summary(m_trib_sl_dam)
```
```
     AIC      BIC   logLik deviance df.resid
  1074.3   1209.4   -490.1    980.3       84

Scaled residuals:
     Min       1Q   Median       3Q      Max
-1.20736 -0.11954 -0.00655  0.11381  1.87933

Random effects:
 Groups            Name        Variance  Std.Dev. Corr
 Species:fi_fishid (Intercept) 2.835e+02 16.83600
                   season1     2.477e+02 15.73989 -0.97
                   season2     2.664e+02 16.32192 -0.44  0.59
                   season3     2.960e+02 17.20337  0.71 -0.68 -0.15
                   season4     4.598e+02 21.44198 -0.43  0.59  0.78  0.10
 Species           (Intercept) 2.613e-04  0.01616
                   season1     3.521e-04  0.01877 -0.98
                   season2     1.169e-03  0.03418 -0.83  0.89
                   season3     2.277e-03  0.04772  0.20 -0.12  0.28
                   season4     3.893e-03  0.06240 -0.46  0.55  0.83  0.75
 Residual                      9.738e-01  0.98682
Number of obs: 131, groups:  Species:fi_fishid, 31; Species, 3

Fixed effects:
                          Estimate Std. Error        df t value Pr(>|t|)
(Intercept)                4.88384    5.36563  30.82524   0.910  0.36978
season1                   -2.78834    5.04609  28.53476  -0.553  0.58486
season2                    8.62427    5.43870  32.26003   1.586  0.12256
season3                    4.31772    5.50512  31.13894   0.784  0.43878
season4                    1.27849    7.08703  26.41304   0.180  0.85822
Speciespikeperch          20.04900    8.32824  30.36135   2.407  0.02235 *
Specieswels               13.31639    7.00551  30.49365   1.901  0.06681 .
dam                       -0.08507    0.03753  26.43848  -2.267  0.03181 *
season1:Speciespikeperch -19.35755    7.85088  28.28250  -2.466  0.02000 *
season2:Speciespikeperch -23.08372    8.22884  30.91261  -2.805  0.00862 **
season3:Speciespikeperch  12.98548    8.53188  30.50710   1.522  0.13831
season4:Speciespikeperch -13.46096   11.04331  26.70534  -1.219  0.23353
season1:Specieswels      -14.25948    6.59059  28.32734  -2.164  0.03908 *
season2:Specieswels      -19.00174    6.97024  31.24823  -2.726  0.01041 *
season3:Specieswels        7.94179    7.16601  30.44682   1.108  0.27643
season4:Specieswels      -10.46457    9.17739  25.80841  -1.140  0.26465
```

#### Analysis of seasonal differences in tributary use between species (Species x Sesion interaction)

```
cat_plot(m_trib_sl_dam, pred = season, modx = Species, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,x.label = "Season", y.label = "Rate of tributary excursions", pred.labels=c("Spring I","Spring II","Autumn","Summer","Winter"),modx.labels=c("pike","pikeperch","wels"), main.title = "Mean tributary use by specie and season")
```
![Dist_range_season](/Plots/Dist_range_season_8.png "Dist_range_season")

#### Relationship between tributary excursions and dam use

To analyse wether ther are conditional effects of the dam use on tributary excursions we fit a model including a three-way interaction _Species_ x _season_ x _dam_. Actually, this was a candidate model in a former (not included here) model selection subset

:warning:
- The data for _tributary_ contains 0s that are recognized as negative values by the _lme4_ fitting function
- If we want to fit a model to this data adjusting for a "Gamma" distribution we need to re-value 0s to values > 0 (e.g., 0.0001)

```
data_distr_season<-data_distr_season %>% mutate(tributary = replace(tributary, tributary == 0, 0.0001))
```
```
m_trib_3int<-glmer(tributary ~ 1 + season * Species * dam + (1 + season| Species:fi_fishid)+(1 + season| Species),data = data_distr_season, REML=F, control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```
```
probe_interaction(m_trib_3int, pred = dam, mod2 = Species, modx= season, plot.points = TRUE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Rate of dam use", y.label = "Rate of tributary excursions",legend.main="Season", modx.labels=c("Spring I","Spring II","Autumn","Summer","Winter"),main.title = "Conditional effects of dam use on tributary excursions")
```
```
¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While Species (2nd moderator) = pike ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When season = 0:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.14   0.02    -5.80   0.00
Conditional intercept         -2.96   0.69    -4.31   0.00

When season = 1:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.21   0.61    -0.35   0.73
Conditional intercept         -2.20   8.86    -0.25   0.80

When season = 2:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                   0.05   0.06     0.87   0.38
Conditional intercept         -4.01   1.53    -2.62   0.01

When season = 3:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                   0.01   0.02     0.51   0.61
Conditional intercept         -4.14   1.39    -2.97   0.00

When season = 4:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                   0.02   0.05     0.51   0.61
Conditional intercept         -3.67   1.52    -2.42   0.02

¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While Species (2nd moderator) = pikeperch ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When season = 0:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.07   0.06    -1.25   0.21
Conditional intercept          2.10   1.11     1.89   0.06

When season = 1:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.45   0.13    -3.35   0.00
Conditional intercept         -7.98   2.25    -3.55   0.00

When season = 2:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.11   0.05    -2.36   0.02
Conditional intercept         -1.03   1.67    -0.62   0.53

When season = 3:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.11   0.05    -2.08   0.04
Conditional intercept          1.94   1.62     1.20   0.23

When season = 4:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.07   0.07    -1.06   0.29
Conditional intercept          1.08   1.72     0.63   0.53

¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While Species (2nd moderator) = wels ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When season = 0:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.05   0.03    -1.44   0.15
Conditional intercept          1.46   0.66     2.20   0.03

When season = 1:

                                Est.   S.E.   t val.      p
--------------------------- -------- ------ -------- ------
Slope of dam                   -0.45   0.12    -3.68   0.00
Conditional intercept         -10.65   1.75    -6.07   0.00

When season = 2:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.03   0.03    -1.16   0.24
Conditional intercept         -2.59   1.46    -1.77   0.08

When season = 3:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.11   0.04    -2.75   0.01
Conditional intercept          0.33   1.31     0.25   0.80

When season = 4:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of dam                  -0.04   0.02    -1.76   0.08
Conditional intercept         -2.25   1.39    -1.62   0.11
```
```
interact_plot(m_trib_3int, pred = dam, mod2 = Species, modx= season, plot.points = TRUE, cond.int = TRUE, interval = FALSE,jnplot = FALSE ,x.label = "Rate of dam use", y.label = "Rate of tributary excursions",legend.main="Season", modx.labels=c("Spring I","Spring II","Autumn","Summer","Winter"),main.title = "Conditional effects of dam use on tributary excursions")
```
![Dist_range_season](/Plots/Dist_range_season_9.png "Dist_range_season")















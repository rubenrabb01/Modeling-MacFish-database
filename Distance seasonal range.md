# How does distance monthly range change?

## Create a dataframe including monthly range and reservoir parts

To do this, create a new monthly range dataframe (see page "Common code") but this time including reservoir parts (the first three steps are the same as in previous code)
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

Check for correlations between _distance_range_ and reservoir parts
```
corr<-cor(data_distr_season[,c(3,9,10,11,12)], use="pairwise", method="spearman")
corrplot(corr,method="number")
```
![Dist_range_season](/Plots/Dist_range_corr.png "Dist_range_season")

## Fit Mixed-Effects Models (LMM) to the data of seasonal distance range and use of reservoir parts

### 1. Find the best unconditional model fitted via REML

#### 1.1. Fit nested intercept models including all potentially relevant random-effects and compare them with LRT. Since models **m_int_1** and **m_int_2** are not nested they are compared using is the difference in LogLik between both models

```
m_int_1<-lmer(dist.range ~ 1  + (1| Species) + (1| fi_fishid),data = data_distr_season, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_int_2<-lmer(dist.range ~ 1  + (1| Species) + (1| season),data = data_distr_season, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_int_3<-lmer(dist.range ~ 1  + (1| fi_fishid) + (1| Species) + (1| season),data = data_distr_season, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
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

#### 1.2. Fit nested slope models including all potentially relevant random-effects and compare them with LRT.

```
m_sl_1<-lmer(dist.range ~ 1  + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_sl_2<-lmer(dist.range ~ 1  + (season| Species:fi_fishid)+(season| Species) + (1|season),data = data_distr_season, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
lrtest(m_sl_1,m_sl_2)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + (season | Species:fi_fishid) + (season |
    Species)
Model 2: dist.range ~ 1 + (season | Species:fi_fishid) + (season |
    Species) + (1 | season)
  #Df  LogLik Df Chisq Pr(>Chisq)
1  32 -1153.3
2  33 -1153.3  1 0.023     0.8795
```
Model **m_sl_1** is chosen

#### 1.3. Compare the best intercept and slope models

```
0.5*(1 - pchisq(13.3, 1)) + 0.5*(1 - pchisq(13.3, 2))
```
[1] 0.0007797141
```

The Chi-square test p < 0.05 indicating there is significant evidence in support of the larger model **m_sl_1**

### 2. Find the best conditional LMMs fitted via ML including the RF structure of the previously selected unconditional model

```
m1<-lmer(dist.range ~ 1 + body_size + Species + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m2<-lmer(dist.range ~ 1 + body_size * Species + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m3<-lmer(dist.range ~ 1 + body_size + season + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m4<-lmer(dist.range ~ 1 + body_size * season + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m5<-lmer(dist.range ~ 1 + Species + season + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m6<-lmer(dist.range ~ 1 + Species * season + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m7<-lmer(dist.range ~ 1 + body_size + Species + season + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m8<-lmer(dist.range ~ 1 + body_size + Species * season + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m9<-lmer(dist.range ~ 1 + body_size * Species + season + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m10<-lmer(dist.range ~ 1 + body_size * Species * season + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m11<-lmer(dist.range ~ 1 + body_size * Species + Species * season + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
bic = BIC(m1,m2,m3,m4,m5,m6,m7,m8,m9,m10,m11)
sortScore(bic , score = "bic")
```
```
df      BIC
m1  35 2484.210
m2  37 2485.037
m3  37 2495.912
m9  41 2500.092
m7  39 2501.996
m5  38 2502.447
m4  41 2511.647
m11 49 2524.985
m8  47 2526.404
m6  46 2527.218
m10 61 2572.438
```
```
lrtest(m1,m2)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size + Species + (season | Species:fi_fishid) +
    (season | Species)
Model 2: dist.range ~ 1 + body_size * Species + (season | Species:fi_fishid) +
    (season | Species)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  35 -1156.8
2  37 -1152.3  2 8.9238    0.01154 *
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
```
lrtest(m2,m10)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size * Species + (season | Species:fi_fishid) +
    (season | Species)
Model 2: dist.range ~ 1 + body_size * Species * season + (season |
    Species:fi_fishid) + (season | Species)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  37 -1152.3
2  61 -1137.5 24 29.604     0.1982
```
```
lrtest(m2,m3)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size * Species + (season | Species:fi_fishid) +
    (season | Species)
Model 2: dist.range ~ 1 + body_size + season + (season | Species:fi_fishid) +
    (season | Species)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  37 -1152.3
2  37 -1157.8  0 10.875  < 2.2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
```
lrtest(m2,m9)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size * Species + (season | Species:fi_fishid) +
    (season | Species)
Model 2: dist.range ~ 1 + body_size * Species + season + (season |
    Species:fi_fishid) + (season | Species)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  37 -1152.3
2  41 -1150.1  4 4.4456      0.349
```
**Model 2** is always selected. Note that **Model 2** and **Model 3** have the same _df_ and in this case the lower log-likelihood of **Model 3** indicates a poorer fit than **Model 2**

### Analysis of the best-fit model

```
summ(m2)
```
```
 MODEL INFO:
Observations: 131
Dependent Variable: dist.range
Type: Mixed effects linear regression

MODEL FIT:
AIC = 2378.65, BIC = 2485.04

FIXED EFFECTS:
-----------------------------------------------------------------------------
                                       Est.      S.E.   t val.    d.f.      p
-------------------------------- ---------- --------- -------- ------- ------
(Intercept)                        -2655.22   1121.67    -2.37   22.39   0.03
body_size                              7.02      1.45     4.83   18.37   0.00
Speciespikeperch                   10455.05   2806.71     3.73   24.04   0.00
Specieswels                         5410.77   1593.43     3.40   22.96   0.00
body_size:Speciespikeperch           -13.61      5.48    -2.48   23.06   0.02
body_size:Specieswels                 -6.07      1.71    -3.55   19.02   0.00
-----------------------------------------------------------------------------

p values calculated using Satterthwaite d.f.

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)    1063.52
 Species:fi_fishid     season1      1237.93
 Species:fi_fishid     season2      1632.73
 Species:fi_fishid     season3      1676.52
 Species:fi_fishid     season4      1736.89
      Species        (Intercept)     0.00
      Species          season1      2169.11
      Species          season2      1476.08
      Species          season3      966.00
      Species          season4      2027.26
     Residual                       892.52
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      0.59
      Species           3       0.00
-------------------------------------
```
#### Plot main-effects
```
plot(Effect(c("Species", "body_size"), m2),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Dist_range_season](/Plots/Dist_range_season_1.png "Dist_range_season")

#### Simple slope analysis

In this analysis we estimate the slopes of body size trends for each Species, i.e., the moderator effects on distance range indicating the values at which the slopes are significant
```
probe_interaction(m2, pred = body_size, modx = Species, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on travel distance")
```
```
SIMPLE SLOPES ANALYSIS

When Species = wels:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of body_size               0.96     0.90     1.06   0.30
Conditional intercept         3588.77   442.40     8.11   0.00

When Species = pikeperch:

                                 Est.      S.E.   t val.      p
--------------------------- --------- --------- -------- ------
Slope of body_size              -6.62      5.27    -1.26   0.22
Conditional intercept         1919.51   2224.89     0.86   0.40

When Species = pike:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of body_size               7.02     1.45     4.84   0.00
Conditional intercept         3587.44   501.18     7.16   0.00
```
The the slope of _body_size_ is positive and significant (p < 0.05) in **pike** but not **wels** and **pikeperch** indicating that body size affected the mean travel distance only in the first species

#### Plot the interaction _body_size_ x _Species_
```
interact_plot(m2, pred = body_size, modx = Species, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on travel distance")
```
![Dist_range_season](/Plots/Dist_range_season_2.png "Dist_range_season")

#### Adjust Model 2 to a gamma distribution
```
m2_gamma<-glmer(dist.range ~ 1 + body_size * Species + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```
```
interact_plot(m2_gamma, pred = body_size, modx = Species, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on travel distance")
```
![Dist_range_season](/Plots/Dist_range_season_3.png "Dist_range_season")



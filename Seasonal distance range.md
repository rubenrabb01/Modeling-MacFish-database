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
```
[1] 0.0007797141
```
- The Chi-square test p < 0.05 indicating there is significant evidence in support of the larger model **m_sl_1**

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

### Analysis of the effects of body size on seasonal distance range travelled by each species

Look at the results summary for the best-fit model
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
- The slope of _body_size_ is positive and significant (p < 0.05) in _pike_ but not _wels_ and _pikeperch_ indicating that body size affected the mean travel distance only in the first species

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

### How does stationarity affect mean distance range?

There is no easy way to approach such an answer but here let consider the proportion of dam "excursions" as a site-fidelity measure. For that, let re-fit the previous model (with gamma distribution) including the variable _dam_ in the _body_size x _Species_ interaction (Three-way interaction)
```
m2_dam<-glmer(dist.range ~ 1 + body_size * Species * dam + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```
```
summ(m2_dam)
```
```
MODEL INFO:
Observations: 131
Dependent Variable: dist.range
Type: Mixed effects generalized linear regression
Error Distribution: Gamma
Link function: log

MODEL FIT:
AIC = 180.07, BIC = 303.70

FIXED EFFECTS:
-------------------------------------------------------------------
                                        Est.   S.E.   t val.      p
------------------------------------ ------- ------ -------- ------
(Intercept)                             5.90   0.52    11.42   0.00
body_size                               0.00   0.00     3.26   0.00
Speciespikeperch                        2.97   1.27     2.34   0.02
Specieswels                             1.29   0.80     1.63   0.10
dam                                    -0.00   0.03    -0.11   0.91
body_size:Speciespikeperch             -0.00   0.00    -1.15   0.25
body_size:Specieswels                  -0.00   0.00    -1.70   0.09
body_size:dam                           0.00   0.00     0.13   0.89
Speciespikeperch:dam                    0.16   0.07     2.38   0.02
Specieswels:dam                         0.03   0.03     0.88   0.38
body_size:Speciespikeperch:dam         -0.00   0.00    -2.66   0.01
body_size:Specieswels:dam              -0.00   0.00    -0.51   0.61
-------------------------------------------------------------------

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)     0.56
 Species:fi_fishid     season1       0.90
 Species:fi_fishid     season2       0.99
 Species:fi_fishid     season3       0.88
 Species:fi_fishid     season4       1.11
      Species        (Intercept)     0.01
      Species          season1       0.40
      Species          season2       0.47
      Species          season3       0.43
      Species          season4       0.45
     Residual                        0.00
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      1.00
      Species           3       0.00
-------------------------------------
```
Perform a simple slope analysis without the moderator effects of _body_size_
```
probe_interaction(m2_dam, pred = body_size, modx = Species, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of dam use on travel distance as subject to body size")
```
```
SIMPLE SLOPES ANALYSIS

When Species = wels:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     0.56   0.58
Conditional intercept         8.09   0.22    37.51   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.01   0.00    -2.50   0.01
Conditional intercept          5.08   1.35     3.77   0.00

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     3.08   0.00
Conditional intercept         8.03   0.25    32.51   0.00
```
- The slopes for _pikeperch_ and _pike_ are signficant but in opposite directions
```
interact_plot(m2_dam, pred = dam, modx = Species, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Proportion of dam use", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("wels","pikeperch","pike"),main.title = "Effects of dam use on travel distance")
```
![Dist_range_season](/Plots/Dist_range_season_4.png "Dist_range_season")

Now we account for the conditional effects of body size
```
probe_interaction(m2_dam, pred = body_size, modx = Species, mod2=body_size, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of dam use on travel distance as subject to body size")
```
```
██████████████████████████████████████████ While body_size (2nd moderator) =  528.21 (- 1 SD) █████████████████████████████████████████

SIMPLE SLOPES ANALYSIS

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     2.37   0.02
Conditional intercept         7.21   0.25    29.02   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.01   0.00    -2.71   0.01
Conditional intercept          8.22   0.23    35.44   0.00

When Species = wels:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     0.93   0.35
Conditional intercept         7.45   0.31    24.06   0.00

███████████████████████████████████████████ While body_size (2nd moderator) =  890.00 (Mean) ██████████████████████████████████████████

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

██████████████████████████████████████████ While body_size (2nd moderator) = 1251.79 (+ 1 SD) █████████████████████████████████████████

SIMPLE SLOPES ANALYSIS

When Species = pike:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     2.32   0.02
Conditional intercept         8.83   0.58    15.31   0.00

When Species = pikeperch:

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of body_size            -0.01   0.00    -2.02   0.04
Conditional intercept          2.52   2.93     0.86   0.39

When Species = wels:

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of body_size            0.00   0.00     0.40   0.69
Conditional intercept         8.14   0.14    57.96   0.00
```
```
interact_plot(m2_dam, pred = dam, modx = Species, mod2=body_size, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Proportion of dam use", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("wels","pikeperch","pike"),main.title = "Effects of dam use on travel distance as subject to body size")
```
![Dist_range_season](/Plots/Dist_range_season_5.png "Dist_range_season")

### Are there any seasonal changes in dam use?

Fit a model of the interaction _dam_ x _season_
```
m_dam_season<-lmer(dist.range ~ 1 + season * dam + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
summ(m_dam_season)
```
```
MODEL INFO:
Observations: 131
Dependent Variable: dist.range
Type: Mixed effects linear regression

MODEL FIT:
AIC = 2386.15, BIC = 2504.04
Pseudo-R² (fixed effects) = 0.13
Pseudo-R² (total) = 0.93

FIXED EFFECTS:
--------------------------------------------------------------
                        Est.      S.E.   t val.    d.f.      p
----------------- ---------- --------- -------- ------- ------
(Intercept)          3309.09    607.33     5.45    3.33   0.01
dam                    37.35     16.07     2.32   11.18   0.04
season1             -1911.50   1444.91    -1.32    2.53   0.29
season2              -927.15    842.42    -1.10    3.41   0.34
season3              -565.90    604.15    -0.94    3.93   0.40
season4              -166.40   1034.89    -0.16    3.16   0.88
dam:season1            88.16     46.64     1.89   19.31   0.07
dam:season2           -20.48     16.80    -1.22   12.35   0.25
dam:season3           -32.46     16.57    -1.96   17.58   0.07
dam:season4           -55.60     16.78    -3.31   12.31   0.01
--------------------------------------------------------------

p values calculated using Satterthwaite d.f.

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)    1536.16
 Species:fi_fishid     season1      2772.09
 Species:fi_fishid     season2      1846.02
 Species:fi_fishid     season3      1777.85
 Species:fi_fishid     season4      1750.79
      Species        (Intercept)    867.21
      Species          season1      2207.44
      Species          season2      1237.94
      Species          season3      714.43
      Species          season4      1593.77
     Residual                       554.14
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      0.69
      Species           3       0.22
-------------------------------------
```
- The interaction is significant but how the slopes change?
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
![Dist_range_season](/Plots/Dist_range_season_6.png "Dist_range_season")

### Are there conditional effects of dam use in distance range across seasons and between species ?

Now we test wether there are seasonal differences between species excluding the moderator effects of body size
```
m_dam_season_sp<-glmer(dist.range ~ 1 + season * Species * dam + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
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
█████████████████████████████████████████████████ While Species (2nd moderator) = pike ██████████████████████████████████████████████████

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

████████████████████████████████████████████████ While Species (2nd moderator) = pikeperch ███████████████████████████████████████████████

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

██████████████████████████████████████████████████ While Species (2nd moderator) = wels ██████████████████████████████████████████████████

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



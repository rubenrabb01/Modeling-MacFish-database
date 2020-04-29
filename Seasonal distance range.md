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

Fit the models with setting **REML=FALSE**

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
Export table of the six best models

:books:`library(sjPlot)`
```
tab_model(m1,m2,m3,m9,m7,m5, transform = NULL, collapse.ci = F,  show.adj.icc = TRUE, auto.label = FALSE,  show.se = TRUE,collapse.se = T,
              dv.labels = c("Model 1", "Model 2","Model 3","Model 4","Model 5", "Model 6"),
               string.pred = "Variable",
               string.p = "P" ,file = "Season_dist.range.doc", use.viewer = TRUE)

```
:warning:
- I get the following error: "Error in gsub("Statistic", gsub("-statistic", , attr(statistic, "statistic",  :invalid replacement argument"
 - This is probably related to incorrect loading of the _sjPlot_ library as there is also failure running the _sjp.lmer(m2)_ function

Compare best-fit models

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

### 3. Analysis of the effects of body size on seasonal distance range travelled by each species

Looking at the summary results for the best-fit model we see the _Species_ x _body_size_ interaction is significant
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

#### Calculate Level-2 and Level-3 ICC indices

:books:`library(sjstats)`
```
iccm.1st <- icc(m2)
print(iccm.1st)
print(iccm.1st, comp = "var")
icc(m2, adjusted = TRUE)
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
emtrends(m2, pairwise ~ Species, var = "body_size",pbkrtest.limit = 10000, lmerTest.limit = 10000)
```
```
$emtrends
 Species   body_size.trend   SE   df lower.CL upper.CL
 pike                7.016 1.92 29.0     3.08    10.95
 pikeperch          -6.592 6.89 33.0   -20.61     7.43
 wels                0.943 1.20 31.2    -1.50     3.38

Degrees-of-freedom method: kenward-roger
Confidence level used: 0.95

$contrasts
 contrast         estimate   SE   df t.ratio p.value
 pike - pikeperch    13.61 7.15 32.7  1.902  0.1542
 pike - wels          6.07 2.26 29.6  2.681  0.0310
 pikeperch - wels    -7.54 6.99 32.9 -1.077  0.5346

Degrees-of-freedom method: kenward-roger
P value adjustment: tukey method for comparing a family of 3 estimates
```
```
emmeans(m2, pairwise ~ Species, pbkrtest.limit = 10000, lmerTest.limit = 10000)
```
```
$emmeans
body_size = 890:
 Species   emmean   SE    df lower.CL upper.CL
 pike        3589 1185 282.6     1257     5921
 pikeperch   1933 3044  36.2    -4239     8105
 wels        3595 1346 179.8      938     6252

Degrees-of-freedom method: kenward-roger
Confidence level used: 0.95

$contrasts
body_size = 890:
 contrast         estimate   SE    df t.ratio p.value
 pike - pikeperch  1655.82 3266  38.7  0.507  0.8684
 pike - wels         -6.29 1793 230.4 -0.004  1.0000
 pikeperch - wels -1662.11 3328  37.9 -0.499  0.8720

Degrees-of-freedom method: kenward-roger
P value adjustment: tukey method for comparing a family of 3 estimates
```
- Trends are significant between _pike_ and _wels_
- However, contrasts render non-significant as they assess least-square means but not trends/slopes (besides not accounting for random-effects and the involvement of the interaction)
- This is the meaning of further performing simple slope analysis

#### Plot main-effects

:books:`library(effects)`
```
plot(Effect(c("Species", "body_size"), m2),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Dist_range_season](/Plots/Dist_range_season_1.png "Dist_range_season")

**Note** that similar linear predictions can be obtained with the _emmip()_ function in _emmeans_ library as follows:
```
emmip(m2, Species ~ body_size, cov.reduce = range, pbkrtest.limit = 10000, lmerTest.limit = 10000))
```

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

#### Plot the Species x body_size interaction
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

### Does body size determine seasonal changes in distance range between species?

For this analysis we can take **Model 10** including a three-way interaction between body size, season and species

```
summ(m10)
```
```
MODEL INFO:
Observations: 131
Dependent Variable: dist.range
Type: Mixed effects linear regression

MODEL FIT:
AIC = 2397.05, BIC = 2572.44
Pseudo-R² (fixed effects) = 0.37
Pseudo-R² (total) = 0.83

FIXED EFFECTS:
-------------------------------------------------------------------------------------
                                               Est.      S.E.   t val.    d.f.      p
---------------------------------------- ---------- --------- -------- ------- ------
(Intercept)                                -3185.33   1799.71    -1.77   30.30   0.09
body_size                                      7.66      2.44     3.14   30.21   0.00
Speciespikeperch                           11031.82   4500.32     2.45   30.21   0.02
Specieswels                                 5752.93   2551.46     2.25   30.25   0.03
season1                                     1847.55   2180.42     0.85    4.22   0.44
season2                                      569.71   2589.13     0.22   30.36   0.83
season3                                     3153.20   2449.12     1.29   30.15   0.21
season4                                     3531.27   2758.30     1.28   25.63   0.21
body_size:Speciespikeperch                   -14.12      8.87    -1.59   30.20   0.12
body_size:Specieswels                         -6.48      2.86    -2.27   30.21   0.03
body_size:season1                             -0.34      2.89    -0.12    3.93   0.91
body_size:season2                              0.74      3.50     0.21   29.72   0.83
body_size:season3                             -4.30      3.33    -1.29   29.94   0.21
body_size:season4                             -2.63      3.70    -0.71   24.58   0.48
Speciespikeperch:season1                   -3914.98   6284.50    -0.62    6.60   0.55
Specieswels:season1                        -6254.17   3144.25    -1.99    4.50   0.11
Speciespikeperch:season2                   -3570.42   6435.99    -0.55   29.58   0.58
Specieswels:season2                         1241.66   3657.64     0.34   29.90   0.74
Speciespikeperch:season3                   -9054.48   6141.16    -1.47   29.94   0.15
Specieswels:season3                          156.01   3477.88     0.04   30.03   0.96
Speciespikeperch:season4                   -5798.35   7170.59    -0.81   27.12   0.43
Specieswels:season4                        -5899.22   3925.43    -1.50   25.54   0.15
body_size:Speciespikeperch:season1            -1.57     12.00    -0.13    6.06   0.90
body_size:Specieswels:season1                  2.35      3.42     0.69    4.11   0.53
body_size:Speciespikeperch:season2             1.59     12.67     0.13   29.46   0.90
body_size:Specieswels:season2                 -3.60      4.09    -0.88   29.64   0.39
body_size:Speciespikeperch:season3            12.14     12.10     1.00   29.91   0.32
body_size:Specieswels:season3                  1.06      3.90     0.27   29.93   0.79
body_size:Speciespikeperch:season4             1.63     14.01     0.12   26.61   0.91
body_size:Specieswels:season4                  3.06      4.37     0.70   24.99   0.49
-------------------------------------------------------------------------------------

p values calculated using Satterthwaite d.f.

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)    1133.24
 Species:fi_fishid     season1      1084.56
 Species:fi_fishid     season2      1626.66
 Species:fi_fishid     season3      1519.46
 Species:fi_fishid     season4      1751.68
      Species        (Intercept)     0.20
      Species          season1       0.14
      Species          season2       0.82
      Species          season3       0.46
      Species          season4       1.35
     Residual                       791.66
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      0.67
      Species           3       0.00
-------------------------------------
```
```
probe_interaction(m10, pred = body_size, modx = Species, mod2 = season, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("Spring I","Spring II","Autumn","Summer","Winter"),main.title = "Effects of dam use on travel distance as subject to body size")
```
```
¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While Species (2nd moderator) = pike ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When season = 0:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of body_size               7.66     2.44     3.14   0.00
Conditional intercept         3633.23   628.51     5.78   0.00

When season = 1:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of body_size               7.28     2.22     3.27   0.01
Conditional intercept         5143.45   634.71     8.10   0.00

When season = 2:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of body_size               8.40     2.71     3.10   0.00
Conditional intercept         4860.07   689.52     7.05   0.00

When season = 3:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of body_size               3.36     3.26     1.03   0.31
Conditional intercept         2962.54   825.94     3.59   0.00

When season = 4:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of body_size               5.04     2.69     1.88   0.07
Conditional intercept         4825.21   684.55     7.05   0.00

¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While Species (2nd moderator) = pikeperch ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When season = 0:

                                 Est.      S.E.   t val.      p
--------------------------- --------- --------- -------- ------
Slope of body_size              -6.46      8.53    -0.76   0.45
Conditional intercept         2101.41   3534.79     0.59   0.56

When season = 1:

                                  Est.      S.E.   t val.      p
--------------------------- ---------- --------- -------- ------
Slope of body_size               -8.36      9.76    -0.86   0.41
Conditional intercept         -1662.74   3746.30    -0.44   0.67

When season = 2:

                                 Est.      S.E.   t val.      p
--------------------------- --------- --------- -------- ------
Slope of body_size              -4.13      9.42    -0.44   0.66
Conditional intercept         1173.24   3905.74     0.30   0.77

When season = 3:

                                 Est.      S.E.   t val.      p
--------------------------- --------- --------- -------- ------
Slope of body_size               1.38     11.41     0.12   0.90
Conditional intercept         3176.78   4728.82     0.67   0.51

When season = 4:

                                  Est.      S.E.   t val.      p
--------------------------- ---------- --------- -------- ------
Slope of body_size               -7.46     10.21    -0.73   0.47
Conditional intercept         -1054.76   4134.08    -0.26   0.80

¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While Species (2nd moderator) = wels ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

SIMPLE SLOPES ANALYSIS

When season = 0:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of body_size               1.18     1.49     0.79   0.43
Conditional intercept         3619.54   581.61     6.22   0.00

When season = 1:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of body_size               3.17     1.46     2.17   0.05
Conditional intercept         1000.11   619.23     1.62   0.13

When season = 2:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of body_size              -1.68     1.64    -1.02   0.32
Conditional intercept         2886.99   642.89     4.49   0.00

When season = 3:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of body_size              -2.05     1.99    -1.03   0.31
Conditional intercept         4049.57   777.06     5.21   0.00

When season = 4:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of body_size               1.62     1.73     0.93   0.36
Conditional intercept         1633.88   635.36     2.57   0.02
```
- The slopes are significant for _pike_ in Spring I, II, and Autumn and for _wels_ in Spring I whereas for _pikeperch_ slopes are not significant in either season
```
interact_plot(m10, pred = body_size, modx = season, mod2 = Species, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Body size (cm)", y.label = "Mean distance range (m)", modx.labels=c("Spring I","Spring II","Autumn","Summer","Winter"), main.title = "Seasonal effects of body size on travel distance")
```
![Dist_range_season](/Plots/Dist_range_season_31.png "Dist_range_season")

### How does stationarity affect mean distance range?

There is no easy way to approach such an answer but here let consider the proportion of dam "excursions" as a site-fidelity measure. For that, let re-fit the previous model (with gamma distribution) including the variable _dam_ in the _Species_ x _body_size_ interaction (Three-way interaction)
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
Perform a simple slope analysis of the effects of dam use on mean distance range
```
probe_interaction(m2_dam, pred = dam, modx = Species, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of dam use on travel distance as subject to body size")
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
- The slope for _pikeperch_ is the only signficant and negative
```
interact_plot(m2_dam, pred = dam, modx = Species,plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Proportion of dam use", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("wels","pikeperch","pike"),main.title = "Effects of dam use on travel distance")
```
![Dist_range_season](/Plots/Dist_range_season_4.png "Dist_range_season")

Now account for the moderator effects of dam on the slopes of body size
```
probe_interaction(m2_dam, pred = body_size, modx = Species, mod2 = dam, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on travel distance as subject to dam use")
```
```
¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While dam (2nd moderator) = -8.12 (- 1 SD) ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

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

¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While dam (2nd moderator) = 21.31 (Mean) ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

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

¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦ While dam (2nd moderator) = 50.75 (+ 1 SD) ¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦

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
At mean proportion of dam use the slopes of body size for _pike_ and _pikeperch_ are significant and in opposite directions
```
interact_plot(m2_dam, pred = body_size, modx = Species, mod2 = dam, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("wels","pikeperch","pike"),main.title = "Effects of body size on travel distance as subject to dam use")
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

For selecting the best random-effects terms, compare three unconditional models
```
m_trib_in1<-lmer(tributary ~ 1 + (1| Species:fi_fishid)+(1| Species),data = data_distr_season, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_trib_in2<-lmer(tributary ~ 1 + (1| Species:fi_fishid)+(1| Species)+(1| season),data = data_distr_season, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_trib_sl<-lmer(tributary ~ 1 + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
lrtest(m_trib_in1, m_trib_in2)
lrtest(m_trib_in1, m_trib_sl)
lrtest(m_trib_in2, m_trib_sl)
```
The slope model **m_trib_sl** is preferred

#### Include the Species x Season interaction and the _dam_ covariate if necessary

```
m_trib_sl<-lmer(tributary ~ 1 + season * Species + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
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
m_trib_3int<-glmer(tributary ~ 1 + season * Species * dam + (season| Species:fi_fishid)+(season| Species),data = data_distr_season, REML=F, control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
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














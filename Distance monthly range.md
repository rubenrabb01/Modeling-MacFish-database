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
Rename some variables
```
colnames(data_distr)[6] <- "body_size"
colnames(data_distr)[3] <- "mon"
colnames(data_distr)[2] <- "month"
colnames(data_distr)[9] <- "Species"
```
Convert the variable _mon_yr_ into a time vector of repeated-measures
```
data_distr$month<-revalue(data_distr$month, c("4_2017"="0","5_2017"="1","6_2017"="2","7_2017"="3","8_2017"="4","9_2017"="5","10_2017"="6","11_2017"="7", "12_2017"="8","1_2018"="9","2_2018"="10","3_2018"="11","4_2018"="12"))
```
Convert variables including _month_ to integer to allow correct ordering (from months 10-12 2018)
```
data_distr$month <- as.integer(data_distr$month)
data_distr$res_part <- as.factor(data_distr$res_part)
data_distr$fi_fishid <- as.factor(data_distr$fi_fishid)
data_distr$Species <- as.factor(data_distr$Species)
```
Order by _fi_fishid_ and _month_
```
data_distr<-data_distr[with(data_distr, order(fi_fishid, month)),]
data_distr$month <- as.factor(data_distr$month)
```
```
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
![Dist_range](/Plots/Dist_range_hist.png "Dist_range")

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
  #Df  LogLik Df Chisq Pr(>Chisq)
1   3 -3034.8
2   4 -3034.8  1     0     0.9961
```
```
lrtest(m_id,m_id_mon)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + (1 | fi_fishid)
Model 2: dist.range ~ 1 + (1 | month) + (1 | fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   3 -3034.8
2   4 -3020.7  1 28.356  1.009e-07 ***
```

```
lrtest(m_id_mon,m_id_sp_mon)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + (1 | month) + (1 | fi_fishid)
Model 2: dist.range ~ 1 + (1 | month) + (1 | fi_fishid) + (1 | Species)
  #Df  LogLik Df Chisq Pr(>Chisq)
1   4 -3020.7
2   5 -3020.7  1     0     0.9974
```
```
anova(m_id_mon,m_sp_mon)
0.5*(1 - pchisq(19.3, 1)) + 0.5*(1 - pchisq(19.3, 2))
```

**Model m_id_mon** is preferred

#### 1.2. Fit nested slope models including all potentially relevant random-effects and compare them with LRT.

```
m_sl_1<-lmer(dist.range ~ 1  + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_sl_2<-lmer(dist.range ~ 1  + (month| Species:fi_fishid)+(month| Species) + (1|month),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
lrtest(m_sl_1,m_sl_2)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + (month | Species:fi_fishid) + (month | Species)
Model 2: dist.range ~ 1 + (month | Species:fi_fishid) + (month | Species) +
    (1 | month)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1 184 -2851.8
2 185 -2879.4  1 55.059   1.17e-13 ***
```
Model **m_sl_1** is PREFERRED

#### 1.3. Compare the best intercept and slope models

```
anova(m_sl_1,m_id_mon)
```
```
Models:
m_id_mon: dist.range ~ 1 + (1 | month) + (1 | fi_fishid)
m_sl_1: dist.range ~ 1 + (month | Species:fi_fishid) + (month | Species)
          Df    AIC    BIC  logLik deviance Chisq Chi Df Pr(>Chisq)
m_id_mon   4 6061.9 6077.3 -3026.9   6053.9
m_sl_1   184 6081.6 6789.3 -2856.8   5713.6 340.3    180  5.592e-12 ***
```
- The Chi-square test p < 0.05 indicating there is significant evidence in support of the larger model **m_sl_1**

### 2. Find the best conditional LMMs fitted via ML including the RF structure of the previously selected unconditional model

Fit the models with setting **REML=FALSE**

```
m1<-lmer(dist.range ~ 1 + body_size + Species + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m2<-lmer(dist.range ~ 1 + body_size * Species + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m3<-lmer(dist.range ~ 1 + body_size + month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m4<-lmer(dist.range ~ 1 + body_size * month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m5<-lmer(dist.range ~ 1 + Species + month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m6<-lmer(dist.range ~ 1 + Species * month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m7<-lmer(dist.range ~ 1 + body_size + Species + month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m8<-lmer(dist.range ~ 1 + body_size + Species * month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m9<-lmer(dist.range ~ 1 + body_size * Species + month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m10<-lmer(dist.range ~ 1 + body_size * Species * month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m11<-lmer(dist.range ~ 1 + body_size * Species + Species * month + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
bic = BIC(m1,m2,m3,m4,m5,m6,m7,m8,m9,m10,m11)
sortScore(bic , score = "bic")
```
```
df      BIC
m2  189 6851.243
m1  187 6861.822
m3  197 6878.680
m5  198 6880.828
m9  201 6906.163
m7  199 6915.146
m4  209 6921.372
m8  223 6992.848
m11 225 7024.412
m6  222 7029.568
m10 261 7061.611
```
Export table of the six best models

:books:`library(sjPlot)`
```
tab_model(m2,m1,m3,m5,m9,m7, transform = NULL, collapse.ci = F,  show.adj.icc = TRUE, auto.label = FALSE,  show.se = TRUE,collapse.se = T,
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

Model 1: dist.range ~ 1 + body_size + Species + (month | Species:fi_fishid) +
    (month | Species)
Model 2: dist.range ~ 1 + body_size * Species + (month | Species:fi_fishid) +
    (month | Species)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1 187 -2884.3
2 189 -2873.1  2 22.272  1.458e-05 ***
```
```
lrtest(m2,m3)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size * Species + (month | Species:fi_fishid) +
    (month | Species)
Model 2: dist.range ~ 1 + body_size + month + (month | Species:fi_fishid) +
    (month | Species)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1 189 -2873.1
2 197 -2863.5  8 19.335    0.01317 *
```
```
lrtest(m2,m5)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size * Species + (month | Species:fi_fishid) +
    (month | Species)
Model 2: dist.range ~ 1 + Species + month + (month | Species:fi_fishid) +
    (month | Species)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1 189 -2873.1
2 198 -2861.6  9 23.033   0.006123 **
```
```
lrtest(m2,m9)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size * Species + (month | Species:fi_fishid) +
    (month | Species)
Model 2: dist.range ~ 1 + body_size * Species + month + (month | Species:fi_fishid) +
    (month | Species)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1 189 -2873.1
2 201 -2865.5 12 15.238     0.2287
```
**Model 2** is the best choice and the addition of _month_ to this model does not improve the model fit (i.e., **Model 9**)

### 3. Analysis of the effects of body size on monthly distance range travelled by each species

Looking at the summary results for the best-fit model we see the _Species_ x _body_size_ interaction is significant
```
summ(m2)
```
```
MODEL INFO:
Observations: 346
Dependent Variable: dist.range
Type: Mixed effects linear regression

MODEL FIT:
AIC = 6124.27, BIC = 6851.24

FIXED EFFECTS:
----------------------------------------------------------------------------
                                       Est.     S.E.   t val.    d.f.      p
-------------------------------- ---------- -------- -------- ------- ------
(Intercept)                        -1911.96   384.90    -4.97   28.05   0.00
body_size                              4.38     0.51     8.58   26.06   0.00
Speciespikeperch                    5480.23   967.79     5.66   28.65   0.00
Specieswels                         3680.66   544.44     6.76   28.13   0.00
body_size:Speciespikeperch            -9.76     1.89    -5.17   27.83   0.00
body_size:Specieswels                 -5.12     0.60    -8.52   26.50   0.00
----------------------------------------------------------------------------

p values calculated using Satterthwaite d.f.

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)    687.18
 Species:fi_fishid     month1       1516.26
 Species:fi_fishid     month2       871.82
 Species:fi_fishid     month3       1777.05
 Species:fi_fishid     month4       1692.82
 Species:fi_fishid     month5       1822.85
 Species:fi_fishid     month6       1410.75
 Species:fi_fishid     month7       1488.27
 Species:fi_fishid     month8       1243.91
 Species:fi_fishid     month9       1076.17
 Species:fi_fishid     month10      1455.77
 Species:fi_fishid     month11      2254.83
 Species:fi_fishid     month12      1387.12
      Species        (Intercept)    140.86
      Species          month1       2509.87
      Species          month2       551.58
      Species          month3       1121.54
      Species          month4       1048.63
      Species          month5       913.88
      Species          month6       601.67
      Species          month7       705.67
      Species          month8       1040.56
      Species          month9       739.32
      Species          month10      683.32
      Species          month11      1543.03
      Species          month12      1495.20
     Residual                       270.72
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      0.84
      Species           3       0.04
-------------------------------------
```

#### Calculate Level-2 and Level-3 ICC indices

:books:`library(sjstats)`
```
iccm.1st <- icc(m2)
print(iccm.1st)
print(iccm.1st, comp = "var")
```
```
# Intraclass Correlation Coefficient

     Adjusted ICC: 0.870
  Conditional ICC: 0.577

# Intraclass Correlation Coefficient

     Adjusted ICC: 0.870
  Conditional ICC: 0.577
```

Between levels 1 and 2
```
sum(get_re_var(m2)) / (sum(get_re_var(m2)) + get_re_var(m2, "sigma_2"))
```
Between levels 2 and 3
```
get_re_var(m2)[2] / sum(get_re_var(m2))
```
:warning: There is an issue with the _sjstats_ package

:new: It seems that the _get_re_var_ function is no longer in library _sjstats_ but in library _insight_ (insight::get_variance())

#### Pairwise comparisons

:books:`library(emmeans)`
```
emtrends(m2, pairwise ~ Species, var = "body_size",pbkrtest.limit = 10000, lmerTest.limit = 10000)
emmeans(m2, pairwise ~ Species, pbkrtest.limit = 10000, lmerTest.limit = 10000)
```
:warning: cannot compute due to cov matrix

#### Plot main-effects

:books:`library(effects)`
```
plot(Effect(c("Species", "body_size"), m2),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Dist_range_month](/Plots/Dist_range_month_1.png "Dist_range_month")

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
Slope of body_size              -0.70     0.32    -2.18   0.04
Conditional intercept         1104.05   127.40     8.67   0.00

When Species = pikeperch:

                                  Est.     S.E.   t val.      p
--------------------------- ---------- -------- -------- ------
Slope of body_size               -5.20     1.82    -2.85   0.01
Conditional intercept         -1128.01   755.16    -1.49   0.15

When Species = pike:

                                 Est.    S.E.   t val.      p
--------------------------- --------- ------- -------- ------
Slope of body_size               5.41    0.06    86.17   0.00
Conditional intercept         2271.82   21.93   103.58   0.00
```
- The slopes of _body_size_ are significant for the three species (p < 0.05) , negative for _pikeperch_and _wels_, and positive for _pike_

#### Plot the Species x body_size interaction

First, we will re-fit **Model 2* with a gamma distribution. We need to re-value 0s to values > 0 (e.g., 0.0001) before re-fitting the model
```
data_distr<-data_distr %>% mutate(dist.range = replace(dist.range, dist.range == 0, 0.0001))
```
```
m2_gamma<-glmer(dist.range ~ 1 + body_size * Species + (month| Species:fi_fishid)+(month| Species),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```
```
summ(m2_gamma)
```
```
MODEL INFO:
Observations: 131
Dependent Variable: dist.range
Type: Mixed effects generalized linear regression
Error Distribution: Gamma
Link function: log

MODEL FIT:
AIC = -116.06, BIC = -9.68

FIXED EFFECTS:
---------------------------------------------------------------
                                    Est.   S.E.   t val.      p
-------------------------------- ------- ------ -------- ------
(Intercept)                         5.97   0.64     9.35   0.00
body_size                           0.00   0.00     2.75   0.01
Speciespikeperch                    3.27   1.62     2.02   0.04
Specieswels                         1.53   0.91     1.68   0.09
body_size:Speciespikeperch         -0.01   0.00    -1.66   0.10
body_size:Specieswels              -0.00   0.00    -2.11   0.03
---------------------------------------------------------------

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)     0.56
 Species:fi_fishid     season1       0.77
 Species:fi_fishid     season2       0.96
 Species:fi_fishid     season3       0.75
 Species:fi_fishid     season4       0.83
      Species        (Intercept)     0.16
      Species          season1       0.53
      Species          season2       0.29
      Species          season3       0.40
      Species          season4       0.53
     Residual                        0.00
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      0.92
      Species           3       0.08
-------------------------------------
```
```
interact_plot(m2_gamma, pred = body_size, modx = Species, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on travel distance")
```
![Dist_range_month](/Plots/Dist_range_month_3.png "Dist_range_month")




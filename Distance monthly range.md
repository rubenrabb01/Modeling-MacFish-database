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

Convert _month_ into a time vector of repeated-measures and code it as integer to allow correct ordering (from months 10-12 2018)
In addition, we will include this time variable both as random-effects and fixed-effects and given its number of time points vector it should be coded as integer
```
data_distr$month<-revalue(data_distr$month, c("5_2017"="0","6_2017"="1","7_2017"="2","8_2017"="3","9_2017"="4","10_2017"="5","11_2017"="6","12_2017"="7","1_2018"="8","2_2018"="9","3_2018"="10"))
data_distr$month <- as.integer(data_distr$month)
```
```
data_distr$res_part <- as.factor(data_distr$res_part)
data_distr$fi_fishid <- as.factor(data_distr$fi_fishid)
data_distr$Species <- as.factor(data_distr$Species)
```
Order by _fi_fishid_ and _month_
```
data_distr<-data_distr[with(data_distr, order(fi_fishid, month)),]
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

Fit models with setting **REML=FALSE**

```
m1<-lmer(dist.range ~ 1 + body_size + month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m2<-lmer(dist.range ~ 1 + body_size * month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m3<-lmer(dist.range ~ 1 + Species + month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m4<-lmer(dist.range ~ 1 + Species * month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m5<-lmer(dist.range ~ 1 + body_size + Species + month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m6<-lmer(dist.range ~ 1 + body_size + Species * month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m7<-lmer(dist.range ~ 1 + body_size * Species + month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m8<-lmer(dist.range ~ 1 + body_size * Species * month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m9<-lmer(dist.range ~ 1 + body_size * Species + Species * month + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m10<-lmer(dist.range ~ 1 + body_size + month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m11<-lmer(dist.range ~ 1 + body_size * month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m12<-lmer(dist.range ~ 1 + Species + month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m13<-lmer(dist.range ~ 1 + Species * month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m14<-lmer(dist.range ~ 1 + body_size + Species + month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m15<-lmer(dist.range ~ 1 + body_size + Species * month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m16<-lmer(dist.range ~ 1 + body_size * Species + month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m17<-lmer(dist.range ~ 1 + body_size * Species * month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m18<-lmer(dist.range ~ 1 + body_size * Species + Species * month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
```
bic = BIC(m1,m2,m3,m4,m5,m6,m7,m8,m9,m10,m11,m12,m13,m14,m15,m16,m17,m18)
sortScore(bic , score = "bic")
```
```
  df      BIC
m1  11 5071.975
m10 12 5072.122
m16 16 5075.369
m2  12 5077.499
m11 13 5077.640
m12 13 5078.521
m7  15 5078.874
m18 18 5079.177
m3  12 5080.816
m14 14 5080.956
m5  13 5081.379
m4  14 5081.611
m13 15 5082.100
m9  17 5082.400
m15 16 5084.480
m6  15 5084.752
m17 21 5095.551
m8  20 5098.824
```
Export table of the six best models

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

Compare best-fit models

```
lrtest(m1,m10)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size + month + (1 + month | Species:fi_fishid) +
    (1 + month | Species) + (1 | month)
Model 2: dist.range ~ 1 + body_size + month + day_count + (1 + month |
    Species:fi_fishid) + (1 + month | Species) + (1 | month)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  11 -2504.9
2  12 -2502.1  1 5.5131    0.01887 *
```
```
lrtest(m1,m16)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size + month + (1 + month | Species:fi_fishid) +
    (1 + month | Species) + (1 | month)
Model 2: dist.range ~ 1 + body_size * Species + month + day_count + (1 +
    month | Species:fi_fishid) + (1 + month | Species) + (1 |
    month)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  11 -2504.9
2  16 -2492.4  5 24.904  0.0001454 ***
```
```
lrtest(m2,m10)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size * month + (1 + month | Species:fi_fishid) +
    (1 + month | Species) + (1 | month)
Model 2: dist.range ~ 1 + body_size + month + day_count + (1 + month |
    Species:fi_fishid) + (1 + month | Species) + (1 | month)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  12 -2504.8
2  12 -2502.1  0 5.3777  < 2.2e-16 ***
```
```
lrtest(m2,m16)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size * month + (1 + month | Species:fi_fishid) +
    (1 + month | Species) + (1 | month)
Model 2: dist.range ~ 1 + body_size * Species + month + day_count + (1 +
    month | Species:fi_fishid) + (1 + month | Species) + (1 |
    month)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  12 -2504.8
2  16 -2492.4  4 24.768    5.6e-05 ***
```
```
lrtest(m16,m10)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + body_size * Species + month + day_count + (1 +
    month | Species:fi_fishid) + (1 + month | Species) + (1 |
    month)
Model 2: dist.range ~ 1 + body_size + month + day_count + (1 + month |
    Species:fi_fishid) + (1 + month | Species) + (1 | month)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1  16 -2492.4
2  12 -2502.1 -4 19.391  0.0006585 ***
```

- **Model 10** and **Model 16** are preferred to the first model and to **Model 2** including the _body_size_ x month_ interaction
- **Model 16** is better than **Model 10**, thus the addition of the _Species x body_size_ interaction improves the model fit

### 3. Analysis of the effects of body size on monthly distance range travelled by each species

Looking at the summary results for the best-fit model we see the _Species_ x _body_size_ interaction is significant
```
summ(m16)
```
```
MODEL INFO:
Observations: 287
Dependent Variable: dist.range
Type: Mixed effects linear regression

MODEL FIT:
AIC = 5016.82, BIC = 5075.37
Pseudo-R² (fixed effects) = 0.17
Pseudo-R² (total) = 0.47

FIXED EFFECTS:
------------------------------------------------------------------------------
                                       Est.      S.E.   t val.     d.f.      p
-------------------------------- ---------- --------- -------- -------- ------
(Intercept)                        -6696.69   2067.53    -3.24   106.79   0.00
body_size                              5.75      1.06     5.43    23.92   0.00
Speciespikeperch                    6041.09   2041.44     2.96    26.11   0.01
Specieswels                         3448.35   1112.36     3.10    24.49   0.00
month                                -30.45    103.69    -0.29     4.69   0.78
day_count                            177.40     57.08     3.11   161.11   0.00
body_size:Speciespikeperch           -11.01      3.98    -2.77    25.26   0.01
body_size:Specieswels                 -5.71      1.24    -4.61    24.04   0.00
------------------------------------------------------------------------------

p values calculated using Satterthwaite d.f.

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)    1142.27
 Species:fi_fishid      month       131.98
       month         (Intercept)    525.33
      Species        (Intercept)    930.33
      Species           month       143.16
     Residual                       1276.80
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      0.32
       month            11      0.07
      Species           3       0.21
-------------------------------------
```

#### Calculate Level-2 and Level-3 ICC indices

:books:`library(sjstats)`
```
iccm.1st <- icc(m16)
print(iccm.1st)
print(iccm.1st, comp = "var")
```
```
# Intraclass Correlation Coefficient

     Adjusted ICC: 0.355
  Conditional ICC: 0.293
> print(iccm.1st, comp = "var")
# Intraclass Correlation Coefficient

     Adjusted ICC: 0.355
  Conditional ICC: 0.293
```

Between levels 1 and 2
```
sum(get_re_var(m16)) / (sum(get_re_var(m16)) + get_re_var(m16, "sigma_2"))
```
Between levels 2 and 3
```
get_re_var(m16)[2] / sum(get_re_var(m16))
```
:warning: There is an issue with the _sjstats_ package

:new: It seems that the _get_re_var_ function is no longer in library _sjstats_ but in library _insight_ (insight::get_variance())

#### Pairwise comparisons

:books:`library(emmeans)`
```
emtrends(m16, pairwise ~ Species, var = "body_size",pbkrtest.limit = 10000, lmerTest.limit = 10000)
```
```
$emtrends
 Species   body_size.trend    SE   df lower.CL upper.CL
 pike               5.7477 1.258 33.4     3.19     8.31
 pikeperch         -5.2673 4.556 34.2   -14.53     3.99
 wels               0.0367 0.771 32.9    -1.53     1.61

Degrees-of-freedom method: kenward-roger
Confidence level used: 0.95

$contrasts
 contrast         estimate   SE   df t.ratio p.value
 pike - pikeperch    11.01 4.73 34.1  2.331  0.0649
 pike - wels          5.71 1.47 33.2  3.878  0.0013
 pikeperch - wels    -5.30 4.62 34.1 -1.148  0.4919

Degrees-of-freedom method: kenward-roger
P value adjustment: tukey method for comparing a family of 3 estimates
```
```
emmeans(m16, pairwise ~ Species, pbkrtest.limit = 10000, lmerTest.limit = 10000)
```
```
$emmeans
 Species   emmean   SE  df lower.CL upper.CL
 pike        3511 1485 299      588     6434
 pikeperch   -330 2419  37    -5232     4572
 wels        1836 1466 212    -1055     4726

Degrees-of-freedom method: kenward-roger
Confidence level used: 0.95

$contrasts
 contrast         estimate   SE    df t.ratio p.value
 pike - pikeperch     3841 2782  37.1  1.381  0.3610
 pike - wels          1675 1971 129.5  0.850  0.6727
 pikeperch - wels    -2166 2763  36.6 -0.784  0.7151

Degrees-of-freedom method: kenward-roger
P value adjustment: tukey method for comparing a family of 3 estimates
```

#### Plot main-effects

:books:`library(effects)`
```
plot(Effect(c("Species", "body_size"), m16),lines=list(multiline=TRUE), rug = FALSE, layout=c(1, 1))
```
![Dist_range_month](/Plots/Dist_range_month_1.png "Dist_range_month")

**Note** that similar linear predictions can be obtained with the _emmip()_ function in _emmeans_ library as follows:
```
emmip(m16, Species ~ body_size, cov.reduce = range, pbkrtest.limit = 10000, lmerTest.limit = 10000))
```

#### Simple slope analysis

In this analysis we estimate the slopes of body size trends for each Species, i.e., the moderator effects on distance range indicating the values at which the slopes are significant
```
probe_interaction(m16, pred = body_size, modx = Species, plot.points = FALSE, cond.int = TRUE, interval = TRUE,jnplot = FALSE ,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on travel distance")
```
```
SIMPLE SLOPES ANALYSIS

When Species = wels:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of body_size               0.03     0.65     0.04   0.96
Conditional intercept         1932.82   304.39     6.35   0.00

When Species = pikeperch:

                                 Est.      S.E.   t val.      p
--------------------------- --------- --------- -------- ------
Slope of body_size              -5.17      3.86    -1.34   0.19
Conditional intercept         -210.89   1614.71    -0.13   0.90

When Species = pike:

                                 Est.     S.E.   t val.      p
--------------------------- --------- -------- -------- ------
Slope of body_size               5.75     1.07     5.39   0.00
Conditional intercept         3335.42   331.31    10.07   0.00
```
- The slopes of _body_size_ are significant for the three species (p < 0.05) , negative for _pikeperch_and _wels_, and positive for _pike_

#### Plot the Species x body_size interaction

First, we will re-fit **Model 16** with a gamma distribution. We need to re-value 0s to values > 0 (e.g., 0.0001) before re-fitting the model
```
data_distr<-data_distr %>% mutate(dist.range = replace(dist.range, dist.range == 0, 0.0001))
```
```
m16_gamma<-glmer(dist.range ~ 1 + body_size * Species + month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```
```
summ(m16_gamma)
```
```
MODEL INFO:
Observations: 287
Dependent Variable: dist.range
Type: Mixed effects generalized linear regression
Error Distribution: Gamma
Link function: log

MODEL FIT:
AIC = 4905.17, BIC = 4963.72
Pseudo-R² (fixed effects) =  NA
Pseudo-R² (total) =  NA

FIXED EFFECTS:
---------------------------------------------------------------
                                    Est.   S.E.   t val.      p
-------------------------------- ------- ------ -------- ------
(Intercept)                         3.48   1.02     3.42   0.00
body_size                           0.00   0.00     4.24   0.00
Speciespikeperch                    3.53   1.23     2.87   0.00
Specieswels                         2.12   0.68     3.09   0.00
month                              -0.02   0.04    -0.48   0.63
day_count                           0.07   0.03     2.55   0.01
body_size:Speciespikeperch         -0.01   0.00    -2.48   0.01
body_size:Specieswels              -0.00   0.00    -3.80   0.00
---------------------------------------------------------------

RANDOM EFFECTS:
---------------------------------------------
       Group          Parameter    Std. Dev.
------------------- ------------- -----------
 Species:fi_fishid   (Intercept)     0.38
 Species:fi_fishid      month        0.05
       month         (Intercept)     0.16
      Species        (Intercept)     0.30
      Species           month        0.06
     Residual                        0.68
---------------------------------------------

Grouping variables:
-------------------------------------
       Group         # groups   ICC
------------------- ---------- ------
 Species:fi_fishid      31      0.20
       month            11      0.04
      Species           3       0.12
-------------------------------------
```
```
interact_plot(m16_gamma, pred = body_size, modx = Species, plot.points = TRUE,robust = "HC3", geom = "line", point.shape = TRUE,pred.labels = NULL,x.label = "Body size (cm)", y.label = "Mean distance range (m)",legend.main="Species", modx.labels=c("pike","pikeperch","wels"),main.title = "Effects of body size on travel distance")
```
![Dist_range_month](/Plots/Dist_range_month_3.png "Dist_range_month")

### Does body size determine monthly changes in distance range between species?

- For this analysis we can take **Model 17** including a three-way interaction between body size, month and species (and controlling for _day_count_, incl. as covariate)
- However, to test separate effects by month we need to convert the variable _month_ to factor and re-fit the model (let run a "Gamma" distr)
- Computing a model with so many levels in both RF and FE terms requires pretty much computation power and fitting time considerably extends
- This is not the object of these LMM analyses (i.e., assessing the RF covariance) but it is fine here for further exploring purposes

```
data_distr$month <- as.factor(data_distr$month)
```
```
m17_gamma<-glmer(dist.range ~ 1 + body_size * Species * month + day_count + (1 + month| Species:fi_fishid)+(1 + month| Species) + (1|month),data = data_distr, REML=F, control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit,family="Gamma"(link='log'))
```
```
summ(m17_gamma)
```

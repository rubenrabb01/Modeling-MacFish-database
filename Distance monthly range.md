# How does distance range change monthly?

:books:`library(jtools)`  
:books:`library(interactions)`   

## Create a dataframe including monthly range and reservoir parts

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
colnames(data_distr)[2] <- "time"
```
Convert the variable _mon_yr_ variable into a time vector of repeated-measures
```
data_distr$time<-revalue(data_distr$time, c("4_2017"="0","5_2017"="1","6_2017"="2","7_2017"="3","8_2017"="4","9_2017"="5","10_2017"="6","11_2017"="7", "12_2017"="8","1_2018"="9","2_2018"="10","3_2018"="11","4_2018"="12"))
```
Convert variables including _time_ to integer to allow correct ordering (from months 10-12 2018)
```
data_distr$time <- as.integer(data_distr$time)
data_distr$month <- as.factor(data_distr$month)
data_distr$res_part <- as.factor(data_distr$res_part)
data_distr$fi_fishid <- as.factor(data_distr$fi_fishid)
data_distr$fi_species <- as.factor(data_distr$fi_species)
```
Order by _fi_fishid_ and _time_
```
data_distr<-data_distr[with(data_distr, order(fi_fishid, time)),]
data_distr$time <- as.factor(data_distr$time)
```
```
data_distr
```
| fi_fishid | fi_species | time | dist.range | dam | middle | tributary | upper | body_size | ca_weight_g | day_count |
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

Summary of the variable  _dist_range_
```
ddply(data_distr,.(fi_species),summarize,mean=mean(dist.range),sd=sd(dist.range),nobs=length(unique(fi_fishid)))
```
```
  fi_species     mean       sd nobs
1       pike 2001.874 1759.052   10
2  pikeperch 1757.787 1598.057    7
3       wels 1781.509 1618.366   14
```
```
hist(data_distr$dist.range, breaks = 20)
```
![Dist_range](/Plots/Dist_range_hist.png "Dist_range")

Check for correlations between _distance_range_ and reservoir parts
```
corr<-cor(data_distr[,c(4,10,11,12,13)], use="pairwise", method="spearman")
corrplot(corr,method="number")
```
![Dist_range](/Plots/Dist_range_corr.png "Dist_range")

## Fit Linear Mixed-Effects Models (LMM) to data of monthly distance range and use of reservoir parts

Fit models using the subseted data to explore which RF fits better
```
m_id<-lmer(dist.range ~ 1  + (1| fi_fishid),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_sp<-lmer(dist.range ~ 1  + (1| fi_species) + (1| fi_fishid),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_mon<-lmer(dist.range ~ 1  + (1| time) + (1| fi_fishid),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_sp_mon<-lmer(dist.range ~ 1  + (1| fi_species) + (1| time),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_sp_mon<-lmer(dist.range ~ 1  + (1| time) + (1| fi_fishid) + (1| fi_species),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
```
Order models from best to worst fitting in terms of Akaike values (i.e., BIC)
```
bic = BIC(m_id,m_id_sp,m_id_mon,m_sp_mon,m_id_sp_mon)
sortScore(bic , score = "bic")
```
```
          df      BIC
m_id_mon     4 6064.718
m_id_sp_mon  5 6070.565
m_id         3 6087.228
m_id_sp      4 6093.075
m_sp_mon     4 6103.681
```
```
lrtest(m_id_mon,m_id_sp_mon)
```
```
Likelihood ratio test

Model 1: dist.range ~ 1 + (1 | time) + (1 | fi_fishid)
Model 2: dist.range ~ 1 + (1 | time) + (1 | fi_fishid) + (1 | fi_species)
  #Df  LogLik Df Chisq Pr(>Chisq)
1   4 -3020.7
2   5 -3020.7  1     0     0.9974
```
**Model m_id_sp_mon** is not significantly better than **Model m_id_mon** so we retain the later RF structure

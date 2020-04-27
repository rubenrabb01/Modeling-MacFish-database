# How does distance monthly range change?

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


arrange(data_distr,desc(mon_yr),fi_fishid)
```
```
data_distr
```
| fi_fishid | fi_species | mon_yr  | month | dist.range | dam | middle | tributary | upper | day_count | ca_tl_mm | ca_weight_g | fi_sex |
|-----------|------------|---------|-------|------------|-----|--------|-----------|-------|-----------|----------|-------------|--------|
| T449202_1 | pikeperch  | 10_2017 | 10    | 3609.092   | 0   | 3      | 9         | 27    | 31        | 430      | 605         | M      |
| T449202_1 | pikeperch  | 12_2017 | 12    | 3165.064   | 0   | 2      | 20        | 15    | 31        | 430      | 605         | M      |
| T449202_1 | pikeperch  | 1_2018  | 1     | 2573.331   | 0   | 3      | 23        | 12    | 31        | 430      | 605         | M      |
| T449202_1 | pikeperch  | 2_2018  | 2     | 2573.331   | 0   | 0      | 25        | 6     | 28        | 430      | 605         | M      |
| T449319_1 | wels       | 6_2017  | 6     | 3042.14    | 3   | 29     | 4         | 5     | 29        | 1070     | 7700        | M      |
| T449319_1 | wels       | 7_2017  | 7     | 5408.336   | 4   | 29     | 3         | 8     | 31        | 1070     | 7700        | M      |
| T449319_1 | wels       | 8_2017  | 8     | 2496.028   | 13  | 30     | 0         | 6     | 30        | 1070     | 7700        | M      |
| T449319_1 | wels       | 9_2017  | 9     | 1086.458   | 14  | 29     | 0         | 0     | 29        | 1070     | 7700        | M      |
| T449209_1 | pike       | 11_2017 | 11    | 577.9322   | 0   | 27     | 0         | 22    | 27        | 640      | 1750        | X      |
| T449209_1 | pike       | 12_2017 | 12    | 587.6172   | 1   | 31     | 0         | 21    | 31        | 640      | 1750        | X      |
| T449209_1 | pike       | 2_2018  | 2     | 4383.3361  | 5   | 27     | 0         | 22    | 28        | 640      | 1750        | X      |
| T449209_1 | pike       | 3_2018  | 3     | 5059.2163  | 17  | 22     | 2         | 12    | 31        | 640      | 1750        | X      |
```
Convert and rename variables for analysis
```
data_distr$mon_yr <- as.factor(data_distr$mon_yr)
data_distr$res_part <- as.factor(data_distr$res_part)
data_distr$fi_fishid <- as.factor(data_distr$fi_fishid)
data_distr$fi_species <- as.factor(data_distr$fi_species)
colnames(data_distr)[6] <- "body_size"
```
Summary of the variable  "dist_range"
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

## Fit Linear Mixed-Effects Models (LMM) to data of monthly distance range and use of reservoir parts

Fit models using the subseted data to explore which RF fits the data better
```
m_id<-lmer(dist.range ~ 1  + (1| fi_fishid),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_sp<-lmer(dist.range ~ 1  + (1| fi_species) + (1| fi_fishid),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_mon<-lmer(dist.range ~ 1  + (1| mon_yr) + (1| fi_fishid),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_sp_mon<-lmer(dist.range ~ 1  + (1| fi_species) + (1| mon_yr),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
m_id_sp_mon<-lmer(dist.range ~ 1  + (1| mon_yr) + (1| fi_fishid) + (1| fi_species),data = data_distr, REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), na.action=na.omit)
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

Model 1: dist.range ~ 1 + (1 | mon_yr) + (1 | fi_fishid)
Model 2: dist.range ~ 1 + (1 | mon_yr) + (1 | fi_fishid) + (1 | fi_species)
  #Df  LogLik Df Chisq Pr(>Chisq)
1   4 -3020.7
2   5 -3020.7  1     0     0.9974
```
**Model m_id_sp_mon** is not significantly better than **Model m_id_mon** so we retain the later RF structure

### 2. Fit a series of conditional _POGLMMs_ (incl. covariates) with the RFs structure of selected model (m4)

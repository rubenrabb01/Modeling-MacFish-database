### Load all necessary libraries

```
library(sjstats)
library(sjmisc)
library(sjPlot)
library(lsmeans)
library(multcompView)
library(tidyverse)
library(data.table)
library(RPostgreSQL)
library(maptools)
library(lubridate)
library(rgdal)
library(postGIStools)
library(ggplot2)
library(brglm)
library(brms)
library(interactions)
library(lmerTest)
library(effects)
library(lme4)
library(glmulti)
library(geepack)
library(rJava)
library(car)
library(languageR)
library(AICcmodavg)
library(MASS)
library(randomForest)
library(rpart)
library(randomForestExplainer)
library(scatterplot3d)
library(randomForestSRC)
library(missRanger)
library(rfUtilities) # to test model significance
library(caret) # to get leave-one
library(ggplot2)
library(MuMIn)
library(reshape2)
library(ggeffects)
library(jtools)
library(interactions) # All functionalities of the jtools library (sim_slope, probe_interactions, etc.) have been moved to this package
library(VGAM)
library(VGAMdata)
library(VIF)
library(lsr)
library(effsize)
library(rstan)
library(brm)
library(merTools)       ## for random-effects plot
library(rptR)        ###REPEATABILITY ESTIMATION FOR GAUSSIAN AND NON-GAUSSIAN DATA
library(car)         ###REGRESIÓN función Anova
library(corrplot)    ###CORRELACIONES
library(glmm)        ###GENERALIZED LINEAR MIXED MODELS
library(bestglm)     ###SELECCIÓN MODELOS LINEALES GENERALIZADOS
library(languageR)
library(ordinal)
library(Hmisc)
library(RColorBrewer)
library(tidyverse)
library(dplyr)
library(relaimpo)
library(MCMCglmm)    ###multivariate glm
```

### Connect to the server and import/load data

`con <-  dbConnect(drv = PostgreSQL(), dbname ="teridb", host="10.0.37.1", user= "teriuser", password = "t3r1us3r!")`

`start.summer.time <- as.POSIXct('2017-04-27 00:00:00', tz = "UTC")
end.summer.time <-  as.POSIXct('2017-11-20 23:59:59', tz="UTC")`

`start.winter.time <- as.POSIXct('2017-11-27 00:00:00', tz = "UTC")
end.winter.time <-  as.POSIXct('2018-05-01 23:59:59', tz="UTC")`

`setwd("~/Teri/longit_displacement")
dist2dam.dt <- data.table(read_csv("./data/Teri_dis2data_predatory_fullarray.csv"))`

### Extracting info for fish

`fish.info <- data.table(dbGetQuery(con, "SELECT ca_tl_mm, ca_weight_g, b.* FROM teri.capture a INNER JOIN teri.fish b ON a.fi_fishid = b.fi_fishid"))`
`fish.capture <- data.table(dbGetQuery(con, "SELECT ca_lat_catch, ca_lon_catch, b.* FROM teri.capture a INNER JOIN teri.fish b ON a.fi_fishid = b.fi_fishid"))`

### Calculation of range of distances (calculation only from fish we have more than 3 months of data)

#### min and max was set as 0.5% and 99.5% quantiles to reduce influence of single false positions

`range.d2d <- dist2dam.dt[day.count > 89, .(min.dist = quantile(distfromdam,0.005), max.dist = quantile(distfromdam,0.995) ), by = .(date =as.Date(dd_timestamp_utc), fi_fishid)]`

`range.d2d.lg.t <- melt(range.d2d, id= c("date", "fi_fishid"), measure.vars = c("min.dist",  "max.dist") )`

`range.d2d.lg <- merge(range.d2d.lg.t, fish.info, by= c("fi_fishid"))`

#### daily diffrence between min and max distance from dam 

`mean.ranged2d.t <- range.d2d[, .(ranged2d = max.dist-min.dist,meand2d = (min.dist +max.dist)/2), by = .(date, fi_fishid)]`

`mean.ranged2d <- merge(mean.ranged2d.t, fish.info, by= c("fi_fishid"))`

`mean.ranged2d[, fish.name := paste(ca_weight_g/1000,"kg ", fi_sex, sep="")]`

`mean.ranged2d[, month := month(date)]`

#### setting of seasons

`mean.ranged2d[date > "2017-04-25" & date <= "2017-06-20", season := "spring_I" ]`

`mean.ranged2d[date > "2017-06-20" & date <= "2017-09-20", season := "summer" ]`

`mean.ranged2d[date > "2017-09-20" & date <= "2017-12-20", season := "autumn" ]`

`mean.ranged2d[date > "2017-12-20" & date <= "2018-03-20", season := "winter" ]`

`mean.ranged2d[date > "2018-03-20" & date <= "2018-08-20", season := "spring_II"]`

`setkey(mean.ranged2d,ca_weight_g)`

#### calculation of difference between maximum and minimum distance as daily range

`full.range.season.t <-  range.d2d[, .(ranged2d = max(max.dist)-min(min.dist)), by = .( fi_fishid)]`

`full.range.season <- merge(full.range.season.t, fish.info, by= c("fi_fishid"))`

`full.range.season[, fish.name := paste(ca_weight_g/1000,"kg ", fi_sex, sep="")]`

`setkey(full.range.season,ca_weight_g)`

### Fit mixed-effects models 

`model.ranged2d <- lmer(sqrt(ranged2d+1) ~ fi_species*season + (1|fi_fishid), data =mean.ranged2d,
                       REML = T, control = lmerControl(optimizer = "bobyqa"))`

`model.ranged2d <- lmer(sqrt(ranged2d+1)~season + (1 + season|fi_fishid), data =mean.ranged2d[fi_species == "pikeperch"],
                       REML = T, control = lmerControl(optimizer = "bobyqa"))`

#### A more suitable model would be:

`model.ranged2d <- lmer(sqrt(ranged2d+1) ~ fi_species*season + (1 + fi_species|fi_fishid), data =mean.ranged2d,
                       , REML=T, control=lmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",
                       check.nobs.vs.nRE="ignore"))`
                       
- The output shows warnings of non-convergence so let's try a different optimizer such as the Nelder-Mead optimisation routine:
                      
'ibrary(optmix)' 
  
 `model.ranged2d <- lmer(sqrt(ranged2d+1) ~ fi_species*season + (1 + fi_species|fi_fishid), data =mean.ranged2d,
                       , REML = FALSE, control = lmerControl(optimizer ='optimx', optCtrl=list(method='L-BFGS-B')))`  
                       
- The result is the same using the *nlminb* method
  
- Therefore, we need to look at the origin of those issues and define a new more suitable model. 
Including fi_species both as a fixed effect and a random slope for fi_fishid is probably causing those warnings from 
the model running out of d.f. to estimate intercepts-slopes correlations. 

`model.ranged2d <- lmer(sqrt(ranged2d+1) ~ fi_species*season + (1 + fi_species|fi_fishid)+ (1 + season|fi_fishid), data =mean.ranged2d,
                       , REML = FALSE, control = lmerControl(optimizer ='optimx', optCtrl=list(method='L-BFGS-B')))`  
 
```
MODEL INFO:
Observations: 8846
Dependent Variable: sqrt(ranged2d + 1)
Type: Mixed effects linear regression 

MODEL FIT:
AIC = 70233.69, BIC = 70495.93
Pseudo-R² (fixed effects) = 0.08
Pseudo-R² (total) = 0.11 

FIXED EFFECTS:
---------------------------------------------------------------------------------
                                              Est.   S.E.   t val.    d.f.      p
----------------------------------------- -------- ------ -------- ------- ------
(Intercept)                                  26.10   2.79     9.34   21.26   0.00
fi_speciespikeperch                          -7.29   4.38    -1.66   17.47   0.11
fi_specieswels                                0.57   3.50     0.16   29.44   0.87
seasonspring_I                               -1.86   2.88    -0.65   32.87   0.52
seasonspring_II                              12.60   2.74     4.60   19.47   0.00
seasonsummer                                 -3.15   2.82    -1.12   31.46   0.27
seasonwinter                                  2.57   3.28     0.79   25.19   0.44
fi_speciespikeperch:seasonspring_I            7.65   4.36     1.75   31.70   0.09
fi_specieswels:seasonspring_I                 7.03   3.67     1.91   31.37   0.06
fi_speciespikeperch:seasonspring_II         -12.54   4.18    -3.00   17.24   0.01
fi_specieswels:seasonspring_II               -7.54   3.51    -2.15   18.86   0.04
fi_speciespikeperch:seasonsummer             12.93   4.34     2.98   31.46   0.01
fi_specieswels:seasonsummer                   4.19   3.64     1.15   30.82   0.26
fi_speciespikeperch:seasonwinter             -3.28   5.23    -0.63   25.79   0.54
fi_specieswels:seasonwinter                  -1.15   4.18    -0.27   24.88   0.79
---------------------------------------------------------------------------------

p values calculated using Satterthwaite d.f.

RANDOM EFFECTS:
-----------------------------------------------
    Group           Parameter        Std. Dev. 
------------- --------------------- -----------
  fi_fishid        (Intercept)         3.17    
  fi_fishid    fi_speciespikeperch     5.91    
  fi_fishid      fi_specieswels        3.25    
 fi_fishid.1       (Intercept)         7.78    
 fi_fishid.1     seasonspring_I        8.26    
 fi_fishid.1     seasonspring_II       6.59    
 fi_fishid.1      seasonsummer         8.38    
 fi_fishid.1      seasonwinter         9.22    
  Residual                             12.49   
-----------------------------------------------

Grouping variables:
-----------------------------
   Group     # groups   ICC  
----------- ---------- ------
 fi_fishid      31      0.04 
-----------------------------
```                                    
                       
`summary(model.ranged2d)`

```
Linear mixed model fit by maximum likelihood . t-tests use Satterthwaite's method ['lmerModLmerTest']
Formula: sqrt(ranged2d + 1) ~ fi_species * season + (1 + fi_species |      fi_fishid) + (1 + season | fi_fishid)
   Data: mean.ranged2d
Control: lmerControl(optimizer = "optimx", optCtrl = list(method = "L-BFGS-B"))

     AIC      BIC   logLik deviance df.resid 
 70233.7  70495.9 -35079.8  70159.7     8809 

Scaled residuals: 
    Min      1Q  Median      3Q     Max 
-3.8132 -0.6124 -0.0517  0.6297  4.7258 

Random effects:
 Groups      Name                Variance Std.Dev. Corr                   
 fi_fishid   (Intercept)          10.05    3.170                          
             fi_speciespikeperch  34.94    5.911   -0.79                  
             fi_specieswels       10.57    3.251   -1.00  0.79            
 fi_fishid.1 (Intercept)          60.51    7.779                          
             seasonspring_I       68.27    8.262   -0.69                  
             seasonspring_II      43.38    6.586   -0.63  0.64            
             seasonsummer         70.25    8.381   -0.37  0.57 -0.09      
             seasonwinter         85.03    9.221   -0.50  0.40  0.82  0.09
 Residual                        156.10   12.494                          
Number of obs: 8846, groups:  fi_fishid, 31

Fixed effects:
                                    Estimate Std. Error       df t value Pr(>|t|)    
(Intercept)                          26.0954     2.7943  21.2618   9.339 5.65e-09 ***
fi_speciespikeperch                  -7.2913     4.3843  17.4743  -1.663 0.114133    
fi_specieswels                        0.5716     3.5020  29.4421   0.163 0.871468    
seasonspring_I                       -1.8643     2.8782  32.8713  -0.648 0.521656    
seasonspring_II                      12.6032     2.7382  19.4702   4.603 0.000183 ***
seasonsummer                         -3.1498     2.8230  31.4623  -1.116 0.272978    
seasonwinter                          2.5747     3.2766  25.1876   0.786 0.439337    
fi_speciespikeperch:seasonspring_I    7.6514     4.3636  31.7004   1.753 0.089187 .  
fi_specieswels:seasonspring_I         7.0295     3.6746  31.3714   1.913 0.064910 .  
fi_speciespikeperch:seasonspring_II -12.5442     4.1797  17.2448  -3.001 0.007938 ** 
fi_specieswels:seasonspring_II       -7.5362     3.5075  18.8626  -2.149 0.044869 *  
fi_speciespikeperch:seasonsummer     12.9292     4.3387  31.4564   2.980 0.005519 ** 
fi_specieswels:seasonsummer           4.1886     3.6394  30.8174   1.151 0.258634    
fi_speciespikeperch:seasonwinter     -3.2796     5.2278  25.7893  -0.627 0.535958    
fi_specieswels:seasonwinter          -1.1482     4.1798  24.8792  -0.275 0.785818    
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Correlation matrix not shown by default, as p = 15 > 12.
Use print(x, correlation=TRUE)  or
    vcov(x)        if you need it

convergence code: 0
boundary (singular) fit: see ?isSingular
```

`qqnorm(model.ranged2d)`

`plot(model.ranged2d)`

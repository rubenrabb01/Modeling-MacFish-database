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
Formula: sqrt(ranged2d + 1) ~ fi_species * season + (1 + fi_species | fi_fishid) + (1 + season | fi_fishid)
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

![M_r_1](/Plots/M_r_1.png "M_r_1")

### Let's try a model including the interaction with the variable fish length 

`m_r_2 <- lmer(sqrt(ranged2d+1) ~ ca_tl_mm*fi_species + (1 + season|fi_fishid), data =mean.ranged2d,REML = T, control = lmerControl(optimizer = "bobyqa"))`

`summ(m_r_2)`

```
MODEL INFO:
Observations: 8846
Dependent Variable: sqrt(ranged2d + 1)
Type: Mixed effects linear regression 

MODEL FIT:
AIC = 70242.75, BIC = 70398.68 

FIXED EFFECTS:
--------------------------------------------------------------------------
                                      Est.    S.E.   t val.    d.f.      p
---------------------------------- ------- ------- -------- ------- ------
(Intercept)                          -6.36    7.45    -0.85   23.77   0.40
ca_tl_mm                              0.04    0.01     4.46   22.93   0.00
fi_speciespikeperch                  55.23   18.67     2.96   24.02   0.01
fi_specieswels                       46.98   10.52     4.46   23.39   0.00
ca_tl_mm:fi_speciespikeperch         -0.10    0.04    -2.85   23.73   0.01
ca_tl_mm:fi_specieswels              -0.05    0.01    -4.57   23.02   0.00
--------------------------------------------------------------------------

p values calculated using Satterthwaite d.f.

RANDOM EFFECTS:
-----------------------------------------
   Group        Parameter      Std. Dev. 
----------- ----------------- -----------
 fi_fishid     (Intercept)       7.66    
 fi_fishid   seasonspring_I      9.40    
 fi_fishid   seasonspring_II     11.75   
 fi_fishid    seasonsummer       9.67    
 fi_fishid    seasonwinter       8.97    
 Residual                        12.50   
-----------------------------------------

Grouping variables:
-----------------------------
   Group     # groups   ICC  
----------- ---------- ------
 fi_fishid      31      0.27 
-----------------------------
Warning message:
In summ.merMod(m_r_2) :
  Could not calculate r-squared. Try removing missing data
before fitting the model.
```
`probe_interaction(m_r_2, modx  = fi_species, pred = ca_tl_mm, plot.points=TRUE,cond.int = TRUE, interval = TRUE,jnplot = TRUE)`

```
Using data mean.ranged2d from global environment. This could cause incorrect results if mean.ranged2d has been altered since the model was fit. You can manually provide the
data to the "data =" argument.
Using data mean.ranged2d from global environment. This could cause incorrect results if mean.ranged2d has been altered since the model was fit. You can manually provide the
data to the "data =" argument.
SIMPLE SLOPES ANALYSIS 

When fi_species = wels: 

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of ca_tl_mm             -0.01   0.01    -1.46   0.16
Conditional intercept         32.62   2.36    13.83   0.00

When fi_species = pike: 

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of ca_tl_mm              0.04   0.01     4.46   0.00
Conditional intercept         33.73   2.60    12.96   0.00

When fi_species = pikeperch: 

                               Est.    S.E.   t val.      p
--------------------------- ------- ------- -------- ------
Slope of ca_tl_mm             -0.06    0.04    -1.69   0.10
Conditional intercept         -4.58   14.77    -0.31   0.76

Warning messages:
1: Johnson-Neyman intervals are not available for factor moderators. 
2: In summ.merMod(model, confint = TRUE, ci.width = ci.width, vifs = FALSE,  :
  Could not calculate r-squared. Try removing missing data
before fitting the model.

3: In summ.merMod(newmod, model.fit = FALSE, confint = TRUE, ci.width = ci.width,  :
  Could not calculate r-squared. Try removing missing data
before fitting the model.

4: In summ.merMod(newmod, model.fit = FALSE, confint = TRUE, ci.width = ci.width,  :
  Could not calculate r-squared. Try removing missing data
before fitting the model.

5: In summ.merMod(newmod, model.fit = FALSE, confint = TRUE, ci.width = ci.width,  :
  Could not calculate r-squared. Try removing missing data
before fitting the model.

```
![M_r_2_1](/Plots/M_r_2_1.png "M_r_2_1")


## MULTIMODEL SELECTION AND INFERENCE

### FIT MIXED-EFFECTS MODEL WITH LMER (RANDOM EFFECTS)

- Need to convert to factor to prevent errors:

`mean.ranged2d$fi_species <- as.factor(mean.ranged2d$fi_species)`
`mean.ranged2d$season <- as.factor(mean.ranged2d$season)`

- Transfom character variable *fi_fishid* to a numeric index:

`mean.ranged2d$fi_fishid <- as.numeric(factor(mean.ranged2d$fi_fishid, levels=unique(mean.ranged2d$fi_fishid)))`

- Define mixed-effects function:

`mixed.glmulti<-function(formula,data,random="",...){lmer(paste(deparse(formula),random),data=mean.ranged2d,REML=F,...)}`

- Apply correction to *getfit* function in order to allow integration between glmulti and lme4:

```
setMethod('getfit', 'merMod', function(object, ...) {
summ=summary(object)$coef
summ1=summ[,1:2]
if (length(dimnames(summ)[[1]])==1) {
    summ1=matrix(summ1, nr=1, dimnames=list(c("(Intercept)"),c("Estimate","Std. Error")))
}
cbind(summ1, df=rep(10000,length(fixef(object))))
})
```
**Note**: *Using lmer in glmulti makes the coef() function invalid. The above is a bugfix correction that needs to be ran before running multi-model selection and inference


- Define glmulti formula for fixed effects:

`model.glmulti = as.formula(paste("sqrt(ranged2d+1)~ 1 + fi_species + season + ca_tl_mm"))`

- Define a candidate models object and run multi-model selection:

`glmulti.cand.mod <- glmulti(model.glmulti,random="+(date|fi_fishid)",data=mean.ranged2d,
method="h",fitfunc=mixed.glmulti, intercept=TRUE,marginality=TRUE,level=2,crit=aicc,bunch=3000,confsetsize = 150, plotty = F, report = T,
control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"),na.action=na.omit)`

**Note**: *We have selected an exhaustive screening method (method="h") and specification for all possible interactions between variables (level=2)*

### SUMMARY RESULTS

- Importance variable and unconditional variance estimates:

`coef(glmulti.cand.mod)`

```
                                              Estimate   Uncond. variance Nb models        Importance  +/- (alpha=0.05)
ca_tl_mm:seasonspring_I              1.76124825953e-57 1.71990731173e-113         2 8.21388536861e-55 8.12930056346e-57
ca_tl_mm:seasonspring_II            -1.22599562048e-57 1.41576847282e-113         2 8.21388536861e-55 7.37559019073e-57
ca_tl_mm:seasonsummer               -4.04089503077e-57 6.78148486205e-113         2 8.21388536861e-55 1.61422124151e-56
ca_tl_mm:seasonwinter                7.33287815890e-58 4.52788516209e-114         2 8.21388536861e-55 4.17107848059e-57
ca_tl_mm:fi_speciespikeperch         2.19540299132e-37  1.95109120125e-73         3 3.63599178768e-38 8.65843587275e-37
ca_tl_mm:fi_specieswels             -7.31054255785e-38  2.15991735036e-74         3 3.63599178768e-38 2.88084058112e-37
ca_tl_mm                             2.51074054202e-13  2.70722764153e-23        13 2.70577660459e-10 1.01991281205e-11
(Intercept)                          2.61552563251e+01  4.49678737705e+00        18 1.00000000000e+00 4.15673019417e+00
fi_speciespikeperch                 -6.91458911936e+00  1.09621149966e+01        13 1.00000000000e+00 6.49004691763e+00
fi_specieswels                       7.53688698731e-01  7.66969438235e+00        13 1.00000000000e+00 5.42862308213e+00
seasonspring_I                      -1.78335008444e+00  7.16543756942e-01        13 1.00000000000e+00 1.65928893767e+00
seasonspring_II                      1.47140444790e+01  2.99868263631e+00        13 1.00000000000e+00 3.39442261349e+00
seasonsummer                        -3.27096265441e+00  5.05706451425e-01        13 1.00000000000e+00 1.39395869955e+00
seasonwinter                         3.65412808772e+00  5.96397368876e-01        13 1.00000000000e+00 1.51380006298e+00
fi_speciespikeperch:seasonspring_I   7.53080204284e+00  1.68016417905e+00         5 1.00000000000e+00 2.54083533375e+00
fi_specieswels:seasonspring_I        6.81287029896e+00  1.12863658382e+00         5 1.00000000000e+00 2.08246504959e+00
fi_speciespikeperch:seasonspring_II -1.37377787038e+01  6.45921870177e+00         5 1.00000000000e+00 4.98185008285e+00
fi_specieswels:seasonspring_II      -1.04744084707e+01  4.85249405666e+00         5 1.00000000000e+00 4.31800524604e+00
fi_speciespikeperch:seasonsummer     1.29330641692e+01  1.24290338269e+00         5 1.00000000000e+00 2.18534165807e+00
fi_specieswels:seasonsummer          4.32905800498e+00  8.14273959919e-01         5 1.00000000000e+00 1.76882933769e+00
fi_speciespikeperch:seasonwinter    -3.46463971852e+00  1.47792433304e+00         5 1.00000000000e+00 2.38301490839e+00
fi_specieswels:seasonwinter         -2.91402369630e+00  9.50449610609e-01         5 1.00000000000e+00 1.91101989384e+00

```
- Model metrics

`plot(allEffects(glmulti.cand.mod@objects[[1]]),style="stacked",colors = c("black", "grey", "white"), rug = FALSE)
dev.off()`

![M_r_2_3](/Plots/M_r_2_3.png "M_r_2_3")

`plot(glmulti.cand.mod, type = "p")`

![M_r_2_4](/Plots/M_r_2_4.png "M_r_2_4")

`plot(glmulti.cand.mod, type = "w")`

![M_r_2_5](/Plots/M_r_2_5.png "M_r_2_5")

`plot(glmulti.cand.mod, type = "s")`

![M_r_2_6](/Plots/M_r_2_6.png "M_r_2_6")

`options(digits=12)`

`best.model<-glmulti.cand.mod@objects[[1]]`

`print(glmulti.cand.mod@objects[[1]])`

```
Linear mixed model fit by maximum likelihood  ['lmerModLmerTest']
Formula: paste(deparse(formula), random)
   Data: mean.ranged2d
        AIC         BIC      logLik    deviance    df.resid
 71518.5943  71653.2610 -35740.2971  71480.5943        8827
Random effects:
 Groups    Name        Std.Dev.      Corr
 fi_fishid (Intercept) 19.6975820906
           date         0.0014994607 -1.0000000
 Residual              13.6547759192
Number of obs: 8846, groups:  fi_fishid, 31
Fixed Effects:
                        (Intercept)                  fi_speciespikeperch                       fi_specieswels                       seasonspring_I                      seasonspring_II
                       26.155256326                         -6.914589120                          0.753688698                         -1.783350084                         14.714044479
                       seasonsummer                         seasonwinter   fi_speciespikeperch:seasonspring_I        fi_specieswels:seasonspring_I  fi_speciespikeperch:seasonspring_II
                       -3.270962654                          3.654128088                          7.530802043                          6.812870299                        -13.737778704
     fi_specieswels:seasonspring_II     fi_speciespikeperch:seasonsummer          fi_specieswels:seasonsummer     fi_speciespikeperch:seasonwinter          fi_specieswels:seasonwinter
                      -10.474408471                         12.933064169                          4.329058005                         -3.464639719                         -2.914023696
convergence code 0; 1 optimizer warnings; 0 lme4 warnings
```

`aiccvalues<-summary(glmulti.cand.mod)$icvalues`

`as.data.frame(summary(glmulti.cand.mod)$icvalues)`

´´´
   summary(glmulti.cand.mod)$icvalues
1                       71518.6803937
2                       71565.5139064
3                       71565.5139064
4                       71565.5139064
5                       71565.5139064
6                       71692.4813913
7                       71692.4813913
8                       71767.0335604
9                       71767.7531019
10                      71802.3614677
11                      71802.3643362
12                      71804.2740590
13                      71817.4141958
14                      71822.0853109
15                      71869.9082069
16                      71895.6250939
17                      71898.0593460
18                      71898.9819136
```

`model.weights<-summary(glmulti.cand.mod)$modelweights`

`plot(aiccvalues,model.weights)`

![M_r_2_7](/Plots/M_r_2_7.png "M_r_2_7")

`print(glmulti.cand.mod)`

```
glmulti.analysis
Method: h / Fitting: mixed.glmulti / IC used: aicc
Level: 2 / Marginality: TRUE
From 18 models:
Best IC: 71518.6803937446
Best model:
[1] "sqrt(ranged2d + 1) ~ 1 + fi_species + season + season:fi_species"
Evidence weight: 0.999999999729422
Worst IC: 71898.9819135795
1 models within 2 IC units.
0 models to reach 95% of evidence weight.
```


### Load all necessary libraries

```
library(sjstats)
library(sjmisc)
library(sjPlot)
library(lsmeans)
library(multcompView)
library(maptools)
library(lubridate)
library(rgdal)
library(postGIStools)
library(ggplot2)
library(brglm)
library(brms)
library(effects)
library(lme4)
library(geepack)
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
library(maptools)
library(lubridate)
library(rgdal)
library(postGIStools)
library(ggplot2)
library(brglm)
library(MUMIn)
library(brms)
```
### Connect to the server and import/load data

📗 `library(tidyverse)`     

📗 `library(data.table)`  
   
📗 `library(RPostgreSQL)`
   
```
con <-  dbConnect(drv = PostgreSQL(), dbname ="teridb", host="10.0.37.1", user= "teriuser", password = "t3r1us3r!")

start.summer.time <- as.POSIXct('2017-04-27 00:00:00', tz = "UTC")

end.summer.time <-  as.POSIXct('2017-11-20 23:59:59', tz="UTC")

start.winter.time <- as.POSIXct('2017-11-27 00:00:00', tz = "UTC")

end.winter.time <-  as.POSIXct('2018-05-01 23:59:59', tz="UTC")

setwd("~/Teri/longit_displacement")

dist2dam.dt <- data.table(read_csv("./data/Teri_dis2data_predatory_fullarray.csv"))
```

### Extracting info for fish

```
fish.info <- data.table(dbGetQuery(con, "SELECT ca_tl_mm, ca_weight_g, b.* FROM teri.capture a INNER JOIN teri.fish b ON a.fi_fishid = b.fi_fishid"))

fish.capture <- data.table(dbGetQuery(con, "SELECT ca_lat_catch, ca_lon_catch, b.* FROM teri.capture a INNER JOIN teri.fish b ON a.fi_fishid = b.fi_fishid"))
```

### Calculation of range of distances (calculation only from fish we have more than 3 months of data)

#### min and max was set as 0.5% and 99.5% quantiles to reduce influence of single false positions

```
range.d2d <- dist2dam.dt[day.count > 89, .(min.dist = quantile(distfromdam,0.005), max.dist = quantile(distfromdam,0.995) ), by = .(date =as.Date(dd_timestamp_utc), fi_fishid)]

range.d2d.lg.t <- melt(range.d2d, id= c("date", "fi_fishid"), measure.vars = c("min.dist",  "max.dist"))

range.d2d.lg <- merge(range.d2d.lg.t, fish.info, by= c("fi_fishid"))
```

#### daily diffrence between min and max distance from dam 

```
mean.ranged2d.t <- range.d2d[, .(ranged2d = max.dist-min.dist,meand2d = (min.dist +max.dist)/2), by = .(date, fi_fishid)]

mean.ranged2d <- merge(mean.ranged2d.t, fish.info, by= c("fi_fishid"))

mean.ranged2d[, fish.name := paste(ca_weight_g/1000,"kg ", fi_sex, sep="")]

mean.ranged2d[, month := month(date)]
```

 #### setting of seasons

```
mean.ranged2d[date > "2017-04-25" & date <= "2017-06-20", season := "spring_I"]

mean.ranged2d[date > "2017-06-20" & date <= "2017-09-20", season := "summer"]

mean.ranged2d[date > "2017-09-20" & date <= "2017-12-20", season := "autumn"]

mean.ranged2d[date > "2017-12-20" & date <= "2018-03-20", season := "winter"]

mean.ranged2d[date > "2018-03-20" & date <= "2018-08-20", season := "spring_II"]

setkey(mean.ranged2d,ca_weight_g)
```

#### calculation of difference between maximum and minimum distance as daily range

```
full.range.season.t <- range.d2d[, .(ranged2d = max(max.dist)-min(min.dist)), by = .( fi_fishid)]

full.range.season <- merge(full.range.season.t, fish.info, by= c("fi_fishid"))

full.range.season[, fish.name := paste(ca_weight_g/1000,"kg ", fi_sex, sep="")]

setkey(full.range.season,ca_weight_g)
```

#### Calculation of body condition (residuals of the regression of body mass on body length)

```
condition <- lm(ca_weight_g~ca_tl_mm, mean.ranged2d, na.action=na.exclude)

summary(condition)

mean.ranged2d$r3_condition<-rstandard(condition)
```

#### Combine dfs by _fi_fishid_

```
fish.capture2 <- as.data.table(fish.capture[,1:3])

mean.ranged2d <- merge(mean.ranged2d,fish.capture2, by="fi_fishid")
```

## Are there differences in distance travelled between species and across seasons?

- Before fitting a model we need to pose some questions:

   - Do we have any evidence to think that distance should vary between species? And between individuals?

   - Should be there more variability between individuals than within individuals (repeated measures)?   

   - What model to fit? What fixed-efects and random-effects should we include? 

- What we know is taht we are interested in investigating differences in change over time between species. Since we have repeated-measures at the individual level we need to fir longitudinal mixed-effects models with either a random intercept and slope or a random intercept alone

### Fit different L2 mixed-effects models

📗 `library(lmerTest)`

- **Conditional non-growth model / Random intercept model**
 
```
 model.ranged2d_1 <- lmer(sqrt(ranged2d+1) ~ fi_species * season + (1|fi_fishid), data = mean.ranged2d,
                   REML = T, control = lmerControl(optimizer = "bobyqa"))
```

 - **Unconditional growth model  /  Random-slope-intercept model**
 
```
  model.ranged2d_2 <- lmer(sqrt(ranged2d+1) ~ 1 + date + (1 + date|fi_fishid), data = mean.ranged2d,
                   REML = T, control = lmerControl(optimizer = "bobyqa"))
                   
  model.ranged2d_2 <- lmer(sqrt(ranged2d+1) ~ 1 + season + (1 + season|fi_fishid), data = mean.ranged2d,
                   REML = T, control = lmerControl(optimizer = "bobyqa"))         
                   
  model.ranged2d_2 <- lmer(sqrt(ranged2d+1) ~ 1 + season + (1 + season|fi_species), data = mean.ranged2d,
                   REML = T, control = lmerControl(optimizer = "bobyqa"))                       
```
 
- **Conditional growth model (Random Intercept and Slope for One Level-1 Factor) / random-slope-intercept model for the species*season     interaction effect**
 
```
  model.ranged2d_3 <- lmer(sqrt(ranged2d+1) ~ 1 + season * fi_species + (1 + season|fi_fishid), data =mean.ranged2d,
                   REML = T, control = lmerControl(optimizer = "bobyqa"))
                   
  model.ranged2d_3 <- lmer(sqrt(ranged2d+1) ~ 1 + date * ca_tl_mm + (1 + date|fi_fishid), data =mean.ranged2d,
                   REML = T, control = lmerControl(optimizer = "bobyqa"))          
                   
  model.ranged2d_4 <- lmer(sqrt(ranged2d+1) ~ 1 + fi_species * ca_tl_mm + (1 + season|fi_species), data =mean.ranged2d,
                   REML = T, control = lmerControl(optimizer = "bobyqa"))                       
```

- **Conditional growth model random intercept model + dropping intercept-slope covariance**
 
```
   model.ranged2d_5 <- lmer(sqrt(ranged2d+1) ~ 1 + season * fi_species + (1 | fi_fishid) + (0 + season | fi_fishid)"), data         =mean.ranged2d, REML = T, control = lmerControl(optimizer = "bobyqa"))
   
   model.ranged2d_6 <- lmer(sqrt(ranged2d+1) ~ 1 + date * fi_species + (1 | fi_fishid) + (0 + date | fi_fishid)"), data       =mean.ranged2d, REML = T, control = lmerControl(optimizer = "bobyqa"))
   
   model.ranged2d_7 <- lmer(sqrt(ranged2d+1) ~ 1 + season * ca_tl_mm + (1 | fi_species) + (0 + season | fi_species)"), data       =mean.ranged2d, REML = T, control = lmerControl(optimizer = "bobyqa"))
```
```
2-level Longitudinal conditional growth model - Random intercept and slope / Random Intercept and Slope for One Level-1 Factor
m3 <- sim_formula("y ~ time * treatment + (1 + time | subject)")

#2-level Longitudinal conditional growth model  - dropping random intercept
m4 <- sim_formula("y ~ time * treatment + (0 + time | subject)")

#3-level Longitudinal conditional growth model - random slope + dropping random intercept
m5 <- sim_formula("y ~ time * treatment + (1 + time | subject) + (0 + time | cluster)")

#3-level Longitudinal conditional growth model - random intercept and slope model
m6 <- sim_formula("y ~ time * treatment + (1 + time | subject) + (1 + time | cluster)")

#3-level Longitudinal conditional (nested) growth model - random intercept and slope model
m7 <- sim_formula("y ~ time * treatment + (1 + time | cluster:subject) + (1 + time | cluster)")
```



```


model.ranged2d <- lmer(sqrt(ranged2d+1) ~ season + (1 + season|fi_fishid), data =mean.ranged2d[fi_species == "pikeperch"],
                   REML = T, control = lmerControl(optimizer = "bobyqa"))             
```

#### A more realistic model would be:

```
model.ranged2d <- lmer(sqrt(ranged2d+1) ~ fi_species*season + (1 + fi_species|fi_fishid), data =mean.ranged2d,
                    REML=T, control=lmerControl(optimizer = "bobyqa", check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",
                    check.nobs.vs.nRE="ignore"))
```

- The output shows warnings of non-convergence so let's try a different optimizer such as the Nelder-Mead optimisation routine:

📗 `library(optmix)`
  
 ```
 model.ranged2d <- lmer(sqrt(ranged2d+1) ~ fi_species*season + (1 + fi_species|fi_fishid), data =mean.ranged2d,
                       , REML = FALSE, control = lmerControl(optimizer ='optimx', optCtrl=list(method='L-BFGS-B')))
                                
```
- The result is the same using the *"nlminb"* method or the *"Nelder_Mead"* optimizer
  
- Therefore, we need to look at the origin of those issues and define a new more suitable model. 
Including fi_species both as a fixed effect and a random slope for *fi_fishid* is probably causing those warnings from 
the model running out of d.f. to estimate intercepts-slopes correlations. 

```
model.ranged2d <- lmer(sqrt(ranged2d+1) ~ fi_species*season + (1 + fi_species|fi_fishid)+ (1 + season|fi_fishid), data =mean.ranged2d,
                   , REML = FALSE, control = lmerControl(optimizer ='optimx', optCtrl=list(method='L-BFGS-B')))
``` 

### Results summary with transformation of variables

📗 `library(jtools)`

📗 `library(interactions)`       # jtools is now deprecated and all its functionalities have passed to the interactions package

#### Transform and standardize the model results (obtain beta coefficients estimates, instead of frequentist Est ± SE)

```
summ(model.ranged2d)                              # default summary output without standardising/transforming variables

summ(m2, center = TRUE)                           # input variables are mean-centered

summ(m2, robust = "HC1")                          # report robust standard errors (default is "HC3")

summ(m2, scale = TRUE)                            # scaled predictors/coefficients

summ(m2, confint = TRUE, digits = 3)              # otain confidence interval

summ(m2,  center = TRUE, scale = TRUE, n.sd = 2)  # standardized/scaled coefficients dividing by 2 s.d. units (Gelman 2008)
```
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
```
summary(model.ranged2d)
```
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
```
qqnorm(model.ranged2d)

plot(model.ranged2d)
```

![M_r_1](/Plots/M_r_1.png "M_r_1")

### Let's try a model including the interaction with the variable fish length 

```
m_r_2 <- lmer(sqrt(ranged2d+1) ~ ca_tl_mm*fi_species + (1 + season|fi_fishid), data =mean.ranged2d,REML = T, control = lmerControl(optimizer = "bobyqa"))

summ(m_r_2)
```

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
### Simple slope analysis
```
probe_interaction(m_r_2, modx  = fi_species, pred = ca_tl_mm, plot.points=TRUE,cond.int = TRUE, interval = TRUE,jnplot = TRUE)
```

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

- The slope of body length is positive and significantly different from zero in pike but not wels and pikeperch, indicating that larger body lengths are associated with higher mean range distances travelled only in pike

🔴 However, if you look at the plot, negative values denote that the linearity of the model is not typical for this data and therefore, you we should re-fit the model using a corrected distribution for the distance range variable


## MULTI-MODEL SELECTION AND INFERENCE

📗 `library(rJava)`

📗 `library(glmulti)`

### Search best possible mixed-effects models fitted to the data

Need to convert to factor to prevent errors:

```
mean.ranged2d$fi_species <- as.factor(mean.ranged2d$fi_species)

mean.ranged2d$season <- as.factor(mean.ranged2d$season)
```

Transfom character variable *fi_fishid* to a numeric index:

```
mean.ranged2d$fi_fishid <- as.numeric(factor(mean.ranged2d$fi_fishid, levels=unique(mean.ranged2d$fi_fishid)))
```

Define a mixed-effects function:

```
mixed.glmulti<-function(formula,data,random="",...){lmer(paste(deparse(formula),random),data=mean.ranged2d,REML=T,...)}
```

Apply correction to *getfit* function in order to allow integration between *glmulti* and *lme4*:

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
🔺 _Using lmer in glmulti makes the coef() function invalid. The above is a bugfix correction that needs to be ran before running multi-model selection and inference_

Define glmulti formula for fixed effects:

```
model.glmulti = as.formula(paste("sqrt(ranged2d+1) ~ 1 + fi_species + season + ca_tl_mm"))
```

Define a candidate models object and run multi-model selection and inference:

```
glmulti.cand.mod <- glmulti(model.glmulti,random="+(date|fi_fishid)",data=mean.ranged2d,
method="h",fitfunc=mixed.glmulti, intercept=TRUE,marginality=TRUE,level=2,crit=aicc,bunch=3000,confsetsize = 150, plotty = F, report = T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"),na.action=na.omit)
```

**Note**: _We have selected an exhaustive screening method (method="h") and specification for all possible interactions between variables (level=2)_

### Results summary

Obtain average estimates of terms: return coefficients and unconditional variance estimates

```
coef(glmulti.cand.mod)
```

```
                                              Estimate   Uncond. variance Nb models        Importance  +/- (alpha=0.05)
ca_tl_mm:seasonspring_I              3.97388107192e-70 8.93180574208e-139         2 1.89871428844e-67 1.85255151745e-69
ca_tl_mm:seasonspring_II            -2.63187445743e-70 7.09909231322e-139         2 1.89871428844e-67 1.65158934093e-69
ca_tl_mm:seasonsummer               -9.39477417751e-70 3.66635322182e-138         2 1.89871428844e-67 3.75333917015e-69
ca_tl_mm:seasonwinter                1.75580226775e-70 2.52930032818e-139         2 1.89871428844e-67 9.85827343300e-70
ca_tl_mm:fi_speciespikeperch         7.33808778808e-35  2.17965680905e-68         3 1.21739675047e-35 2.89397462511e-34
ca_tl_mm:fi_specieswels             -2.44474126601e-35  2.41532994843e-69         3 1.21739675047e-35 9.63360627480e-35
ca_tl_mm                             2.54984596247e-03  6.79228396711e-05        13 9.99996909539e-01 1.61550600515e-02
(Intercept)                          2.40307208729e+01  4.32897637861e+01        18 1.00000000000e+00 1.28971356468e+01
fi_speciespikeperch                 -5.76886593752e+00  2.54313854746e+01        13 1.00000000000e+00 9.88520476678e+00
fi_specieswels                      -5.88001378356e-02  3.10445014729e+01        13 1.00000000000e+00 1.09217694488e+01
seasonspring_I                      -1.76402939590e+00  7.20398185679e-01        13 1.00000000000e+00 1.66374577170e+00
seasonspring_II                      1.47186050546e+01  3.00120584039e+00        13 1.00000000000e+00 3.39585041048e+00
seasonsummer                        -3.25310444437e+00  5.06948163563e-01        13 1.00000000000e+00 1.39566901413e+00
seasonwinter                         3.64967749749e+00  5.97291978216e-01        13 1.00000000000e+00 1.51493500444e+00
fi_speciespikeperch:seasonspring_I   7.50659025187e+00  1.68895469981e+00         5 1.00000000000e+00 2.54747341352e+00
fi_specieswels:seasonspring_I        6.79421452422e+00  1.13490353579e+00         5 1.00000000000e+00 2.08823867158e+00
fi_speciespikeperch:seasonspring_II -1.37366358811e+01  6.46524417298e+00         5 1.00000000000e+00 4.98417319640e+00
fi_specieswels:seasonspring_II      -1.04740151788e+01  4.85669544200e+00         5 1.00000000000e+00 4.31987414877e+00
fi_speciespikeperch:seasonsummer     1.29102922408e+01  1.24583762595e+00         5 1.00000000000e+00 2.18791971202e+00
fi_specieswels:seasonsummer          4.31169391795e+00  8.16253673398e-01         5 1.00000000000e+00 1.77097827625e+00
fi_speciespikeperch:seasonwinter    -3.45615550384e+00  1.48018037634e+00         5 1.00000000000e+00 2.38483304433e+00
fi_specieswels:seasonwinter         -2.91280872759e+00  9.52024929576e-01         5 1.00000000000e+00 1.91260294435e+00
```

### Plots and model metrics 

```
plot(allEffects(glmulti.cand.mod@objects[[1]]),style="stacked",colors = c("black", "grey", "white"), rug = FALSE)
```

![M_r_2_3](/Plots/M_r_2_3.png "M_r_2_3")

Plot the **IC profile** from the best to the worst models. The horizontal line is two units above the best model representing the optimal of candidate models set

```
plot(glmulti.cand.mod, type = "p")
```

![M_r_2_4](/Plots/M_r_2_4.png "M_r_2_4")

Relative likelihood, expressed by RL = exp **(-(IC(i)-IC(best)/2)**, is the difference between a model and the best (first) model (i.e., the odds that a model is the best in the whole set)

Plot the normalized evidence weights of the models. The vertical line delineates models that total 95% evidence weight. 

```
plot(glmulti.cand.mod, type = "w")
```

![M_r_2_5](/Plots/M_r_2_5.png "M_r_2_5")

Plot the relative evidence weight represents the estimated importance for each term according to their Akaike weights, given by the sum of the relative evidence weights of all the models in which the term appears

```
plot(glmulti.cand.mod, type = "s")
```

![M_r_2_6](/Plots/M_r_2_6.png "M_r_2_6")

Summary of best fitted model 

```
options(digits=12)

best.model<-glmulti.cand.mod@objects[[1]]

print(glmulti.cand.mod@objects[[1]])
```
```
Linear mixed model fit by REML ['lmerModLmerTest']
Formula: paste(deparse(formula), random)
   Data: mean.ranged2d
REML criterion at convergence: 71465.0136
Random effects:
 Groups    Name        Std.Dev.       Corr
 fi_fishid (Intercept) 23.31207778820
           date         0.00186829445 -0.9999732
 Residual              13.65505150088
Number of obs: 8846, groups:  fi_fishid, 31
Fixed Effects:
                        (Intercept)                  fi_speciespikeperch                       fi_specieswels                       seasonspring_I
                     24.03071953287                       -5.76887179686                       -0.05880964371                       -1.76402947592
                    seasonspring_II                         seasonsummer                         seasonwinter                             ca_tl_mm
                     14.71860509541                       -3.25310449657                        3.64967750199                        0.00254985384
 fi_speciespikeperch:seasonspring_I        fi_specieswels:seasonspring_I  fi_speciespikeperch:seasonspring_II       fi_specieswels:seasonspring_II
                      7.50659036387                        6.79421462518                      -13.73663592024                      -10.47401534263
   fi_speciespikeperch:seasonsummer          fi_specieswels:seasonsummer     fi_speciespikeperch:seasonwinter          fi_specieswels:seasonwinter
                     12.91029231560                        4.31169398801                       -3.45615553340                       -2.91280877200
convergence code 0; 1 optimizer warnings; 0 lme4 warnings
```

Visualize the candidate models ordered according to their level of complexity depending on the number of predictors

```
aiccvalues<-summary(glmulti.cand.mod)$icvalues

as.data.frame(summary(glmulti.cand.mod)$icvalues)
```
```
summary(glmulti.cand.mod)$icvalues
1                       71505.1087679
2                       71505.1087679
3                       71505.1087679
4                       71505.1087679
5                       71527.7105534
6                       71664.5099943
7                       71664.5099943
8                       71737.1715651
9                       71797.9793696
10                      71801.1812122
11                      71809.6002216
12                      71814.5624886
13                      71827.6013337
14                      71842.3928875
15                      71868.8848324
16                      71872.8068276
17                      71882.9964473
18                      71891.3339893
```

```
model.weights<-summary(glmulti.cand.mod)$modelweights
```

```
plot(aiccvalues,model.weights)
```

![M_r_2_7](/Plots/M_r_2_7.png "M_r_2_7")

Summary of multi-model selection and inference analysi (best and worst models, IC value, evidence weight, etc.)

```
print(glmulti.cand.mod)
```

```
glmulti.analysis
Method: h / Fitting: mixed.glmulti / IC used: aicc
Level: 2 / Marginality: TRUE
From 18 models:
Best IC: 71505.1087678507
Best model:
[1] "sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species"
[1] "sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + "
[2] "    season:ca_tl_mm"
[1] "sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + "
[2] "    fi_species:ca_tl_mm"
[1] "sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + "
[2] "    fi_species:ca_tl_mm + season:ca_tl_mm"
Evidence weight: 0.249999227384688
Worst IC: 71891.3339893277
4 models within 2 IC units.
3 models to reach 95% of evidence weight.
```

Return AICc values and model weigths, ranked from lower to higher and from higher to lower, respectively

```
weightable(glmulti.cand.mod)

```

```
                                                                                                                model          aicc           weights
1                                          sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species 71505.1087679 2.49999227385e-01
2                        sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + season:ca_tl_mm 71505.1087679 2.49999227385e-01
3                    sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + fi_species:ca_tl_mm 71505.1087679 2.49999227385e-01
4  sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + fi_species:ca_tl_mm + season:ca_tl_mm 71505.1087679 2.49999227385e-01
5                                                     sqrt(ranged2d + 1) ~ 1 + fi_species + season + season:fi_species 71527.7105534 3.09046124996e-06
6                                        sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + fi_species:ca_tl_mm 71664.5099943 6.08698375235e-36
7                      sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + fi_species:ca_tl_mm + season:ca_tl_mm 71664.5099943 6.08698375235e-36
8                                                 sqrt(ranged2d + 1) ~ 1 + fi_species + ca_tl_mm + fi_species:ca_tl_mm 71737.1715651 1.01424418564e-51
9                                                                         sqrt(ranged2d + 1) ~ 1 + fi_species + season 71797.9793696 6.33717255485e-65
10                                                             sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm 71801.1812122 1.27827491118e-65
11                                           sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:ca_tl_mm 71809.6002216 1.89871414468e-67
12                                                                                     sqrt(ranged2d + 1) ~ 1 + season 71814.5624886 1.58824320291e-68
13                                                                          sqrt(ranged2d + 1) ~ 1 + season + ca_tl_mm 71827.6013337 2.34189688997e-71
14                                                        sqrt(ranged2d + 1) ~ 1 + season + ca_tl_mm + season:ca_tl_mm 71842.3928875 1.43754901777e-74
15                                                                                 sqrt(ranged2d + 1) ~ 1 + fi_species 71868.8848324 2.54079676714e-80
16                                                                      sqrt(ranged2d + 1) ~ 1 + fi_species + ca_tl_mm 71872.8068276 3.57535756502e-81
17                                                                                              sqrt(ranged2d + 1) ~ 1 71882.9964473 2.19114781226e-83
18                                                                                   sqrt(ranged2d + 1) ~ 1 + ca_tl_mm 71891.3339893 3.38998234835e-85
```
```
print(glmulti.cand.mod)
```

```
glmulti.analysis
Method: h / Fitting: mixed.glmulti / IC used: aicc
Level: 2 / Marginality: TRUE
From 18 models:
Best IC: 71505.1087678507
Best model:
[1] "sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species"
[1] "sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + "
[2] "    season:ca_tl_mm"
[1] "sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + "
[2] "    fi_species:ca_tl_mm"
[1] "sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + "
[2] "    fi_species:ca_tl_mm + season:ca_tl_mm"
Evidence weight: 0.249999227384688
Worst IC: 71891.3339893277
4 models within 2 IC units.
3 models to reach 95% of evidence weight.
```
```
summary(glmulti.cand.mod)
```

```
$name
[1] "glmulti.analysis"

$method
[1] "h"

$fitting
[1] "mixed.glmulti"

$crit
[1] "aicc"

$level
[1] 2

$marginality
[1] TRUE

$confsetsize
[1] 150

$bestic
[1] 71505.1087679

$icvalues
 [1] 71505.1087679 71505.1087679 71505.1087679 71505.1087679 71527.7105534 71664.5099943 71664.5099943 71737.1715651 71797.9793696 71801.1812122
[11] 71809.6002216 71814.5624886 71827.6013337 71842.3928875 71868.8848324 71872.8068276 71882.9964473 71891.3339893

$bestmodel
[1] "sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species"

$modelweights
 [1] 2.49999227385e-01 2.49999227385e-01 2.49999227385e-01 2.49999227385e-01 3.09046124996e-06 6.08698375235e-36 6.08698375235e-36 1.01424418564e-51
 [9] 6.33717255485e-65 1.27827491118e-65 1.89871414468e-67 1.58824320291e-68 2.34189688997e-71 1.43754901777e-74 2.54079676714e-80 3.57535756502e-81
[17] 2.19114781226e-83 3.38998234835e-85

$includeobjects
[1] TRUE
```

## COMPARE MODELS

Perform Log-Likelihood Ratio Tests (LRT) to compare the first and the remaining three best models. There are different ways of performing a LRT:
 
#### 1. Using the _bootMer_ function from the _lme4_ package to compute 100 bootstrapped log-likelihood:
  
```
boot_m_1 <-bootMer(m1, FUN = function(x) as.numeric(logLik(x)), nsim = 100)
boot_m_1
```
```  
PARAMETRIC BOOTSTRAP


Call:
bootMer(x = m1, FUN = function(x) as.numeric(logLik(x)), nsim = 100)


Bootstrap Statistics :
     original    bias    std. error
t1* -35732.51 -51.48284    68.00746
```
``` 
boot_m_2 <- bootMer(m2, FUN = function(x) as.numeric(logLik(x)), nsim = 100)
boot_m_2
```
```
PARAMETRIC BOOTSTRAP


Call:
bootMer(x = m2, FUN = function(x) as.numeric(logLik(x)), nsim = 100)


Bootstrap Statistics :
     original    bias    std. error
t1* -35764.49 -54.38482    65.61975
```

 - Obtain observed Chi^2 value of the LRT
 
```  
lrt <- as.numeric(-2 * logLik(m1) + 2 * logLik(m2))
lrt <- as.numeric(-2 * (-35732.51) + 2 * (-35764.49))
```
```
[1] -63.96
```
  - Compute 100 bootstrapped LRT

```
lrt.b <- -2 * boot_m_1$t + 2 * boot_m_2$t
```

#### 2. With the LRT formula:

```
LRT <- as.numeric(-2 * logLik(m2) + 2 * logLik(m1))                       
pchisq(LRT, df = length(coef(m2)) - length(coef(m1)), lower.tail = FALSE)
```

#### 3. Using the *anova()* function in the *car* package specifying by type="LRT":

```
anova(m2, m1, type="LRT")
```
```
refitting model(s) with ML (instead of REML)
Data: mean.ranged2d
Models:
m1: sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + 
m1:     (date | fi_fishid)
m2: sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + 
m2:     season:ca_tl_mm + (date | fi_fishid)
   Df   AIC   BIC logLik deviance  Chisq Chi Df Pr(>Chisq)
m1 20 71497 71639 -35729    71457                         
m2 24 71501 71671 -35727    71453 4.3571      4     0.3598
```

#### 4. Using  the *lrtest* function in _lmtest_ library:

```
📗 `library(lmtest)`
```

```diff
+ m1 <- lmer(sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + (date|fi_fishid), REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), data=mean.ranged2d,na.action=na.omit)

- m2 <- lmer(sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + season:ca_tl_mm + (date|fi_fishid), REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), data=mean.ranged2d,na.action=na.omit)
```
```
lrtest(m1,m2) 
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species +
    (date | fi_fishid)
Model 2: sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species +
    season:ca_tl_mm + (date | fi_fishid)
  #Df LogLik Df  Chisq Pr(>Chisq)
1  20 -35733
2  24 -35764  4 63.973  4.234e-13 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
```diff
+ m1 <- lmer(sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + (date|fi_fishid), REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), data=mean.ranged2d,na.action=na.omit)

- m3 <- lmer(sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + fi_species:ca_tl_mm + (date|fi_fishid), REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), data=mean.ranged2d,na.action=na.omit)
```
```
lrtest(m1,m3)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species +
    (date | fi_fishid)
Model 2: sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species +
    fi_species:ca_tl_mm + (date | fi_fishid)
  #Df LogLik Df  Chisq Pr(>Chisq)
1  20 -35733
2  22 -35616  2 232.83  < 2.2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```

```diff
+ m1 <- lmer(sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + (date|fi_fishid), REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), data=mean.ranged2d,na.action=na.omit)

- m4 <- lmer(sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + fi_species:ca_tl_mm + season:ca_tl_mm + (date|fi_fishid), REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), data=mean.ranged2d,na.action=na.omit)
```
```
lrtest(m1,m4)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species +
    (date | fi_fishid)
Model 2: sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species +
    fi_species:ca_tl_mm + season:ca_tl_mm + (date | fi_fishid)
  #Df LogLik Df  Chisq Pr(>Chisq)
1  20 -35733
2  26 -35766  6 67.551  1.298e-12 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```

- So, *model 2* is better than *model 1* but how is *model 2* compared to models 3 and 4?

🔺 _We can not compare models 2 and 3 using LRT as they have non-nested fixed terms despite their same random-effects structure. In this case we should look at their pseudo-R^2 and AIC values_

```diff
+ m2 <- lmer(sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + season:ca_tl_mm + (date|fi_fishid), REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), data=mean.ranged2d,na.action=na.omit)

- m3 <- lmer(sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + fi_species:ca_tl_mm + (date|fi_fishid), REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), data=mean.ranged2d,na.action=na.omit)
```
```
lrtest(m2,m3)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species +
    season:ca_tl_mm + (date | fi_fishid)
Model 2: sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species +
    fi_species:ca_tl_mm + (date | fi_fishid)
  #Df LogLik Df Chisq Pr(>Chisq)
1  24 -35764
2  22 -35616 -2 296.8  < 2.2e-16 ***     # This high p-value indicates the test is not correct or invalid
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
```diff
+ m2 <- lmer(sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + season:ca_tl_mm + (date|fi_fishid), REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), data=mean.ranged2d,na.action=na.omit)

- m4 <- lmer(sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + fi_species:ca_tl_mm + season:ca_tl_mm + (date|fi_fishid), REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore"), data=mean.ranged2d,na.action=na.omit)
```
```
lrtest(m2,m4)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species +
    season:ca_tl_mm + (date | fi_fishid)
Model 2: sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species +
    fi_species:ca_tl_mm + season:ca_tl_mm + (date | fi_fishid)
  #Df LogLik Df  Chisq Pr(>Chisq)
1  24 -35764
2  26 -35766  2 3.5776     0.1672
```

- The comparison between models 2 and 4 is not significant indicating that including additional parameters is not likely to explain a better piorportion of the variance in the response variable. Therefore, *model 2* is preferred to models 1 and 4. For now we can take *model2* as our final best model ("m_final"). 

## ANALYSE THE FINAL MODEL

Rename _m2_ to _m_final_ and  re-fit the model. There are convergence issues and the **Nelder_Mead** optimizer seems to solve them 

```
 m_final <- lmer(sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + season:ca_tl_mm + (date|fi_fishid),  REML=T, control=lmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore", optimizer = "Nelder_Mead"), data=mean.ranged2d,na.action=na.omit)
```

#### Test overdispersion 

There is no need to test for overdisperion in a mixed model with Gaussian distribution. The reason is # ¿?????  . However, I provide the function required to properly calculate the ratio of summ

```
overdisp_fun <- function(m_final) {
    rdf <- df.residual(m_final)
    rp <- residuals(m_final,type="pearson")
    Pearson.chisq <- sum(rp^2)
    prat <- Pearson.chisq/rdf
    pval <- pchisq(Pearson.chisq, df=rdf, lower.tail=FALSE)
    c(chisq=Pearson.chisq,ratio=prat,rdf=rdf,p=pval)
}

overdisp_fun(m_final)                 
```

#### Calculate marginal (associated with fixed effects) and conditional (fixed effects + random effects) R^2 values. These estimates are equivalent 

📗 `library (MuMIn)`

```
r.squaredGLMM(m_final)  
```
```
         R2m       R2c
[1,] 0.0257772 0.6764458
```

#### Compute confidence intervals using parametric bootstrap

```
confint(m_final, method="boot", nsim=1000)          
```

#### QQ plot and hidtogram of residuals 

```
qqnorm(residuals(m_final))
```

![m_final_qq](/Plots/m_final_qq.png "m_final_qq")

```
hist(residuals(m_final))
```

![m_final_hist](/Plots/m_final_hist.png "m_final_hist")

```
summary(m_final)
```

#### Explore the interactions terms

- We check if the interaction between variables is actually significant before proceeding with a in-depth exploration of the interaction. To do so, let's check the residuals of the models and the ANOVA table

```
par(mfcol=c(1,2))

Anova(m_final)
```
```
Analysis of Deviance Table (Type II Wald chisquare tests)

Response: sqrt(ranged2d + 1)
                    Chisq Df Pr(>Chisq)    
fi_species         0.8484  2     0.6543    
season            40.6673  4  3.150e-08 ***
ca_tl_mm           0.2205  1     0.6387    
fi_species:season 66.9027  8  2.027e-11 ***
season:ca_tl_mm    3.6753  4     0.4517    
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```

- We see that the interaction between _fi_species_ and _season_ is significant, so let's further explore this effect.

- There is an interaction effect, and this can be represented as the adjusted mean of the response for the corresponding interaction of factors. Use the function _interactionMeans_ in the package _phia_ to obtain the adjusted mean and SE from the model coefficients:

📗 `library(phia)`

To obtain marginal means, specify only a factor. For example:

```
interactionMeans(m_final, factors="fi_species")
```
```
  fi_species adjusted mean SE of link
1       pike      26.61821   2.843918
2  pikeperch      27.75892   4.218982
3       wels      27.51327   2.994344
```
```
interactionMeans(m_final, factors="season")
```
```
     season adjusted mean SE of link
1    autumn      25.13013   1.669153
2  spring_I      26.47517   1.657828
3 spring_II      31.52853   2.278390
4    summer      26.64644   1.577263
5    winter      26.70372   1.907980
```
- For both factor levels

```
(m_final.means <- interactionMeans(m_final))  # interactionMeans(m_final, factors=c("season","fi_species"))
``` 
```
   fi_species    season adjusted mean SE of link
1        pike    autumn      24.81254   2.878192
2   pikeperch    autumn      24.16954   4.274128
3        wels    autumn      26.40833   3.029441
4        pike  spring_I      26.15813   2.864160
5   pikeperch  spring_I      23.31361   4.242279
6        wels  spring_I      29.95378   3.010449
7        pike spring_II      33.71909   3.990624
8   pikeperch spring_II      33.47812   5.735293
9        wels spring_II      27.38838   4.127118
10       pike    summer      23.33831   2.715172
11  pikeperch    summer      29.39862   4.043883
12       wels    summer      27.20238   2.877929
13       pike    winter      25.06299   3.288052
14  pikeperch    winter      28.43469   4.877400
15       wels    winter      26.61347   3.419067
````
```
plot(m_final.means)      # plot(interactionMeans(m_final, factors=c("season","fi_species")))
```
![m_final_inter_1](/Plots/m_final_inter_1.png "m_final_inter_1")


#### Plot marginal effects 

```
library(sjPlot)
library(ggplot2)
theme_set(theme_sjplot())
```
#### Re-fit the model using a Gamma distribution

```
m_final <- glmer(ranged2d ~ 1 + fi_species*season*ca_tl_mm + (1|fi_fishid), control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore", optimizer = "Nelder_Mead"), family="Gamma"(link='log'), data=mean.ranged2d,na.action=na.omit)
```
```
Warning messages:
1: In (function (fn, par, lower = rep.int(-Inf, n), upper = rep.int(Inf,  :
  failure to converge in 10000 evaluations
2: In checkConv(attr(opt, "derivs"), opt$par, ctrl = control$checkConv,  :
  Model failed to converge with max|grad| = 1.023 (tol = 0.001, component 1)
3: In checkConv(attr(opt, "derivs"), opt$par, ctrl = control$checkConv,  :
  Model is nearly unidentifiable: very large eigenvalue
 - Rescale variables?;Model is nearly unidentifiable: large eigenvalue ratio
 - Rescale variables?
```
🔴 The model fitted is not optimal and we get warning message. We'll re-fit the model later but now let's continue analysing this model: 
```
summ(m_final)
```
```
MODEL INFO:
Observations: 8846
Dependent Variable: ranged2d
Type: Mixed effects generalized linear regression
Error Distribution: Gamma
Link function: log 

MODEL FIT:
AIC = 122739.31, BIC = 122966.12
Pseudo-R² (fixed effects) =  NA
Pseudo-R² (total) =  NA 

FIXED EFFECTS:
---------------------------------------------------------------------------------
                                                      Est.   S.E.   t val.      p
-------------------------------------------------- ------- ------ -------- ------
(Intercept)                                           4.98   0.52     9.55   0.00
fi_speciespikeperch                                   2.53   1.28     1.98   0.05
fi_specieswels                                        2.45   0.76     3.22   0.00
seasonspring_I                                       -0.06   0.43    -0.13   0.89
seasonspring_II                                       2.09   0.76     2.77   0.01
seasonsummer                                          0.32   0.34     0.95   0.34
seasonwinter                                          2.20   0.34     6.41   0.00
ca_tl_mm                                              0.00   0.00     3.47   0.00
fi_speciespikeperch:seasonspring_I                   -0.17   1.00    -0.17   0.87
fi_specieswels:seasonspring_I                        -0.29   0.61    -0.48   0.63
fi_speciespikeperch:seasonspring_II                   1.78   1.66     1.08   0.28
fi_specieswels:seasonspring_II                       -2.54   1.16    -2.19   0.03
fi_speciespikeperch:seasonsummer                     -3.79   0.86    -4.40   0.00
fi_specieswels:seasonsummer                          -0.48   0.47    -1.01   0.31
fi_speciespikeperch:seasonwinter                     -1.91   0.92    -2.09   0.04
fi_specieswels:seasonwinter                          -3.71   0.50    -7.37   0.00
fi_speciespikeperch:ca_tl_mm                         -0.00   0.00    -1.96   0.05
fi_specieswels:ca_tl_mm                              -0.00   0.00    -3.65   0.00
seasonspring_I:ca_tl_mm                              -0.00   0.00    -0.25   0.81
seasonspring_II:ca_tl_mm                             -0.00   0.00    -1.65   0.10
seasonsummer:ca_tl_mm                                -0.00   0.00    -1.98   0.05
seasonwinter:ca_tl_mm                                -0.00   0.00    -5.62   0.00
fi_speciespikeperch:seasonspring_I:ca_tl_mm           0.00   0.00     0.84   0.40
fi_specieswels:seasonspring_I:ca_tl_mm                0.00   0.00     1.24   0.21
fi_speciespikeperch:seasonspring_II:ca_tl_mm         -0.01   0.00    -1.94   0.05
fi_specieswels:seasonspring_II:ca_tl_mm               0.00   0.00     1.83   0.07
fi_speciespikeperch:seasonsummer:ca_tl_mm             0.01   0.00     5.42   0.00
fi_specieswels:seasonsummer:ca_tl_mm                  0.00   0.00     2.23   0.03
fi_speciespikeperch:seasonwinter:ca_tl_mm             0.00   0.00     1.10   0.27
fi_specieswels:seasonwinter:ca_tl_mm                  0.00   0.00     7.11   0.00
---------------------------------------------------------------------------------

RANDOM EFFECTS:
-------------------------------------
   Group      Parameter    Std. Dev. 
----------- ------------- -----------
 fi_fishid   (Intercept)     0.35    
 Residual                    0.92    
-------------------------------------

Grouping variables:
-----------------------------
   Group     # groups   ICC  
----------- ---------- ------
 fi_fishid      31      0.13 
-----------------------------
```
#### Simple slope analysis

```
probe_interaction(m_final, modx  = fi_species, pred = ca_tl_mm, plot.points=TRUE,cond.int = TRUE, interval = TRUE,jnplot = TRUE)
```
```
SIMPLE SLOPES ANALYSIS 


When fi_species = wels: 

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of ca_tl_mm             -0.00   0.00    -1.35   0.18
Conditional intercept          6.87   0.18    38.86   0.00

When fi_species = pikeperch: 

                               Est.   S.E.   t val.      p
--------------------------- ------- ------ -------- ------
Slope of ca_tl_mm             -0.00   0.00    -1.07   0.28
Conditional intercept          5.23   0.99     5.31   0.00

When fi_species = pike: 

                              Est.   S.E.   t val.      p
--------------------------- ------ ------ -------- ------
Slope of ca_tl_mm             0.00   0.00     3.30   0.00
Conditional intercept         7.17   0.19    37.46   0.00

There were 12 warnings (use warnings() to see them)
```
![m_final_slope](/Plots/m_final_slope.png "m_final_slope")

- As above, the slope of body length is only significant (and positive) in pike

#### Fit another model with a nested design of fish id within species, using Gamma distribution and adaptive Gaussian quadrature

To specify for an adaptive Gaussian quadrature we use the argument _naGQ=0_ 

```
m_final <- glmer(ranged2d ~ 1 + fi_species*season*ca_tl_mm + (1 + fi_species|fi_fishid), control=glmerControl(check.nobs.vs.nlev = "ignore",check.nobs.vs.rankZ = "ignore",check.nobs.vs.nRE="ignore", optimizer = "Nelder_Mead"), family="Gamma"(link='log'), data=mean.ranged2d,na.action=na.omit, nAGQ = 0)   
```
```
summ(m_final,  center = TRUE, scale = TRUE, n.sd = 2) 
``` 
```
MODEL INFO:
Observations: 7311
Dependent Variable: ranged2d
Type: Mixed effects generalized linear regression
Error Distribution: Gamma
Link function: log 

MODEL FIT:
AIC = 114109.82, BIC = 114365.02
Pseudo-R² (fixed effects) =  NA
Pseudo-R² (total) =  NA 

FIXED EFFECTS:
---------------------------------------------------------------------------------
                                                      Est.   S.E.   t val.      p
-------------------------------------------------- ------- ------ -------- ------
(Intercept)                                           7.31   0.10    74.94   0.00
fi_speciespikeperch                                  -0.41   0.66    -0.62   0.53
fi_specieswels                                       -0.46   0.12    -3.81   0.00
seasonspring_I                                       -0.16   0.06    -2.73   0.01
seasonspring_II                                       0.56   0.10     5.54   0.00
seasonsummer                                         -0.46   0.05    -8.67   0.00
seasonwinter                                          0.02   0.06     0.27   0.78
ca_tl_mm                                              1.53   0.26     5.85   0.00
fi_speciespikeperch:seasonspring_I                    0.68   0.41     1.68   0.09
fi_specieswels:seasonspring_I                         0.57   0.08     7.35   0.00
fi_speciespikeperch:seasonspring_II                  -2.41   0.69    -3.51   0.00
fi_specieswels:seasonspring_II                       -0.49   0.14    -3.42   0.00
fi_speciespikeperch:seasonsummer                      2.04   0.32     6.28   0.00
fi_specieswels:seasonsummer                           0.67   0.07     9.78   0.00
fi_speciespikeperch:seasonwinter                     -0.66   0.35    -1.92   0.05
fi_specieswels:seasonwinter                          -0.15   0.07    -2.10   0.04
fi_speciespikeperch:ca_tl_mm                         -1.03   1.12    -0.92   0.36
fi_specieswels:ca_tl_mm                              -1.35   0.30    -4.56   0.00
seasonspring_I:ca_tl_mm                              -0.10   0.16    -0.58   0.56
seasonspring_II:ca_tl_mm                             -1.00   0.24    -4.24   0.00
seasonsummer:ca_tl_mm                                -0.59   0.15    -3.96   0.00
seasonwinter:ca_tl_mm                                -1.30   0.15    -8.86   0.00
fi_speciespikeperch:seasonspring_I:ca_tl_mm           0.01   0.68     0.01   0.99
fi_specieswels:seasonspring_I:ca_tl_mm                0.03   0.19     0.14   0.89
fi_speciespikeperch:seasonspring_II:ca_tl_mm         -2.38   1.20    -1.98   0.05
fi_specieswels:seasonspring_II:ca_tl_mm               1.33   0.29     4.60   0.00
fi_speciespikeperch:seasonsummer:ca_tl_mm             2.55   0.55     4.62   0.00
fi_specieswels:seasonsummer:ca_tl_mm                  0.53   0.17     3.13   0.00
fi_speciespikeperch:seasonwinter:ca_tl_mm             0.02   0.58     0.04   0.97
fi_specieswels:seasonwinter:ca_tl_mm                  1.66   0.17     9.74   0.00
---------------------------------------------------------------------------------

RANDOM EFFECTS:
---------------------------------------------
   Group          Parameter        Std. Dev. 
----------- --------------------- -----------
 fi_fishid       (Intercept)         0.20    
 fi_fishid   fi_speciespikeperch     0.20    
 fi_fishid     fi_specieswels        0.04    
 Residual                            0.66    
---------------------------------------------

Grouping variables:
-----------------------------
   Group     # groups   ICC  
----------- ---------- ------
 fi_fishid      31      0.08 
-----------------------------

Continuous predictors are mean-centered and scaled by 2 s.d.
```




## References

**Gelman, A. 2008.** _Scaling regression inputs by dividing by two standard deviations. Statistics in Medicine. 27, 2865-2873. (DOI 10.1002/sim.3107)_

RED APPLE (&#x1F34E;): 🍎
GREEN APPLE (&#x1F34F;): 🍏
BLUE HEART (&#x1F499;): 💙
GREEN HEART (&#x1F49A;): 💚
YELLOW HEART (&#x1F49B;): 💛
PURPLE HEART (&#x1F49C;): 💜
GREEN BOOK (&#x1F4D7;): 📗
BLUE BOOK (&#x1F4D8;): 📘
ORANGE BOOK (&#x1F4D9;): 📙
LARGE RED CIRCLE (&#x1F534;): 🔴
LARGE BLUE CIRCLE (&#x1F535;): 🔵
LARGE ORANGE DIAMOND (&#x1F536;): 🔶
LARGE BLUE DIAMOND (&#x1F537;): 🔷
SMALL ORANGE DIAMOND (&#x1F538;): 🔸
SMALL BLUE DIAMOND (&#x1F539;): 🔹
UP-POINTING RED TRIANGLE (&#x1F53A;): 🔺
DOWN-POINTING RED TRIANGLE (&#x1F53B;): 🔻
UP-POINTING SMALL RED TRIANGLE (&#x1F53C;): 🔼
DOWN-POINTING SMALL RED TRIANGLE (&#x1F53D;): 🔽

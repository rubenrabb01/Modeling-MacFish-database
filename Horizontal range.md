# Analysis of range of distances for horizontal movement

## 1. Prepare dataset with daily and seasonally distance range

:books:`library(lubridate)`  
:books:`library(plyr)`  

Add info for each species and order by date
```
data_longit<-mean.ranged2d[with(mean.ranged2d, order(date)),]
```

Create a new numeric variable _week_num_ for day of the week based on _date_ (i.e., day)
```
data_longit$date <- as.Date(data_longit$date)
data_longit$week_day_num <- wday(data_longit$date)
```

Transform and recode week and season variables
```
data_longit<-as.data.table(data_longit)
data_longit[, week_day := as.factor(car::recode(week_day_num, "'1'='Monday';'2'='Tuesday';'3'='Wednesday';'4'='Thursday'; '5'='Friday';'6'='Saturday';'7'='Sunday'"))]
colnames(data_longit)[8] <- "Species"
data_longit$Species<-as.factor(data_longit$Species)
data_longit$fi_fishid<-as.factor(data_longit$fi_fishid)
data_longit$season<-as.factor(data_longit$season)
data_longit[, season_num := as.factor(car::recode(season, "'spring_I'='1';'summer'='2';'autumn'='3';'winter'='4'; 'spring_II'='5'"))]
data_longit$season_num<-as.numeric(data_longit$season_num)
data_longit
```
|            	| fi_fishid 	| date       	| ranged2d 	| meand2d   	| ca_tl_mm 	| ca_weight_g 	| fi_sex 	| Species   	| season    	| r3_condition 	| diff_mean_dist 	| cum_displac 	| week_day_num 	| week_day  	| season_num 	|
|------------	|-----------	|------------	|----------	|-----------	|----------	|-------------	|--------	|-----------	|-----------	|--------------	|----------------	|-------------	|--------------	|-----------	|------------	|
| 1:00       	| T449310_1 	| 27/04/2017 	| 0        	| 5122.2007 	| 405      	| 523         	| M      	| pikeperch 	| spring_I  	| 1.13081917   	| 0              	| 0           	| 5            	| Friday    	| 1          	|
| 2:00       	| T449317_1 	| 27/04/2017 	| 536.8212 	| 5454.211  	| 415      	| 560         	| M      	| pikeperch 	| spring_I  	| 1.07782806   	| 0              	| 0           	| 5            	| Friday    	| 1          	|
| 3:00       	| T449202_1 	| 27/04/2017 	| 682.8973 	| 4841.78   	| 430      	| 605         	| M      	| pikeperch 	| spring_I  	| 0.9951284    	| 0              	| 0           	| 5            	| Friday    	| 1          	|
| 4:00       	| T449207_1 	| 27/04/2017 	| 266.7728 	| 3700.87   	| 500      	| 750         	| X      	| pike      	| spring_I  	| 0.58931601   	| 0              	| 0           	| 5            	| Friday    	| 1          	|
| 5:00       	| T449287_1 	| 27/04/2017 	| 223.4977 	| 498.5292  	| 595      	| 765         	| X      	| pike      	| spring_I  	| -0.01703585  	| 0              	| 0           	| 5            	| Friday    	| 1          	|
| ---        	|           	|            	|          	|           	|          	|             	|        	|           	|           	|              	|                	|             	|              	|           	|            	|
| 8842:00:00 	| T449203_1 	| 10/04/2018 	| 0        	| 4523.1547 	| 1160     	| 10900       	| X      	| pike      	| spring_II 	| -0.54832184  	| 1351.76269     	| 220749.98   	| 3            	| Wednesday 	| 5          	|
| 8843:00:00 	| T449268_1 	| 10/04/2018 	| 0        	| 5026.984  	| 1620     	| 25100       	| M      	| wels      	| spring_II 	| 0.83998846   	| 0              	| 133369.99   	| 3            	| Wednesday 	| 5          	|
| 8844:00:00 	| T449207_1 	| 11/04/2018 	| 0        	| 3052.8224 	| 500      	| 750         	| X      	| pike      	| spring_II 	| 0.58931601   	| 674.61169      	| 46138.93    	| 4            	| Thursday  	| 5          	|
| 8845:00:00 	| T449208_1 	| 11/04/2018 	| 0        	| 1467.4694 	| 560      	| 1483        	| M      	| pikeperch 	| spring_II 	| 0.42781114   	| 84.07252       	| 29398.32    	| 4            	| Thursday  	| 5          	|
| 8846:00:00 	| T449276_1 	| 11/04/2018 	| 640.4041 	| 1383.3969 	| 1030     	| 6150        	| F      	| wels      	| spring_II 	| -1.16616183  	| 399.93036      	| 99324.82    	| 4            	| Thursday  	| 5          	|


Plot horizontal range by date
```
ggplot(data_longit, aes(x = date, y = ranged2d, colour = Species)) +
                   geom_line() +
                   labs(x = "Date", y = "Horizontal range [m]") +
                   theme(panel.border = element_blank(),
                         panel.background = element_blank(),
                         axis.text = element_text(size = 10),
                         axis.title = element_text(size = 12, face = "bold"))
```
![Horiz_range](/Plots/Horiz_range_1.png "Horiz_range")

```
ggplot(data_longit_sub, aes(x = date, y = ranged2d, colour = fi_fishid)) +
                   geom_line() +
                   labs(x = "Date", y = "Horizontal range [m]") +
                   theme(panel.border = element_blank(),
                         panel.background = element_blank(),
                         axis.text = element_text(size = 10),
                         axis.title = element_text(size = 12, face = "bold"))
```
![Horiz_range](/Plots/Horiz_range_2.png "Horiz_range")

## 2. Build GAMM models and conduct model-selection

### 2.1. Create a new dataset

First, lets subset horizontal range data into a new dataset _data_longit_sub_ for easing models fitting
```
data_longit_sub <- data.table(ranged2d = data_longit[, ranged2d], Species = data_longit[, Species], Id = data_longit[, fi_fishid], weekly = data_longit[, week_day_num], season = data_longit[, season], seasonally = data_longit[, season_num], daily = data_longit[, date], body_size = data_longit[, ca_tl_mm])
```

### 2.2. Fit a global GAMM model with uncorrelated errors to seasonality data

- Fit a model assuming all observations are indpependent and including a smooth term for seasonallity grouped by Species
- Add one random-term added for subject identity
- We set k for the variable _seasonally_ to 5, which is the number of unique values for each season
- Include a simple random term for subject identity
- Horizontal range might be expected to vary cyclically across seasons so we need to specify bs = "cc"  (i.e., cyclic cubic regression splines), hence, seasonally shows no discontinuity between Spring I and Spring II and both ends line up

:books:`library(mgcv)`  

```
m_gam_season<- bam(sqrt(ranged2d+1) ~ s(seasonally, bs = "cc", k = 5, by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
```
```
summary(m_gam_season)
```
```
Family: gaussian
Link function: identity

Formula:
sqrt(ranged2d + 1) ~ s(seasonally, bs = "cc", k = 5, by = Species) +
    s(Id, bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)   26.476      1.219   21.72   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                                  edf Ref.df      F  p-value
s(seasonally):Speciespike       2.689      3  97.15 1.23e-15 ***
s(seasonally):Speciespikeperch  2.884      3 281.67  < 2e-16 ***
s(seasonally):Specieswels       2.824      3  27.97 1.48e-08 ***
s(Id)                          29.531     30  67.25  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.211   Deviance explained = 21.4%
-REML =  35805  Scale est. = 188.58    n = 8846
```

### Plot model

:books:`library(gratia)`

```
draw(m_gam_season, ncol = 2)
```
![Horiz_range](/Plots/Horiz_range_3.png "Horiz_range")

- We see that the r-squared value is too low and we should search a more suitable model

### 2.3. Fit a GAMM model with uncorrelated errors to weekly and seasonality data

- Now we set k to 7 and 5 for both _weekly_ and _seasonally, respectively
- Note that _weekly_ refers to day of the week
```
m_gam_week_season<- bam(sqrt(ranged2d+1) ~ s(weekly, bs = "cc", k= 7) + s(seasonally, bs = "cc", k = 5, by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
```
```
summary(m_gam_week_season)
```
```
Family: gaussian
Link function: identity

Formula:
sqrt(ranged2d + 1) ~ s(weekly, bs = "cc", k = 7) + s(seasonally,
    bs = "cc", k = 5, by = Species) + s(Id, bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)   26.476      1.219   21.73   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                                  edf Ref.df       F  p-value
s(weekly)                       1.654      5   0.918   0.0443 *
s(seasonally):Speciespike       2.689      3  97.161 1.21e-15 ***
s(seasonally):Speciespikeperch  2.884      3 281.761  < 2e-16 ***
s(seasonally):Specieswels       2.825      3  28.115 1.36e-08 ***
s(Id)                          29.531     30  67.275  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.211   Deviance explained = 21.5%
-REML =  35804  Scale est. = 188.49    n = 8846
```

### Plot model

```
draw(m_gam_week_season, ncol = 2)
```
![Horiz_range](/Plots/Horiz_range_31.png "Horiz_range")

**Plot surface and one-dimensional seasonal differences in horizontal range**

Lets check differences in _seasonally_ smooth surfaces between species

```
layout(matrix(1:3, nrow = 1))
plot_diff(m_gam_week_season, view="seasonally", comp=list(Species=c("pike", "pikeperch")), main='Seasonal difference pike-pikeperch')
plot_diff(m_gam_week_season, view="seasonally", comp=list(Species=c("pikeperch", "wels")), main='Seasonal difference pikeperch-wels')
plot_diff(m_gam_week_season, view="seasonally", comp=list(Species=c("pike", "wels")), main='Seasonal difference pike-wels')
```
![Horiz_range](/Plots/Horiz_range_32.png "Horiz_range")

- A slightly better fit model

### 2.4. Fit a GAMM model with uncorrelated errors to daily and seasonality data

```
m_gam_daily_season<- bam(sqrt(ranged2d+1) ~ s(as.numeric(daily)) + s(seasonally, bs = "cc", k = 5, by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
```
```
summary(m_gam_daily_season)
```
```
Family: gaussian
Link function: identity

Formula:
sqrt(ranged2d + 1) ~ s(as.numeric(daily)) + s(seasonally, bs = "cc",
    k = 5, by = Species) + s(Id, bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)   26.509      1.208   21.94   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                                  edf Ref.df      F  p-value
s(as.numeric(daily))            7.447  8.353 14.127  < 2e-16 ***
s(seasonally):Speciespike       2.046  3.000  3.522  0.00148 **
s(seasonally):Speciespikeperch  2.857  3.000 48.522  < 2e-16 ***
s(seasonally):Specieswels       2.574  3.000  6.190 1.81e-05 ***
s(Id)                          29.528 30.000 67.221  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.221   Deviance explained = 22.5%
-REML =  35755  Scale est. = 186.07    n = 8846
```
```
draw(m_gam_daily_season, ncol = 2)
```
![Horiz_range](/Plots/Horiz_range_33.png "Horiz_range")

- These three models explain only little variation in horizontal range, thus we need to conduct further model search

### 2.5. Fit GAMM models for week day/season seasonality descomposition

- Fit models with both _weekly_ and _seasonally_ as predictors variables to account for seasonality
- Include body size as a predictor variable and additional random terms for season and species
- Forecast models add a main effect for Species allowing the shape of the smooths to vary between _perk_, _pikeperch_ and _wels_
- Add an interaction (5 out of 8 models) between season and day of the week to account for changes in amount of seasonality over time
- Add a tensor product interaction term in presence of smooth functions for main-effects using the _ti_ function (1 out of 8 models)
```
m_gam_sp1 <- bam(sqrt(ranged2d+1) ~ s(seasonally, by = Species, sp = 0.1, k = 5) + s(weekly,  by = Species, bs = "ps", k = 7) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp2 <- bam(sqrt(ranged2d+1) ~ s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(weekly, seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp3 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(weekly, bs = "ps", k = 7, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp4 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp5 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(weekly, seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp6 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(weekly, bs = "cr", k = 7, by = Species) + s(seasonally, bs = "ps", k = 5, by = Species) + ti(seasonally, weekly, bs = c("cr", "ps"), by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp7 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(weekly, seasonally, bs = "ps", k = 5, by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp8 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(seasonally, bs = "cr", k = 5, by = Species) + s(weekly, bs = "ps", k = 7, by = Species) + ti(seasonally, weekly, bs = c("cr", "ps"), by= Species)+ s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
```
**Note:** The models have been fitted also with inverse gaussian distribution for skewed data (the histograma shows right skewness) and compared to normal models but they are not significantly better

### Model-selection

:books:`library(MASS)`  
:books:`library(AICcmodavg)`  
:books:`library(lmtest)`  

#### Based on AIC
```
AIC(m_gam_sp1,m_gam_sp2,m_gam_sp3,m_gam_sp4,m_gam_sp5,m_gam_sp6,m_gam_sp7,m_gam_sp8)
```
```
                 df      AIC
m_gam_sp1  47.12400 71411.09
m_gam_sp2 138.86651 69918.33
m_gam_sp3 132.42435 69922.44
m_gam_sp5 141.33398 69923.55
m_gam_sp6  51.58551 71411.97
m_gam_sp7 142.06060 69925.00
m_gam_sp8  52.36334 71412.02
```
Model **m_gam_sp2** is preferred (lowest AIC)

**Note:** Comparing these models with GCV.Cp (re-fitting models with method=" GCV.Cp" instead "REML" the results are the same)

#### Based on AICc
```
Cand.mod <- list(m_gam_sp1,m_gam_sp2,m_gam_sp3,m_gam_sp4,m_gam_sp5,m_gam_sp6,m_gam_sp7,m_gam_sp8)

Cand.mod.glm <- lapply(Cand.mod, glm.convert)
aictab(cand.set = Cand.mod.glm, second.ord = TRUE)
```
```
Model selection based on AICc:

       K     AICc Delta_AICc AICcWt Cum.Wt        LL
Mod4 271 69929.72       0.00   0.80   0.80 -34685.26
Mod3 277 69933.35       3.63   0.13   0.93 -34680.69
Mod2 322 69935.54       5.82   0.04   0.98 -34633.57
Mod5 331 69936.92       7.20   0.02   1.00 -34624.55
Mod7 362 69942.09      12.36   0.00   1.00 -34593.55
Mod1  63 71410.19    1480.47   0.00   1.00 -35641.64
Mod6 120 71411.30    1481.58   0.00   1.00 -35583.99
Mod8 120 71411.44    1481.72   0.00   1.00 -35584.06
```

Compare the three best-fit models
```
lrtest(m_gam_sp4,m_gam_sp2)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + te(seasonally, bs = "ps", k = 5,
    by = Species)
Model 2: sqrt(ranged2d + 1) ~ s(Species, Id, bs = "re") + s(season, Id,
    bs = "re") + te(weekly, seasonally, bs = "ps", k = 5, by = Species)
     #Df LogLik     Df Chisq Pr(>Chisq)
1 127.74 -34831
2 138.87 -34820 11.123 21.67    0.02706 *
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
```
lrtest(m_gam_sp3,m_gam_sp2)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + te(weekly, bs = "ps", k = 7, by = Species)
Model 2: sqrt(ranged2d + 1) ~ s(Species, Id, bs = "re") + s(season, Id,
    bs = "re") + te(weekly, seasonally, bs = "ps", k = 5, by = Species)
     #Df LogLik     Df  Chisq Pr(>Chisq)
1 132.42 -34829
2 138.87 -34820 6.4422 16.999   0.009286 **
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
The interaction model **m_gam_sp2** is preferred to both single terms models

### Summary of best-fit model

View GCV and EDF estimates
```
summary(m_gam_sp2)$sp.criterion
```
```
    REML
35079.97
attr(,"Dp")
[1] 4417.996
```
```
summary(m_gam_sp2)$s.table
```
```
                                             edf     Ref.df           F      p-value
s(Species,Id)                          22.238281  30.000000 1043.195544 1.535684e-12
s(season,Id)                           96.266922 127.000000   91.760667 3.746332e-03
te(weekly,seasonally):Speciespike       5.853487   6.320310    3.096203 2.406524e-03
te(weekly,seasonally):Speciespikeperch  3.032668   3.064389    2.220056 8.084995e-02
te(weekly,seasonally):Specieswels       5.880048   6.879601    1.480415 1.650919e-01
```

**Summary table**

:books:`library(itsadug)`  

```
gamtabs(m_gam_sp2, caption="Summary of m_gam_sp2", comment=FALSE, type='html')
```
<table border=1>
<caption align="bottom"> Summary of m_gam_sp2 </caption>
  <tr> <td> A. parametric coefficients </td> <td align="right"> Estimate </td> <td align="right"> Std. Error </td> <td align="right"> t-value </td> <td align="right"> p-value </td> </tr>
  <tr> <td> (Intercept) </td> <td align="right"> 26.4379 </td> <td align="right"> 1.2109 </td> <td align="right"> 21.8342 </td> <td align="right"> &lt; 0.0001 </td> </tr>
   <tr> <td> B. smooth terms </td> <td align="right"> edf </td> <td align="right"> Ref.df </td> <td align="right"> F-value </td> <td align="right"> p-value </td> </tr>
  <tr> <td> s(Species,Id) </td> <td align="right"> 22.2383 </td> <td align="right"> 30.0000 </td> <td align="right"> 1043.1955 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> s(season,Id) </td> <td align="right"> 96.2669 </td> <td align="right"> 127.0000 </td> <td align="right"> 91.7607 </td> <td align="right"> 0.0037 </td> </tr>
  <tr> <td> te(weekly,seasonally):Speciespike </td> <td align="right"> 5.8535 </td> <td align="right"> 6.3203 </td> <td align="right"> 3.0962 </td> <td align="right"> 0.0024 </td> </tr>
  <tr> <td> te(weekly,seasonally):Speciespikeperch </td> <td align="right"> 3.0327 </td> <td align="right"> 3.0644 </td> <td align="right"> 2.2201 </td> <td align="right"> 0.0808 </td> </tr>
  <tr> <td> te(weekly,seasonally):Specieswels </td> <td align="right"> 5.8800 </td> <td align="right"> 6.8796 </td> <td align="right"> 1.4804 </td> <td align="right"> 0.1651 </td> </tr>
   <a name=tab.gam></a>
</table>

We can see that:
- The _weekly_ by _seasonally_ interaction is significant to response variable only in _pike_ whilst it is marginally significant in _pikeperch_
- There is significant inter-individual variation within sesions
- There is high variability between individuals of the three species
- If we fit a model adding an random term _s(Id, bs = 're')_, the term is not significant and undersmooths; finally, the model is not preferred

### Plot model

:books:`library(rgl)`  

**Plot summed effects surfaces (smooth) for the three species**
```
layout(matrix(1:6, nrow = 2))
vis.gam(m_gam_sp2, view=c("seasonally","weekly"), cond=list(Species='pike'), main = "pike", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(28,48), pos=.15)
vis.gam(m_gam_sp2, view=c("seasonally","weekly"), cond=list(Species='pike'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_gam_sp2, view=c("seasonally","weekly"), cond=list(Species='pikeperch'), main = "pikeperch", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(14,24), pos=.15)
vis.gam(m_gam_sp2, view=c("seasonally","weekly"), cond=list(Species='pikeperch'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_gam_sp2, view=c("seasonally","weekly"), cond=list(Species='wels'), main = "wels", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(28,35), pos=.15)
vis.gam(m_gam_sp2, view=c("seasonally","weekly"), cond=list(Species='wels'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
```
![Horiz_range](/Plots/Horiz_range_4.png "Horiz_range")

We see that:
- In _pike_, the highest peak occurs in Spring II and when _weekly_ has value 7 (Sunday)
- In _pikeperch_, the highest peak is in Spring I and nearly highest within _weekly_ values 1-3 (Monday to Wednesday)
- In _wels_, the highest peak is in Spring I, mostly occurring on Monday

**Plot surface and one-dimensional weekly/seasonal differences in horizontal range between the three species**
```
layout(matrix(1:6, nrow = 2))
plot_diff2(m_gam_sp2, view=c("seasonally","weekly"), comp=list(Species=c("pike", "pikeperch")), main='Week day by season difference pike-pikeperch',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_gam_sp2, view="seasonally", comp=list(Species=c("pike", "pikeperch")), main='Seasonal difference pike-pikeperch')
plot_diff2(m_gam_sp2, view=c("seasonally","weekly"), comp=list(Species=c("pikeperch", "wels")), main='Week day by season difference pikeperch-wels',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_gam_sp2, view="seasonally", comp=list(Species=c("pikeperch", "wels")), main='Seasonal difference pikeperch-wels')
plot_diff2(m_gam_sp2, view=c("seasonally","weekly"), comp=list(Species=c("pike", "wels")), main='Week day by season difference pike-wels',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_gam_sp2, view="seasonally", comp=list(Species=c("pike", "wels")), main='Seasonal difference pike-wels')
```
![Horiz_range](/Plots/Horiz_range_5.png "Horiz_range")

### How would dimensional terms change if we would consider cyclical variation in horizontal range?

Now we re-fit the previous best-fit model using cyclic cubic splines with both _weekly_ (values from 1 to 7) and _seasonally_ (cycles from 1 to 5) as predictors to account for cyclic seasonality in horizontal range
```
m_gam_sp2_cyclic <- bam(sqrt(ranged2d+1) ~ s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(weekly, seasonally, bs = "cc", by = Species), family = gaussian, data = data_longit_sub, method = "REML")
```
```
summary(m_gam_sp2_cyclic)$s.table
```
```
                                             edf Ref.df          F      p-value
s(Species,Id)                          21.837242     30 1111.06088 3.381889e-11
s(season,Id)                           97.350379    130  103.74409 3.790050e-03
te(weekly,seasonally):Speciespike       2.498980     15   40.50594 1.180850e-01
te(weekly,seasonally):Speciespikeperch  1.729180     15  218.89610 1.031670e-02
te(weekly,seasonally):Specieswels       5.262844     15   68.55215 1.369554e-01
```
- In contrast to previous model now we see that the _weekly_ by _season_ interaction is significant only in _pikeperch_
- Lets plot the model to see differences with non-cyclic splines

### Plot model
```
layout(matrix(1:6, nrow = 2))
vis.gam(m_gam_sp2_cyclic, view=c("seasonally","weekly"), cond=list(Species='pike'), main = "pike", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(28,48), pos=.15)
vis.gam(m_gam_sp2_cyclic, view=c("seasonally","weekly"), cond=list(Species='pike'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_gam_sp2_cyclic, view=c("seasonally","weekly"), cond=list(Species='pikeperch'), main = "pikeperch", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(14,24), pos=.15)
vis.gam(m_gam_sp2_cyclic, view=c("seasonally","weekly"), cond=list(Species='pikeperch'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_gam_sp2_cyclic, view=c("seasonally","weekly"), cond=list(Species='wels'), main = "wels", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(28,35), pos=.15)
vis.gam(m_gam_sp2_cyclic, view=c("seasonally","weekly"), cond=list(Species='wels'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
```
![Horiz_range](/Plots/Horiz_range_6.png "Horiz_range")

We see that:
- In _pike_, the highest peak occurs between Winter and Spring II, nearly every day of the week but slihtly highest on Tuesday and Wednesday
- In _pikeperch_, the highest peak is in Summer
- In _wels_, there are tow peaks, both in Spring I and Spring II, with maximum values excluding weekends

**Plot surface and one-dimensional weekly/seasonal differences in horizontal range between the three species**
```
layout(matrix(1:6, nrow = 2))
plot_diff2(m_gam_sp2_cyclic, view=c("seasonally","weekly"), comp=list(Species=c("pike", "pikeperch")), main='Week day by season difference pike-pikeperch',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_gam_sp2_cyclic, view="seasonally", comp=list(Species=c("pike", "pikeperch")), main='Seasonal difference pike-pikeperch')
plot_diff2(m_gam_sp2_cyclic, view=c("seasonally","weekly"), comp=list(Species=c("pikeperch", "wels")), main='Week day by season difference pikeperch-wels',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_gam_sp2_cyclic, view="seasonally", comp=list(Species=c("pikeperch", "wels")), main='Seasonal difference pikeperch-wels')
plot_diff2(m_gam_sp2_cyclic, view=c("seasonally","weekly"), comp=list(Species=c("pike", "wels")), main='Week day by season difference pike-wels',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_gam_sp2_cyclic, view="seasonally", comp=list(Species=c("pike", "wels")), main='Seasonal difference pike-wels')
```
![Horiz_range](/Plots/Horiz_range_7.png "Horiz_range")

### 2.4. Fit GAMM models with uncorrelated errors to data of daily and seasonal changes

Now we fit models using both daily and season as predictors to account for seasonality
```
m_gam_sp1_daily <- bam(sqrt(ranged2d+1) ~ s(seasonally, by = Species, sp = 0.1, k = 5) + s(as.numeric(daily),  by = Species, bs = "ps", k = 7) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp2_daily <- bam(sqrt(ranged2d+1) ~ s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(as.numeric(daily), seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp3_daily <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(as.numeric(daily), bs = "ps", k = 7, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp4_daily <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp5_daily <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(as.numeric(daily), seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp6_daily <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(as.numeric(daily), bs = "cr", k = 7, by = Species) + s(seasonally, bs = "ps", k = 5, by=Species) + ti(seasonally, as.numeric(daily), bs = c("cr", "ps"), by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp7_daily <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(as.numeric(daily), seasonally, bs = "ps", k = 5, by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp8_daily <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(seasonally, bs = "cr", k = 5, by = Species) + s(as.numeric(daily), bs = "ps", k = 7, by = Species) + ti(seasonally, as.numeric(daily), bs = c("cr", "ps"), by= Species)+ s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
```

### Model-selection

#### Based on AIC
```
AIC(m_gam_sp1_daily,m_gam_sp2_daily,m_gam_sp3_daily,m_gam_sp4_daily,m_gam_sp5_daily,m_gam_sp6_daily,m_gam_sp7_daily,m_gam_sp8_daily)
```
```
                      df      AIC
m_gam_sp1_daily  57.25701 71271.27
m_gam_sp2_daily 149.21919 69682.63
m_gam_sp3_daily 140.38088 69764.11
m_gam_sp4_daily 127.74369 69917.75
m_gam_sp5_daily 151.71105 69687.57
m_gam_sp6_daily  62.96427 71206.09
m_gam_sp7_daily 151.32024 69686.78
m_gam_sp8_daily  63.17117 71202.77
```
Model **m_gam_sp2_daily** is preferred (lowest AIC)

#### Based on AICc
```
Cand.mod <- list(m_gam_sp1_daily,m_gam_sp2_daily,m_gam_sp3_daily,m_gam_sp4_daily,m_gam_sp5_daily,m_gam_sp6_daily,m_gam_sp7_daily,m_gam_sp8_daily)

Cand.mod.glm <- lapply(Cand.mod, glm.convert)
aictab(cand.set = Cand.mod.glm, second.ord = TRUE)
```
```
Model selection based on AICc:

       K     AICc Delta_AICc AICcWt Cum.Wt        LL
Mod2 322 69700.44       0.00   0.65   0.65 -34516.02
Mod5 331 69701.78       1.34   0.33   0.98 -34506.98
Mod7 362 69706.96       6.52   0.02   1.00 -34475.99
Mod3 277 69772.69      72.25   0.00   1.00 -34600.36
Mod4 271 69929.72     229.28   0.00   1.00 -34685.26
Mod8 111 71198.36    1497.92   0.00   1.00 -35486.76
Mod6 112 71201.50    1501.06   0.00   1.00 -35487.30
Mod1  63 71267.75    1567.31   0.00   1.00 -35570.42
```

Compare the three best-fit models
```
lrtest(m_gam_sp2_daily,m_gam_sp5_daily)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ s(Species, Id, bs = "re") + s(season, Id,
    bs = "re") + te(as.numeric(daily), seasonally, bs = "ps",
    k = 5, by = Species)
Model 2: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + te(as.numeric(daily), seasonally,
    bs = "ps", k = 5, by = Species)
     #Df LogLik     Df  Chisq Pr(>Chisq)
1 149.22 -34692
2 151.71 -34692 2.4919 0.0436     0.9784
```
```
lrtest(m_gam_sp2_daily,m_gam_sp7_daily)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ s(Species, Id, bs = "re") + s(season, Id,
    bs = "re") + te(as.numeric(daily), seasonally, bs = "ps",
    k = 5, by = Species)
Model 2: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + te(as.numeric(daily), seasonally,
    bs = "ps", k = 5, by = Species) + s(Id, bs = "re")
     #Df LogLik     Df  Chisq Pr(>Chisq)
1 149.22 -34692
2 151.32 -34692 2.1011 0.0509     0.9749
```
Once again, the interaction model **m_gam_sp2_daily** fits the data best and we see that the addition of _body_size_ does not improve overall model fit

### Summary of best-fit model

View GCV and EDF estimates
```
summary(m_gam_sp2_daily)$sp.criterion
```
```
   REML
34981.14
attr(,"Dp")
[1] 4418.004
```
```
summary(m_gam_sp2_daily)$s.table
```
```
                                                        edf     Ref.df           F      p-value
s(Species,Id)                                     22.023909  30.000000 1003.553998 8.024167e-12
s(season,Id)                                      90.605308 127.000000   93.790520 1.112161e-03
te(as.numeric(daily),seasonally):Speciespike      11.033269  12.080393    7.109638 3.966682e-13
te(as.numeric(daily),seasonally):Speciespikeperch  8.054617   9.029957    5.806856 4.582046e-08
te(as.numeric(daily),seasonally):Specieswels      12.203002  12.977134   11.988296 1.357029e-25
```

**Summary table**
```
gamtabs(m_gam_sp2_daily, caption="Summary of m_gam_sp2_daily", comment=FALSE, type='html')
```
<table border=1>
<caption align="bottom"> Summary of m_gam_sp2 </caption>
  <tr> <td> A. parametric coefficients </td> <td align="right"> Estimate </td> <td align="right"> Std. Error </td> <td align="right"> t-value </td> <td align="right"> p-value </td> </tr>
  <tr> <td> (Intercept) </td> <td align="right"> 26.3140 </td> <td align="right"> 1.1959 </td> <td align="right"> 22.0044 </td> <td align="right"> &lt; 0.0001 </td> </tr>
   <tr> <td> B. smooth terms </td> <td align="right"> edf </td> <td align="right"> Ref.df </td> <td align="right"> F-value </td> <td align="right"> p-value </td> </tr>
  <tr> <td> s(Species,Id) </td> <td align="right"> 22.0239 </td> <td align="right"> 30.0000 </td> <td align="right"> 1003.5540 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> s(season,Id) </td> <td align="right"> 90.6053 </td> <td align="right"> 127.0000 </td> <td align="right"> 93.7905 </td> <td align="right"> 0.0011 </td> </tr>
  <tr> <td> te(as.numeric(daily),seasonally):Speciespike </td> <td align="right"> 11.0333 </td> <td align="right"> 12.0804 </td> <td align="right"> 7.1096 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> te(as.numeric(daily),seasonally):Speciespikeperch </td> <td align="right"> 8.0546 </td> <td align="right"> 9.0300 </td> <td align="right"> 5.8069 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> te(as.numeric(daily),seasonally):Specieswels </td> <td align="right"> 12.2030 </td> <td align="right"> 12.9771 </td> <td align="right"> 11.9883 </td> <td align="right"> &lt; 0.0001 </td> </tr>
   <a name=tab.gam></a>
</table>

We can see that:
- The _daily_ by _season_ interaction is significant to response variable in the three species
- The percentage of explained variance is still too low

### Plot model

**Plot summed effects surfaces of season and daily smooths for the three species**
```
layout(matrix(1:6, nrow = 2))
vis.gam(m_gam_sp2_daily, view=c("seasonally","daily"), cond=list(Species='pike'), main = "pike", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(-80,80), pos=.85)
vis.gam(m_gam_sp2_daily, view=c("seasonally","daily"), cond=list(Species='pike'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_gam_sp2_daily, view=c("seasonally","daily"), cond=list(Species='pikeperch'), main = "pikeperch", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(10,65), pos=.85)
vis.gam(m_gam_sp2_daily, view=c("seasonally","daily"), cond=list(Species='pikeperch'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_gam_sp2_daily, view=c("seasonally","daily"), cond=list(Species='wels'), main = "wels", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(-60,120), pos=.85)
vis.gam(m_gam_sp2_daily, view=c("seasonally","daily"), cond=list(Species='wels'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
```
![Horiz_range](/Plots/Horiz_range_8.png "Horiz_range")

- We see that in the three species, the highest peak occurs in Spring I and when _Daily_ values are near 17630 (i.e., 2018-04-09 )

**Plot summed effects surfaces (smooth) with and without random effects**
```
par(mfrow=c(1,3), cex=1.1)
plot_smooth(m_gam_sp2_daily, view="seasonally", cond=list(Species="pike"), rug=FALSE, ylim=c(-70,70), print.summary=FALSE, ylab = "Horizontal range [m]", main='Seasonality, Species=pike')
plot_smooth(m_gam_sp2_daily, view="seasonally", cond=list(Species="pike"), rug=FALSE, add=TRUE, col='red', rm.ranef=TRUE, ylim=c(0,7700), print.summary=FALSE, xpd=TRUE)
legend('bottomleft', legend=c("with R.E.","without R.E."), col=c("black", "red"), lwd=2, bty='n')
plot_smooth(m_gam_sp2_daily, view="seasonally", cond=list(Species="pikeperch"), rug=FALSE, ylim=c(-70,70), print.summary=FALSE, ylab = "Horizontal range [m]", main='Seasonality, Species=pikeperch')
plot_smooth(m_gam_sp2_daily, view="seasonally", cond=list(Species="pikeperch"), rug=FALSE, add=TRUE, col='red', rm.ranef=TRUE, ylim=c(0,7700), print.summary=FALSE, xpd=TRUE)
legend('bottomleft', legend=c("with R.E.","without R.E."), col=c("black", "red"), lwd=2, bty='n')
plot_smooth(m_gam_sp2_daily, view="seasonally", cond=list(Species="wels"), rug=FALSE, ylim=c(-70,70), print.summary=FALSE, ylab = "Horizontal range [m]", main='Seasonality, Species=wels')
plot_smooth(m_gam_sp2_daily, view="seasonally", cond=list(Species="wels"), rug=FALSE, add=TRUE, col='red', rm.ranef=TRUE, ylim=c(0,7700), print.summary=FALSE, xpd=TRUE)
legend('bottomleft', legend=c("with R.E.","without R.E."), col=c("black", "red"), lwd=2, bty='n')
```
![Horiz_range](/Plots/Horiz_range_9.png "Horiz_range")

**Plot surface and one-dimensional daily/seasonal differences in horizontal range between the three species**
```
layout(matrix(1:3, nrow = 1))
plot_diff(m_gam_sp2_daily, view="seasonally", comp=list(Species=c("pike", "pikeperch")), main='Seasonal difference pike-pikeperch')
plot_diff(m_gam_sp2_daily, view="seasonally", comp=list(Species=c("pikeperch", "wels")), main='Seasonal difference pikeperch-wels')
plot_diff(m_gam_sp2_daily, view="seasonally", comp=list(Species=c("pike", "wels")), main='Seasonal difference pike-wels')
```
![Horiz_range](/Plots/Horiz_range_10.png "Horiz_range")

Alternatively, plot fitted model values (daily/seasonally) with following code:
```
data_m_gam_sp2_daily <- visreg(m_gam_sp2_daily, "daily", by = "Species", breaks = c ("pike", "pikeperch", "wels"), gg = TRUE, overlay = TRUE, jitter = TRUE, lwd = 0.5, rug = FALSE, partial = FALSE, plot = FALSE)
plot(data_m_gam_sp2_daily, plot.type="rgl") + theme_bw()
data_m_gam_sp2_season <- visreg(m_gam_sp2_daily, "seasonally", by = "Species", breaks = c ("pike", "pikeperch", "wels"), gg = TRUE, overlay = TRUE, jitter = TRUE, lwd = 0.5, rug = FALSE, partial = FALSE, plot = FALSE)
plot(data_m_gam_sp2_season, plot.type="rgl") + theme_bw()
```

## 3. Build GAMM models using MCMC to perform Bayesian estimation

- Keeping the smooth specification, we re-fit previous GAMM models with Bayesian estimators using the _brm_ function in package **brms**
- With this approach we expect to get better estimates of parameter uncertainity
- For models including a tensor product smooths we use the function _t2_ instead of _te/ti_ (not allowed in brms)

In this representation, the wiggly parts of the spline basis are treated as a random effect and their associated variance parameter controls the degree of wiggliness of the fitted spline.

:books:`library(brms)`   
:books:`library(bayesplot)`   
:books:`library(rstanarm)`   
:books:`library(loo)`   

### 3.1. Fit a Bayesian GAMM model for seasonality data

We re-fit the previous model **m_gam_season** with the following specifications:
- Set MCMC samples to 4000 iterations (increase from default values as in a first run we obtain a warning about parts of the model not converging)
- Set chains to default (lowering this value is likely related to previous warning message)
- Set priors to default values
- Samples thinned (thin=10) to deal with strong autocorrelation in the chains
- Set fitted chains to 2
- Set _adapt_delta_ to 0.99 (increase from default values as in a fisrt run there is a warning about divergent transitions after warmup)

```
m_gam_week_season_bayes <- brm(bf(sqrt(ranged2d+1) ~ s(weekly, bs = "cc", k= 7) + s(seasonally, bs = "cc", k = 5, by = Species) + s(Id, bs = "re")),
          data = data_longit_sub,
          family = gaussian(),
          cores = 4, seed = 17,
          iter = 4000, warmup = 1000, thin = 10, refresh = 0,
          control = list(adapt_delta = 0.99))
```

### Model summary

```
summary(m_gam_week_season_bayes)
```
```
 Family: gaussian
  Links: mu = identity; sigma = identity
Formula: sqrt(ranged2d + 1) ~ s(weekly, bs = "cc", k = 7) + s(seasonally, bs = "cc", k = 5, by = Species) + s(Id, bs = "re")
   Data: data_longit_sub (Number of observations: 8846)
Samples: 4 chains, each with iter = 4000; warmup = 1000; thin = 10;
         total post-warmup samples = 1200

Smooth Terms:
                                   Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
sds(sweekly_1)                         0.15      0.15     0.01     0.47 1.00     1093     1062
sds(sseasonallySpeciespike_1)          1.96      1.91     0.59     6.62 1.00     1093     1060
sds(sseasonallySpeciespikeperch_1)     3.59      2.56     1.24    10.92 1.00     1060     1170
sds(sseasonallySpecieswels_1)          2.07      1.72     0.65     6.78 1.00     1090     1098
sds(sId_1)                             6.96      0.91     5.43     8.98 1.00      872      936

Population-Level Effects:
          Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
Intercept    26.56      1.26    24.11    29.09 1.00      787      955

Family Specific Parameters:
      Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
sigma    13.73      0.10    13.54    13.93 1.00     1272     1009

Samples were drawn using sampling(NUTS). For each parameter, Bulk_ESS
and Tail_ESS are effective sample size measures, and Rhat is the potential
scale reduction factor on split chains (at convergence, Rhat = 1).
```

**How is compared with the non-Bayesian version**

```
gam.vcomp(m_gam_week_season, rescale = FALSE)
```
```
Standard deviations and 0.95 confidence intervals:

                                   std.dev       lower      upper
s(weekly)                       0.08380344  0.02202344  0.3188883
s(seasonally):Speciespike       0.98816336  0.42820469  2.2803739
s(seasonally):Speciespikeperch  2.02012546  0.87719342  4.6522315
s(seasonally):Specieswels       1.07050797  0.44343540  2.5843388
s(Id)                           6.73191819  5.20586818  8.7053150
scale                          13.72901857 13.52772063 13.9333119

Rank: 6/6
```

Non-Bayesian vs. Bayesian posterior means and 95% (Confidence/Credible) Intervals for the variable _seasonally_:
- _pike_: 0.99[0.43, 2.28] vs. 1.96[0.59, 6.62]
- _pikeperch_: 2.02[0.88, 4.65] vs. 3.59[1.24, 10.92]
- _wels_: 1.07[0.44, 2.58] vs. 6.96[5.43, 8.98]

### Plot model

```
cond_smooths <- conditional_smooths(m_gam_week_season_bayes)
```
```
plot(cond_smooths) + theme_bw()
```
![Horiz_range](/Plots/Horiz_range_11.png "Horiz_range")

![Horiz_range](/Plots/Horiz_range_12.png "Horiz_range")

We can see that:
- The estimated smooth for the Bayesian version of this model looks about the same as its non-Bayesian version
- The marginal_smooths() function is effectively the equivalent of the plot() method for mgcv-based GAMs.

### Model diagnostics

- We use Pareto-smoothed importance sampling LOO Cross-Validation for model checking
- First, create a loo object and return an object of class _pareto_k_table_ (matrix form "Count", "Proportion", and "Min. n_eff")

```
loo<-loo(m_gam_week_season_bayes)
pareto_k_table(loo)
loo
```
```
Computed from 1200 by 8846 log-likelihood matrix

         Estimate    SE
elpd_loo -35747.4  74.1
p_loo        44.2   0.8
looic     71494.7 148.2
------
Monte Carlo SE of elpd_loo is 0.2.

All Pareto k estimates are good (k < 0.5).
See help('pareto-k-diagnostic') for details.
```
Returns an integer vector indicating which observations have Pareto k estimates above threshold
```
pareto_k_ids(loo, threshold = 0.5)
```

Returns a vector of the estimated Pareto k parameters
```
pareto_k_values(loo)
```

Returns a vector of the estimated PSIS effective sample sizes
```
psis_n_eff_values(loo)
```

Returns the Monte Carlo standard error (MCSE) estimate for PSIS-LOO. MCSE will be NA if any Pareto k values are above threshold
```
mcse_loo(loo, threshold = 0.7)
```
```
0.1971972
```

**Plot estimates and PSIS diagnostic**

```
plot(m_gam_week_season_bayes, diagnostic = c("k", "n_eff"), label_points = FALSE, main = "PSIS diagnostic plot")
```
![Horiz_range](/Plots/Horiz_range_13.png "Horiz_range")

![Horiz_range](/Plots/Horiz_range_14.png "Horiz_range")

### Posterior predictive checks

In addition to LOO-CV, here we test the proportion of 1s predicted by the model and compare them to the observed number of 1s

**Plot posterior predictive check**
```
prop_zero <- function(y) mean(y == 0)
(prop_zero_test1 <- pp_check(m_gam_week_season_bayes, plotfun = "stat", stat = "prop_zero"))
```
![Horiz_range](/Plots/Horiz_range_15.png "Horiz_range")

**Note:** You can run also the same plot with: `pp_check(m_gam_week_season_bayes)`

**Plot empirical cumulative distribution function (obs. and random draws from the model posterior)**
```
pp_check(m_gam_week_season_bayes, type = "ecdf_overlay")
```
![Horiz_range](/Plots/Horiz_range_16.png "Horiz_range")

### Extract the posterior draws for all parameters

```
sims <- as.matrix(m_gam_week_season_bayes)
dim(sims)
```
```
[1] 4000   174
```
```
para_name <- colnames(sims)
```

**Obtain fi_species-level varying intercept a_j**

draws for overall mean
```
mu_a_sims <- as.matrix(m_gam_week_season_bayes, pars = "(Intercept)")
```

draws for 3 fi_species-level error
```
fish_err <- as.matrix(m_gam_week_season_bayes,regex_pars = "b\\[\\(Intercept\\) Species\\:")
```

draws for 3 species varying intercepts
```
fish_inter <- as.numeric(mu_a_sims) + fish_err
```

**Obtain sigma_y and sigma_alpha^2**

draws for sigma_y
```
signma_y <- as.matrix(m_gam_week_season_bayes, pars = "sigma")
```

draws for sigma_alpha^2
```
sigma_alpha <- as.matrix(m_gam_week_season_bayes, pars = "Sigma[Species:(Intercept),(Intercept)]")
```

### Calculate means, SD, medians and 95% credible intervals of varying intercepts

We summarize the posterior probability distribution of the 4,000 estimates (draws) for a1 examining their quantiles

**Posterior mean of each alpha**
```
a_mean <- apply(X = fish_inter, MARGIN = 2, FUN = mean)
```

**Posterior SD of each alpha**
```
a_sd <- apply(X = fish_inter, MARGIN = 2, FUN = sd)
```

**Posterior median and 95% credible interval**
```
a_quant <- apply(X = fish_inter, MARGIN = 2, FUN = quantile, probs = c(0.025, 0.50, 0.975))
a_quant <- data.frame(t(a_quant))
names(a_quant) <- c("X2.5.", "X50.", "X97.5.")
```

**Combine summary statistics of posterior simulation draws**
```
a_df <- data.frame(a_mean, a_sd, a_quant)
round(head(a_df), 2)
```
```
                                  a_mean a_sd  Q2.5   Q50 Q97.5
b_Intercept                        53.12 2.51 48.22 53.09 58.18
sds_sweekly_1                      26.71 1.28 24.28 26.69 29.26
sds_sseasonallySpeciespike_1       28.52 2.28 25.45 28.22 33.28
sds_sseasonallySpeciespikeperch_1  30.15 2.85 26.48 29.52 37.54
sds_sseasonallySpecieswels_1       28.63 2.16 25.43 28.30 34.14
sds_sId_1                          33.52 1.61 30.69 33.37 36.88
```

### Caterpillar plot for all fish intercepts

Sort dataframe containing an estimated alpha mean and sd for every fi_species
```
a_df <- a_df[order(a_df$a_mean), ]
a_df$a_rank <- c(1 : dim(a_df)[1]) # a vector of fi_species rank
```

**Plot fish-level alphas posterior mean and 95% credible interval**
```
ggplot(data = data_longit_sub,
       aes(x = a_rank, y = a_mean)) +
       geom_pointrange(aes(ymin = X2.5., max = X97.5.), position = position_jitter(width = 0.1, height = 0)) +
       geom_hline(yintercept = mean(a_df$a_mean), size = 0.5, col = "red") +
       scale_x_continuous("Rank", breaks = seq(from = 0, to = 80, by = 5)) +
       scale_y_continuous(expression(paste("varying intercept, ", alpha[j]))) +
       theme_bw( base_family = "serif")
```
![Horiz_range](/Plots/Horiz_range_17.png "Horiz_range")

### Differences between species averages

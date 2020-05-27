## Create a dataset of daily and weekly distance range for each species

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

:books:`library(itsadug)`
:books:`library(mgcv)`

### 2.1. Create a new dataset

First, lets subset horizontal range data into a new dataset _data_longit_sub_ for easing models fitting
```
data_longit_sub <- data.table(ranged2d = data_longit[, ranged2d], Species = data_longit[, Species], Id = data_longit[, fi_fishid], weekly = data_longit[, week_day_num], season = data_longit[, season], seasonally = data_longit[, season_num], daily = data_longit[, date], body_size = data_longit[, ca_tl_mm])
```

### 2.4. Fit a global GAMM model for seasonality in horizontal range

We fit a model including a smooth term for season grouped by Species and one-random term added
```
m_gam_season<- bam(sqrt(ranged2d+1) ~ te(seasonally, bs = c("cr", "ps"), by=Species)+ s(Id, bs = "re"), data = data_longit_sub_GAM, family = gaussian, na.action=na.omit)
```
```
summary(m_gam_season)
```
```
Family: gaussian
Link function: identity

Formula:
sqrt(ranged2d + 1) ~ te(seasonally, bs = c("cr", "ps"), by = Species) +
    s(Id, bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)   26.530      1.219   21.75   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                                   edf Ref.df     F  p-value
te(seasonally):Speciespike       3.716  3.957 42.89  < 2e-16 ***
te(seasonally):Speciespikeperch  3.898  3.994 41.68  < 2e-16 ***
te(seasonally):Specieswels       3.351  3.790 19.69 5.33e-15 ***
s(Id)                           29.533 30.000 67.98  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.221   Deviance explained = 22.4%
fREML =  35581  Scale est. = 186.24    n = 8806
```
- We see that the r-squared value is too low and we should search a more sutiable model

```
draw(m_gam_season, ncol = 2)
```
![Horiz_range](/Plots/Horiz_range_3.png "Horiz_range")


### 2.3. Fit GAMM models for week and season seaonality

- Fit models using both week (values from 1 to 7) and season (cycles from 1 to 5) as predictors to account for seasonality
- Include a simple random term for subject identity
- Include body size as a predictor
- Forecast models add a main effect for Species allowing the shape of the smooths to vary between _perk_, _pikeperch_ and _wels_
- Add an interaction (5 models) between season and week to account for changes in amount of seasonality over time
- Add a tensor product interaction term in presence of smooth functions for main-effects using the _ti_ function
```
m_gam_sp1 <- bam(sqrt(ranged2d+1) ~ s(seasonally, by = Species, sp = 0.1, k = 5) + s(weekly,  by = Species, bs = "ps", k = 7) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp2 <- bam(sqrt(ranged2d+1) ~ s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(weekly, seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp3 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(weekly, bs = "ps", k = 7, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp4 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp5 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(weekly, seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp6 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(weekly, bs = "cr", k = 7, by=Species) + s(seasonally, bs = "ps", k = 5, by=Species) + ti(seasonally, weekly, bs = c("cr", "ps"), by= Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp7 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(weekly, seasonally, bs = "ps", k = 5, by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_sp8 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(seasonally, bs = "cr", k = 5, by = Species) + s(weekly, bs = "ps", k = 7, by = Species) + ti(seasonally, weekly, bs = c("cr", "ps"), by= Species)+ s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
```

### 2.3. Model-selection

:books:`library(MASS)`
:books:`library(AICcmodavg)`

#### Based on AICc
```
Cand.mod <- list(m_gam_sp1,m_gam_sp2,m_gam_sp3,m_gam_sp4,m_gam_sp5,m_gam_sp6,m_gam_sp7,m_gam_sp8)

Cand.mod.glm <- lapply(Cand.mod, glm.convert)
aictab(cand.set = Cand.mod.glm, second.ord = TRUE)
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

Compare three best-fit models
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

#### Based on AIC

```
AIC(m_gam_sp1,m_gam_sp2,m_gam_sp3,m_gam_sp4,m_gam_sp5,m_gam_sp6,m_gam_sp7,m_gam_sp8)
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
Again model **m_gam_sp2** is preferred (lowest AIC)

**Note:** Comparing these models with GCV.Cp (re-fitting models with method=" GCV.Cp" instead "REML" the results are the same)

### 2.4. Summary of best-fit model

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

**Summary table** of the winning model
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

Form results of the winning model we can see that:
- The _weekly_ by _season_ interaction is significant to response variable only in _pike_
- The is inter-individual variation within sesions
- There is high variability between individuals of the three species
- If we fit a model adding an random term _s(Id, bs = 're')_ to the model, the term is not significant and undersmooths; finally, the model is not preferred


### 2.5. Plot model

**Plot surface and one-dimensional seasonal and weekly differences in horizontal range**
```
layout(matrix(1:6, nrow = 2))
plot_diff2(m_gam_sp2, view=c("weekly","seasonally"),
        comp=list(Species=c("pike", "pikeperch")),
        zlim=c(-5,7.5),
        main='Week by season difference pike-pikeperch',
        print.summary=FALSE)
plot_diff(m_gam_sp2, view="seasonally",
        comp=list(Species=c("pike", "pikeperch")),
        main='Seasonal differences pike-pikeperch')
plot_diff2(m_gam_sp2, view=c("weekly","seasonally"),
        comp=list(Species=c("pikeperch", "wels")),
        zlim=c(-5,7.5),
        main='Week by season difference pikeperch-wels',
        print.summary=FALSE)
plot_diff(m_gam_sp2, view="seasonally",
        comp=list(Species=c("pikeperch", "wels")),
        main='Seasonal differences pikeperch-wels')
plot_diff2(m_gam_sp2, view=c("weekly","seasonally"),
        comp=list(Species=c("pike", "wels")),
        zlim=c(-5,7.5),
        main='Week by season difference pike-wels',
        print.summary=FALSE)
plot_diff(m_gam_sp2, view="seasonally",
        comp=list(Species=c("pike", "wels")),
        main='Seasonal differences pike-wels')
```
![Horiz_range](/Plots/Horiz_range_4.png "Horiz_range")

**Plot summed effects surfaces (smooth) for the three species**

```
layout(matrix(1:3, nrow = 1))
vis.gam(m_gam_sp2, view=c("weekly","seasonally"), cond=list(Species='pike'), main = "pike", plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
pvisgam(m_gam_sp2, view=c("weekly","seasonally"), select=8, cond=list(Species='pike'), main = "pike", plot.type = "contour", color = "terrain", contour.col = "black", lwd = 2)
vis.gam(m_gam_sp2, view=c("weekly","seasonally"), cond=list(Species='pikeperch'), main = "pikeperch", plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
pvisgam(m_gam_sp2, view=c("weekly","seasonally"), select=9, cond=list(Species='pikeperch'), main = "pikeperch", plot.type = "contour", color = "terrain", contour.col = "black", lwd = 2)
vis.gam(m_gam_sp2, view=c("weekly","seasonally"), cond=list(Species='wels'), main = "wels", plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
pvisgam(m_gam_sp2, view=c("weekly","seasonally"), select=10, cond=list(Species='wels'), main = "wels", plot.type = "contour", color = "terrain", contour.col = "black", lwd = 2)
```
![Horiz_range](/Plots/Horiz_range_5.png "Horiz_range")





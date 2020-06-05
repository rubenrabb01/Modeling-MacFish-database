# Analysis of range of distances for horizontal movement using imputed data 

## 1. Impute missing time series data

Based on the previously created dataset (see _Horizontal_range.md_), we complete the time series inserting rows for missing _daily_ values (ie., date) and, for each individual, replace missing values using LOCF (Last Observation Carried Forward)
```
data_longit_sub_complete<- data_longit_sub %>% complete(Id, daily) %>% fill(everything())
data_longit_sub_complete<-as.data.frame(data_longit_sub_complete)
data_longit_sub_complete$day_num <- lapply(data_longit_sub_complete$daily, ts, start = c(2017, 04, 27), frequency = 1)
```

## 2. Build GAMM models and conduct model-selection

:books:`library(mgcv)`  

### 2.2. Fit a global GAMM model with uncorrelated errors to seasonality data

- Fit a model assuming all observations are indpependent and including a smooth term for seasonallity grouped by Species and add one-random term added for subject identity
- We set k for the variable _seasonally_ to 5, which is the number of unique values
- Include a simple random term for subject identity
- Horizontal range might be expected to vary cyclically across seasons so we need to specify bs = "cc"  (i.e., cyclic cubic regression splines), hence, seasonally shows no discontinuity between Spring I and Spring II and both ends line up
```
m_imp_gam_season<- bam(sqrt(ranged2d+1) ~ s(seasonally, bs = "cc", k = 5, by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub_complete, method = "REML")
```
```
summary(m_imp_gam_season)
```
```
Family: gaussian
Link function: identity

Formula:
sqrt(ranged2d + 1) ~ s(seasonally, bs = "cc", k = 5, by = Species) +
    s(Id, bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)   24.994      1.363   18.34   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                                  edf Ref.df      F  p-value
s(seasonally):Speciespike       2.845      3 464.22  < 2e-16 ***
s(seasonally):Speciespikeperch  2.971      3 680.69  < 2e-16 ***
s(seasonally):Specieswels       2.851      3  47.98 1.21e-06 ***
s(Id)                          29.720     30 103.16  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.285   Deviance explained = 28.8%
-REML =  42963  Scale est. = 181.83    n = 10664
```

### Plot model

:books:`library(gratia)`  

```
draw(m_imp_gam_season, ncol = 2)
```
![Horiz_range](/Plots/Horiz_range_3_imputed.png "Horiz_range")

- We see that the r-squared value is too low and we should search a more suitable model

### 2.3. Fit a GAMM model with uncorrelated errors to weekly and seasonality data

- Now we set k to 7 and 5 for both _weekly_ and _seasonally, respectively
- Note that _weekly_ refers to day of the week
```
m_imp_gam_week_season<- bam(sqrt(ranged2d+1) ~ s(weekly, bs = "cc", k= 7) + s(seasonally, bs = "cc", k = 5, by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub_complete, method = "REML")
```
```
summary(m_imp_gam_week_season)
```
```
Family: gaussian
Link function: identity

Formula:
sqrt(ranged2d + 1) ~ s(weekly, bs = "cc", k = 7) + s(seasonally,
    bs = "cc", k = 5, by = Species) + s(Id, bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)   24.992      1.356   18.43   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                                  edf Ref.df      F  p-value
s(weekly)                       3.943      5  32.91 3.76e-07 ***
s(seasonally):Speciespike       2.841      3 190.78  < 2e-16 ***
s(seasonally):Speciespikeperch  2.970      3 371.78  < 2e-16 ***
s(seasonally):Specieswels       2.851      3  46.16 9.79e-13 ***
s(Id)                          29.716     30 102.16  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.288   Deviance explained = 29.1%
-REML =  42947  Scale est. = 181.08    n = 10664
```

### Plot model

```
draw(m_imp_gam_week_season, ncol = 2)
```
![Horiz_range](/Plots/Horiz_range_31_imputed.png "Horiz_range")

**Plot surface and one-dimensional seasonal differences in horizontal range**

Lets check differences in _seasonally_ smooth surfaces between species

```
layout(matrix(1:3, nrow = 1))
plot_diff(m_imp_gam_week_season, view="seasonally", comp=list(Species=c("pike", "pikeperch")), main='Seasonal difference pike-pikeperch')
plot_diff(m_imp_gam_week_season, view="seasonally", comp=list(Species=c("pikeperch", "wels")), main='Seasonal difference pikeperch-wels')
plot_diff(m_imp_gam_week_season, view="seasonally", comp=list(Species=c("pike", "wels")), main='Seasonal difference pike-wels')
```
![Horiz_range](/Plots/Horiz_range_32_imputed.png "Horiz_range")

- A slightly better fit model

### 2.4. Fit a GAMM model with uncorrelated errors to daily and seasonality data

```
m_imp_gam_daily_season<- bam(sqrt(ranged2d+1) ~ s(as.numeric(daily)) + s(seasonally, bs = "cc", k = 5, by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub_complete, method = "REML")
```
```
summary(m_imp_gam_daily_season)
```
```
Family: gaussian
Link function: identity

Formula:
sqrt(ranged2d + 1) ~ s(as.numeric(daily)) + s(seasonally, bs = "cc",
    k = 5, by = Species) + s(Id, bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)   24.993      1.334   18.74   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                                  edf Ref.df       F  p-value
s(as.numeric(daily))            8.628  8.959   13.56  < 2e-16 ***
s(seasonally):Speciespike       2.876  3.000 1119.13  < 2e-16 ***
s(seasonally):Speciespikeperch  2.940  3.000  781.97  < 2e-16 ***
s(seasonally):Specieswels       2.865  3.000   83.27 4.65e-06 ***
s(Id)                          29.706 30.000   95.01  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.293   Deviance explained = 29.6%
-REML =  42921  Scale est. = 179.77    n = 10664
```
```
draw(m_imp_gam_daily_season, ncol = 2)
```
![Horiz_range](/Plots/Horiz_range_33_imputed.png "Horiz_range")

- These three models explain only little variation in horizontal range, thus we need to conduct further model search

### 2.5. Fit GAMM models for week day/season seasonality descomposition

- Fit models with both _weekly_ and _seasonally_ as predictors variables to account for seasonality
- Include body size as a predictor variable and additional random terms for season and species
- Forecast models add a main effect for Species allowing the shape of the smooths to vary between _perk_, _pikeperch_ and _wels_
- Add an interaction (5 out of 8 models) between season and week to account for changes in amount of seasonality over time
- Add a tensor product interaction term in presence of smooth functions for main-effects using the _ti_ function (1 out of 8 models)
```
m_imp_gam_sp1 <- bam(sqrt(ranged2d+1) ~ s(seasonally, by = Species, sp = 0.1, k = 5) + s(weekly,  by = Species, bs = "ps", k = 7) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_imp_gam_sp2 <- bam(sqrt(ranged2d+1) ~ s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(weekly, seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_imp_gam_sp3 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(weekly, bs = "ps", k = 7, by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_imp_gam_sp4 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_imp_gam_sp5 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(weekly, seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_imp_gam_sp6 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(weekly, bs = "cr", k = 7, by = Species) + s(seasonally, bs = "ps", k = 5, by = Species) + ti(seasonally, weekly, bs = c("cr", "ps"), by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_imp_gam_sp7 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(weekly, seasonally, bs = "ps", k = 5, by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_imp_gam_sp8 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(seasonally, bs = "cr", k = 5, by = Species) + s(weekly, bs = "ps", k = 7, by = Species) + ti(seasonally, weekly, bs = c("cr", "ps"), by= Species)+ s(Id, bs = "re"), family = gaussian, data = data_longit_sub_complete, method = "REML")
```
**Note:** The models have been fitted also with inverse gaussian distribution for skewed data (the histograma shows right skewness) and compared to normal models but they are not significantly better

### Model-selection

:books:`library(MASS)`  
:books:`library(AICcmodavg)`  
:books:`library(lmtest)`  

#### Based on AIC
```
AIC(m_imp_gam_sp1,m_imp_gam_sp2,m_imp_gam_sp3,m_imp_gam_sp4,m_imp_gam_sp5,m_imp_gam_sp6,m_imp_gam_sp7,m_imp_gam_sp8)
```
```
                     df      AIC
m_imp_gam_sp1  54.94736 85587.88
m_imp_gam_sp2 168.83805 83406.05
m_imp_gam_sp3 142.86137 83436.91
m_imp_gam_sp4 130.86528 83468.60
m_imp_gam_sp5 169.53741 83407.61
m_imp_gam_sp6  86.86113 85507.66
m_imp_gam_sp7 169.46569 83407.58
m_imp_gam_sp8  85.53421 85507.17
```
Model **m_imp_gam_sp2** is preferred (lowest AIC)

**Note:** Comparing these models with GCV.Cp (re-fitting models with method=" GCV.Cp" instead "REML" the results are the same)

#### Based on AICc
```
Cand.mod <- list(m_imp_gam_sp1,m_imp_gam_sp2,m_imp_gam_sp3,m_imp_gam_sp4,m_imp_gam_sp5,m_imp_gam_sp6,m_imp_gam_sp7,m_imp_gam_sp8)

Cand.mod.glm <- lapply(Cand.mod, glm.convert)
aictab(cand.set = Cand.mod.glm, second.ord = TRUE)
```
```
Model selection based on AICc:

       K     AICc Delta_AICc AICcWt Cum.Wt        LL
Mod2 322 83408.07       0.00   0.59   0.59 -41371.98
Mod5 331 83409.05       0.98   0.36   0.96 -41362.89
Mod7 362 83413.31       5.25   0.04   1.00 -41331.90
Mod3 277 83442.26      34.19   0.00   1.00 -41436.71
Mod4 271 83478.25      70.18   0.00   1.00 -41461.03
Mod6 120 85495.42    2087.36   0.00   1.00 -42626.33
Mod8 120 85495.46    2087.40   0.00   1.00 -42626.35
Mod1  63 85585.30    2177.24   0.00   1.00 -42729.27
```

Compare the three best-fit models
```
lrtest(m_imp_gam_sp7,m_imp_gam_sp2)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + te(weekly, seasonally, bs = "ps",
    k = 5, by = Species) + s(Id, bs = "re")
Model 2: sqrt(ranged2d + 1) ~ s(Species, Id, bs = "re") + s(season, Id,
    bs = "re") + te(weekly, seasonally, bs = "ps", k = 5, by = Species)
     #Df LogLik       Df  Chisq Pr(>Chisq)
1 169.47 -41534
2 168.84 -41534 -0.62764 0.2738     0.6008
```
```
lrtest(m_imp_gam_sp5,m_imp_gam_sp2)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + te(weekly, seasonally, bs = "ps",
    k = 5, by = Species)
Model 2: sqrt(ranged2d + 1) ~ s(Species, Id, bs = "re") + s(season, Id,
    bs = "re") + te(weekly, seasonally, bs = "ps", k = 5, by = Species)
     #Df LogLik       Df  Chisq Pr(>Chisq)
1 169.54 -41534
2 168.84 -41534 -0.69937 0.1554     0.6934
```
The interaction model **m_imp_gam_sp2** is preferred to both single terms models

### Summary of best-fit model

View GCV and EDF estimates
```
summary(m_imp_gam_sp2)$sp.criterion
```
```
 REML
41864.1
attr(,"Dp")
[1] 5326.999
```
```
summary(m_imp_gam_sp2)$s.table
```
```
                                             edf     Ref.df           F      p-value
s(Species,Id)                          21.872030  31.000000 1350.384337 1.565868e-09
s(season,Id)                           99.468576 128.000000  149.490545 2.614756e-04
te(weekly,seasonally):Speciespike      17.718366  20.205998    3.557153 9.575310e-08
te(weekly,seasonally):Speciespikeperch 13.415649  15.977314    2.574311 5.601980e-04
te(weekly,seasonally):Specieswels       5.311792   6.116416    1.921447 7.393068e-02
```

**Summary table** of the winning model
```
gamtabs(m_imp_gam_sp2, caption="Summary of m_imp_gam_sp2", comment=FALSE, type='html')
```
<table border=1>
<caption align="bottom"> Summary of m_imp_gam_sp2 </caption>
  <tr> <td> A. parametric coefficients </td> <td align="right"> Estimate </td> <td align="right"> Std. Error </td> <td align="right"> t-value </td> <td align="right"> p-value </td> </tr>
  <tr> <td> (Intercept) </td> <td align="right"> 25.8243 </td> <td align="right"> 1.2105 </td> <td align="right"> 21.3339 </td> <td align="right"> &lt; 0.0001 </td> </tr>
   <tr> <td> B. smooth terms </td> <td align="right"> edf </td> <td align="right"> Ref.df </td> <td align="right"> F-value </td> <td align="right"> p-value </td> </tr>
  <tr> <td> s(Species,Id) </td> <td align="right"> 21.8720 </td> <td align="right"> 31.0000 </td> <td align="right"> 1350.3843 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> s(season,Id) </td> <td align="right"> 99.4686 </td> <td align="right"> 128.0000 </td> <td align="right"> 149.4905 </td> <td align="right"> 0.0003 </td> </tr>
  <tr> <td> te(weekly,seasonally):Speciespike </td> <td align="right"> 17.7184 </td> <td align="right"> 20.2060 </td> <td align="right"> 3.5572 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> te(weekly,seasonally):Speciespikeperch </td> <td align="right"> 13.4156 </td> <td align="right"> 15.9773 </td> <td align="right"> 2.5743 </td> <td align="right"> 0.0006 </td> </tr>
  <tr> <td> te(weekly,seasonally):Specieswels </td> <td align="right"> 5.3118 </td> <td align="right"> 6.1164 </td> <td align="right"> 1.9214 </td> <td align="right"> 0.0739 </td> </tr>
   <a name=tab.gam></a>
</table>

- We can see that the _weekly_ by _seasonally_ interaction is significant in _pike_ and _pikeperch_ whilst it is marginally significant in _wels_

### Plot model

:books:`library(itsadug)`  
:books:`library(rgl)`  

**Plot summed effects surfaces (smooth) for the three species**
```
layout(matrix(1:6, nrow = 2))
vis.gam(m_imp_gam_sp2, view=c("seasonally","weekly"), cond=list(Species='pike'), main = "pike", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(28,48), pos=.15)
vis.gam(m_imp_gam_sp2, view=c("seasonally","weekly"), cond=list(Species='pike'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_imp_gam_sp2, view=c("seasonally","weekly"), cond=list(Species='pikeperch'), main = "pikeperch", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(14,24), pos=.15)
vis.gam(m_imp_gam_sp2, view=c("seasonally","weekly"), cond=list(Species='pikeperch'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_imp_gam_sp2, view=c("seasonally","weekly"), cond=list(Species='wels'), main = "wels", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(28,35), pos=.15)
vis.gam(m_imp_gam_sp2, view=c("seasonally","weekly"), cond=list(Species='wels'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
```
![Horiz_range](/Plots/Horiz_range_4_imputed.png "Horiz_range")

We see that:
- In _pike_, the highest peak occurs in Spring II and mostly on weekend
- In _pikeperch_, the highest peak is in Spring I and between Monday to Wednesday
- In _wels_, the highest peak is in Spring I and Monday

**Plot surface and one-dimensional weekly/seasonal differences in horizontal range between the three species**
```
layout(matrix(1:6, nrow = 2))
plot_diff2(m_imp_gam_sp2, view=c("seasonally","weekly"), comp=list(Species=c("pike", "pikeperch")), main='Week day by season difference pike-pikeperch',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_imp_gam_sp2, view="seasonally", comp=list(Species=c("pike", "pikeperch")), main='Seasonal difference pike-pikeperch')
plot_diff2(m_imp_gam_sp2, view=c("seasonally","weekly"), comp=list(Species=c("pikeperch", "wels")), main='Week day by season difference pikeperch-wels',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_imp_gam_sp2, view="seasonally", comp=list(Species=c("pikeperch", "wels")), main='Seasonal difference pikeperch-wels')
plot_diff2(m_imp_gam_sp2, view=c("seasonally","weekly"), comp=list(Species=c("pike", "wels")), main='Week day by season difference pike-wels',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_imp_gam_sp2, view="seasonally", comp=list(Species=c("pike", "wels")), main='Seasonal difference pike-wels')
```
![Horiz_range](/Plots/Horiz_range_5_imputed.png "Horiz_range")

### How would dimensional terms change if we would consider cyclical variation in horizontal range?

Now we re-fit the previous best-fit mode using cyclic cubic splines with both _weekly_ (values from 1 to 7) and _seasonally_ (cycles from 1 to 5) as predictors to account for cyclic seasonality in horizontal range
```
m_imp_gam_sp2_cyclic <- bam(sqrt(ranged2d+1) ~ s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(weekly, seasonally, bs = "cc", by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
```
```
summary(m_imp_gam_sp2_cyclic)$s.table
```
```
                                              edf Ref.df          F      p-value
s(Species,Id)                           20.857349     31 1339.94992 1.202348e-07
s(season,Id)                           103.597358    130  165.82055 6.185788e-04
te(weekly,seasonally):Speciespike       10.767547     15  619.80842 1.532268e-06
te(weekly,seasonally):Speciespikeperch   8.336153     15   87.26920 8.708008e-04
te(weekly,seasonally):Specieswels        5.531690     15   72.85423 2.524844e-01
```
- In contrast to non-imputed data now we see that the _weekly_ by _season_ interaction is significant both in _pike_ and _pikeperch_
- Lets plot the model to see differences with non-cyclic splines

### Plot model
```
layout(matrix(1:6, nrow = 2))
vis.gam(m_imp_gam_sp2_cyclic, view=c("seasonally","weekly"), cond=list(Species='pike'), main = "pike", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(28,48), pos=.15)
vis.gam(m_imp_gam_sp2_cyclic, view=c("seasonally","weekly"), cond=list(Species='pike'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_imp_gam_sp2_cyclic, view=c("seasonally","weekly"), cond=list(Species='pikeperch'), main = "pikeperch", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(14,24), pos=.15)
vis.gam(m_imp_gam_sp2_cyclic, view=c("seasonally","weekly"), cond=list(Species='pikeperch'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_imp_gam_sp2_cyclic, view=c("seasonally","weekly"), cond=list(Species='wels'), main = "wels", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(28,35), pos=.15)
vis.gam(m_imp_gam_sp2_cyclic, view=c("seasonally","weekly"), cond=list(Species='wels'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
```
![Horiz_range](/Plots/Horiz_range_6_imputed.png "Horiz_range")

We see that:
- In _pike_, the highest peak occurs in Winter, being highest between Monday and Tuesday. There is a substantial lowest occurrence in summer mostly focused around Thursdays
- In _pikeperch_, the highest values extend from Autumn to Spring II with its maximum in Winter. There are two lowest peaks in this interval corresponding to days between middle Wednesday to middle Monday next week whilst highest peak days are between Tuesday and Wednesday
- In _wels_, the pattern is very similar as that of the non-imputed dataset

**Plot surface and one-dimensional weekly/seasonal differences in horizontal range between the three species**
```
layout(matrix(1:6, nrow = 2))
plot_diff2(m_imp_gam_sp2_cyclic, view=c("seasonally","weekly"), comp=list(Species=c("pike", "pikeperch")), main='Week day by season difference pike-pikeperch',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_imp_gam_sp2_cyclic, view="seasonally", comp=list(Species=c("pike", "pikeperch")), main='Seasonal difference pike-pikeperch')
plot_diff2(m_imp_gam_sp2_cyclic, view=c("seasonally","weekly"), comp=list(Species=c("pikeperch", "wels")), main='Week day by season difference pikeperch-wels',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_imp_gam_sp2_cyclic, view="seasonally", comp=list(Species=c("pikeperch", "wels")), main='Seasonal difference pikeperch-wels')
plot_diff2(m_imp_gam_sp2_cyclic, view=c("seasonally","weekly"), comp=list(Species=c("pike", "wels")), main='Week day by season difference pike-wels',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_imp_gam_sp2_cyclic, view="seasonally", comp=list(Species=c("pike", "wels")), main='Seasonal difference pike-wels')
```
![Horiz_range](/Plots/Horiz_range_7_imputed.png "Horiz_range")

### 2.4. Fit GAMM models with uncorrelated errors to data of daily and seasonal changes

Now we fit models using both daily and season as predictors to account for seasonality
```
m_imp_gam_sp1_daily <- bam(sqrt(ranged2d+1) ~ s(seasonally, by = Species, sp = 0.1, k = 5) + s(as.numeric(daily),  by = Species, bs = "ps", k = 7) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_imp_gam_sp2_daily <- bam(sqrt(ranged2d+1) ~ s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(as.numeric(daily), seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_imp_gam_sp3_daily <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(as.numeric(daily), bs = "ps", k = 7, by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_imp_gam_sp4_daily <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_imp_gam_sp5_daily <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(as.numeric(daily), seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_imp_gam_sp6_daily <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(as.numeric(daily), bs = "cr", k = 7, by = Species) + s(seasonally, bs = "ps", k = 5, by=Species) + ti(seasonally, as.numeric(daily), bs = c("cr", "ps"), by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_imp_gam_sp7_daily <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(as.numeric(daily), seasonally, bs = "ps", k = 5, by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_imp_gam_sp8_daily <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(seasonally, bs = "cr", k = 5, by = Species) + s(as.numeric(daily), bs = "ps", k = 7, by = Species) + ti(seasonally, as.numeric(daily), bs = c("cr", "ps"), by= Species)+ s(Id, bs = "re"), family = gaussian, data = data_longit_sub_complete, method = "REML")
```

### Model-selection

#### Based on AIC
```
AIC(m_imp_gam_sp1_daily,m_imp_gam_sp2_daily,m_imp_gam_sp3_daily,m_imp_gam_sp4_daily,m_imp_gam_sp5_daily,m_imp_gam_sp6_daily,m_imp_gam_sp7_daily,m_imp_gam_sp8_daily)
```
```
                           df      AIC
m_imp_gam_sp1_daily  58.55865 85430.70
m_imp_gam_sp2_daily 157.74141 83042.38
m_imp_gam_sp3_daily 148.26332 83249.81
m_imp_gam_sp4_daily 130.86528 83468.60
m_imp_gam_sp5_daily 159.84231 83046.24
m_imp_gam_sp6_daily  77.68199 85228.08
m_imp_gam_sp7_daily 158.47024 83043.60
m_imp_gam_sp8_daily  69.76082 85255.72
```
Model **m_imp_gam_sp2_daily** is preferred (lowest AIC)

#### Based on AICc
```
Cand.mod <- list(m_imp_gam_sp1_daily,m_imp_gam_sp2_daily,m_imp_gam_sp3_daily,m_imp_gam_sp4_daily,m_imp_gam_sp5_daily,m_imp_gam_sp6_daily,m_imp_gam_sp7_daily,m_imp_gam_sp8_daily)

Cand.mod.glm <- lapply(Cand.mod, glm.convert)
aictab(cand.set = Cand.mod.glm, second.ord = TRUE)
```
```
Model selection based on AICc:

       K     AICc Delta_AICc AICcWt Cum.Wt        LL
Mod2 322 83055.72       0.00   0.58   0.58 -41195.80
Mod5 331 83056.56       0.84   0.38   0.96 -41186.64
Mod7 362 83060.88       5.16   0.04   1.00 -41155.68
Mod3 277 83253.93     198.21   0.00   1.00 -41342.55
Mod4 271 83478.25     422.53   0.00   1.00 -41461.03
Mod6 116 85221.67    2165.95   0.00   1.00 -42493.55
Mod8 113 85250.92    2195.20   0.00   1.00 -42511.24
Mod1  63 85428.51    2372.79   0.00   1.00 -42650.87
```

Compare the three best-fit models
```
lrtest(m_imp_gam_sp2_daily,m_imp_gam_sp5_daily)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ s(Species, Id, bs = "re") + s(season, Id,
    bs = "re") + te(as.numeric(daily), seasonally, bs = "ps",
    k = 5, by = Species)
Model 2: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + te(as.numeric(daily), seasonally,
    bs = "ps", k = 5, by = Species)
     #Df LogLik     Df Chisq Pr(>Chisq)
1 157.74 -41363
2 159.84 -41363 2.1009 0.351      0.839
```
```
lrtest(m_imp_gam_sp5_daily,m_imp_gam_sp7_daily)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + te(as.numeric(daily), seasonally,
    bs = "ps", k = 5, by = Species)
Model 2: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + te(as.numeric(daily), seasonally,
    bs = "ps", k = 5, by = Species) + s(Id, bs = "re")
     #Df LogLik      Df  Chisq Pr(>Chisq)
1 159.84 -41363
2 158.47 -41363 -1.3721 0.1056     0.7452
```
Once again, the interaction model **m_imp_gam_sp2_daily** fits the data best and we see that the addition of _body_size_ does not improve overall model fit

### Summary of best-fit model

View GCV and EDF estimates
```
summary(m_imp_gam_sp2_daily)$sp.criterion
```
```
REML
41694.95
attr(,"Dp")
[1] 5327
```
```
summary(m_imp_gam_sp2_daily)$s.table
```
```
                                                       edf     Ref.df           F      p-value
s(Species,Id)                                     21.67755  31.000000 1245.920442 3.369099e-07
s(season,Id)                                      93.92731 128.000000  142.907847 6.331782e-03
te(as.numeric(daily),seasonally):Speciespike      14.43355  15.359218   16.126313 4.304208e-40
te(as.numeric(daily),seasonally):Speciespikeperch  8.03303   9.034624    7.559121 2.607575e-11
te(as.numeric(daily),seasonally):Specieswels      14.27979  15.300527   11.112362 1.793698e-27
```

**Summary table**
```
gamtabs(m_imp_gam_sp2_daily, caption="Summary of m_imp_gam_sp2_daily", comment=FALSE, type='html')
```
<table border=1>
<caption align="bottom"> Summary of m_imp_gam_sp2_daily </caption>
  <tr> <td> A. parametric coefficients </td> <td align="right"> Estimate </td> <td align="right"> Std. Error </td> <td align="right"> t-value </td> <td align="right"> p-value </td> </tr>
  <tr> <td> (Intercept) </td> <td align="right"> 25.2554 </td> <td align="right"> 1.2295 </td> <td align="right"> 20.5412 </td> <td align="right"> &lt; 0.0001 </td> </tr>
   <tr> <td> B. smooth terms </td> <td align="right"> edf </td> <td align="right"> Ref.df </td> <td align="right"> F-value </td> <td align="right"> p-value </td> </tr>
  <tr> <td> s(Species,Id) </td> <td align="right"> 21.6775 </td> <td align="right"> 31.0000 </td> <td align="right"> 1245.9204 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> s(season,Id) </td> <td align="right"> 93.9273 </td> <td align="right"> 128.0000 </td> <td align="right"> 142.9078 </td> <td align="right"> 0.0063 </td> </tr>
  <tr> <td> te(as.numeric(daily),seasonally):Speciespike </td> <td align="right"> 14.4335 </td> <td align="right"> 15.3592 </td> <td align="right"> 16.1263 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> te(as.numeric(daily),seasonally):Speciespikeperch </td> <td align="right"> 8.0330 </td> <td align="right"> 9.0346 </td> <td align="right"> 7.5591 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> te(as.numeric(daily),seasonally):Specieswels </td> <td align="right"> 14.2798 </td> <td align="right"> 15.3005 </td> <td align="right"> 11.1124 </td> <td align="right"> &lt; 0.0001 </td> </tr>
   <a name=tab.gam></a>
</table>

Form results of the winning model we can see that:
- The _daily_ by _season_ interaction is significant to response variable in the three species
- The percentage of explained variance is more than 10% higher than with the non-imputed data

### Plot model

**Plot summed effects surfaces of season and daily smooths for the three species**
```
layout(matrix(1:6, nrow = 2))
vis.gam(m_imp_gam_sp2_daily, view=c("seasonally","daily"), cond=list(Species='pike'), main = "pike", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(-80,80), pos=.85)
vis.gam(m_imp_gam_sp2_daily, view=c("seasonally","daily"), cond=list(Species='pike'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_imp_gam_sp2_daily, view=c("seasonally","daily"), cond=list(Species='pikeperch'), main = "pikeperch", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(10,65), pos=.85)
vis.gam(m_imp_gam_sp2_daily, view=c("seasonally","daily"), cond=list(Species='pikeperch'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_imp_gam_sp2_daily, view=c("seasonally","daily"), cond=list(Species='wels'), main = "wels", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(-60,120), pos=.85)
vis.gam(m_imp_gam_sp2_daily, view=c("seasonally","daily"), cond=list(Species='wels'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
```
![Horiz_range](/Plots/Horiz_range_8_imputed.png "Horiz_range")

- We see that in the three species, the highest peak occurs in Spring I and when _Daily_ values are near 17630 (i.e., 2018-04-09 )

**Plot summed effects surfaces (smooth) with and without random effects**
```
par(mfrow=c(1,3), cex=1.1)
plot_smooth(m_imp_gam_sp2_daily, view="seasonally", cond=list(Species="pike"), rug=FALSE, ylim=c(-70,70), print.summary=FALSE, ylab = "Horizontal range [m]", main='Seasonality, Species=pike')
plot_smooth(m_imp_gam_sp2_daily, view="seasonally", cond=list(Species="pike"), rug=FALSE, add=TRUE, col='red', rm.ranef=TRUE, ylim=c(0,7700), print.summary=FALSE, xpd=TRUE)
legend('bottomleft', legend=c("with R.E.","without R.E."), col=c("black", "red"), lwd=2, bty='n')
plot_smooth(m_imp_gam_sp2_daily, view="seasonally", cond=list(Species="pikeperch"), rug=FALSE, ylim=c(-70,70), print.summary=FALSE, ylab = "Horizontal range [m]", main='Seasonality, Species=pikeperch')
plot_smooth(m_imp_gam_sp2_daily, view="seasonally", cond=list(Species="pikeperch"), rug=FALSE, add=TRUE, col='red', rm.ranef=TRUE, ylim=c(0,7700), print.summary=FALSE, xpd=TRUE)
legend('bottomleft', legend=c("with R.E.","without R.E."), col=c("black", "red"), lwd=2, bty='n')
plot_smooth(m_imp_gam_sp2_daily, view="seasonally", cond=list(Species="wels"), rug=FALSE, ylim=c(-70,70), print.summary=FALSE, ylab = "Horizontal range [m]", main='Seasonality, Species=wels')
plot_smooth(m_imp_gam_sp2_daily, view="seasonally", cond=list(Species="wels"), rug=FALSE, add=TRUE, col='red', rm.ranef=TRUE, ylim=c(0,7700), print.summary=FALSE, xpd=TRUE)
legend('bottomleft', legend=c("with R.E.","without R.E."), col=c("black", "red"), lwd=2, bty='n')
```
![Horiz_range](/Plots/Horiz_range_9_imputed.png "Horiz_range")

**Plot surface and one-dimensional daily/seasonal differences in horizontal range between the three species**
```
layout(matrix(1:3, nrow = 1))
plot_diff(m_imp_gam_sp2_daily, view="seasonally", comp=list(Species=c("pike", "pikeperch")), main='Seasonal difference pike-pikeperch')
plot_diff(m_imp_gam_sp2_daily, view="seasonally", comp=list(Species=c("pikeperch", "wels")), main='Seasonal difference pikeperch-wels')
plot_diff(m_imp_gam_sp2_daily, view="seasonally", comp=list(Species=c("pike", "wels")), main='Seasonal difference pike-wels')
```
![Horiz_range](/Plots/Horiz_range_10_imputed.png "Horiz_range")

Alternatively, plot fitted model values (daily/seasonally) with following code:
```
data_m_imp_gam_sp2_daily <- visreg(m_imp_gam_sp2_daily, "daily", by = "Species", breaks = c ("pike", "pikeperch", "wels"), gg = TRUE, overlay = TRUE, jitter = TRUE, lwd = 0.5, rug = FALSE, partial = FALSE, plot = FALSE)
plot(data_m_imp_gam_sp2_daily, plot.type="rgl") + theme_bw()
#data_m_imp_gam_sp2_season <- visreg(m_imp_gam_sp2_daily, "seasonally", by = "Species", breaks = c ("pike", "pikeperch", "wels"), gg = TRUE, overlay = TRUE, jitter = TRUE, lwd = 0.5, rug = FALSE, partial = FALSE, plot = FALSE)
#plot(data_m_imp_gam_sp2_season, plot.type="rgl") + theme_bw()
```
![Horiz_range](/Plots/Horiz_range_11_imputed.png "Horiz_range")

### 2.5. Fit GAMM models with correlated errors to data of daily and seasonal changes

Now lets see if adding autoregressive correlation terms improves the overall fit of previous models

#### 2.5.1. Autocorrelation model 1

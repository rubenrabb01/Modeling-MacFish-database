
## Create a dataframe including monthly range and reservoir parts

:books:`library(lubridate)`
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

Convert _month_ into a time vector of repeated-measures and code it as integer to allow correct ordering (from months 10-12 2018). In addition, we will include this time variable both as random-effects and fixed-effects and given its number of time points vector it should be coded as integer
```
data_distr$month<- revalue(data_distr$month, c("5_2017"="0","6_2017"="1","7_2017"="2","8_2017"="3","9_2017"="4","10_2017"="5","11_2017"="6","12_2017"="7","1_2018"="8","2_2018"="9","3_2018"="10"))
```
```
data_distr$fi_fishid <- as.factor(data_distr$fi_fishid)
data_distr$Species <- as.factor(data_distr$Species)
data_distr$month <- as.integer(data_distr$month)
```
Order by _fi_fishid_ and _month_ and drop missing variables
```
data_distr<- data_distr[with(data_distr, order(fi_fishid, month)),]
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
![Dist_range](/Plots/Dist_range_month_hist.png "Dist_range")

## Build GAMM models to data of monthly distance range

:books:`library(itsadug)`
:books:`library(mgcv)`

### 1. Model 1

#### 1.1. Fit a simple model including Species and one random term specifying for fish identity

m_gam_1 <- gam(dist.range ~ s(month, by=Species, sp=0.1) + s(fi_fishid, bs = "re"), data=data_distr)

#### 1.2. Set autocorrelation of data points

```
rho_1 <- start_value_rho(m_gam_1, plot = TRUE, lag = 2)
```

#### 1.3. Fit autocorrelation model

```
m_gam_1_autoc <- gam(dist.range ~ s(month, by=Species, sp=0.1) + s(fi_fishid, bs = "re"), family = gaussian, data = data_distr, rho = rho_1, AR.start = data_distr$start.event)
```

#### 1.4. Summary of model m_gam_1

```
summary(m_gam_1)
```
```
Family: gaussian
Link function: identity

Formula:
dist.range ~ s(month, by = Species, sp = 0.1) + s(fi_fishid,
    bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)   2037.8      164.9   12.35   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                             edf Ref.df     F  p-value
s(month):Speciespike       5.117  6.241 3.318  0.00315 **
s(month):Speciespikeperch  4.750  5.821 3.900  0.00108 **
s(month):Specieswels       5.678  6.847 5.997 4.05e-06 ***
s(fi_fishid)              22.709 30.000 3.368 4.11e-12 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.389   Deviance explained = 47.1%
GCV = 2.0472e+06  Scale est. = 1.7672e+06  n = 287
```

**See summary table**
```
gamtabs(m_gam_1, caption="Summary of m_gam_1", comment=FALSE, type='html')
```

<!-- html table generated in R 3.6.3 by xtable 1.8-4 package -->
<!-- Wed May 13 09:26:11 2020 -->
<table border=1>
<caption align="bottom"> Summary of m1 </caption>
  <tr> <td> A. parametric coefficients </td> <td align="right"> Estimate </td> <td align="right"> Std. Error </td> <td align="right"> t-value </td> <td align="right"> p-value </td> </tr>
  <tr> <td> (Intercept) </td> <td align="right"> 2037.7990 </td> <td align="right"> 164.9471 </td> <td align="right"> 12.3543 </td> <td align="right"> &lt; 0.0001 </td> </tr>
   <tr> <td> B. smooth terms </td> <td align="right"> edf </td> <td align="right"> Ref.df </td> <td align="right"> F-value </td> <td align="right"> p-value </td> </tr>
  <tr> <td> s(month):Speciespike </td> <td align="right"> 5.1173 </td> <td align="right"> 6.2406 </td> <td align="right"> 3.3179 </td> <td align="right"> 0.0031 </td> </tr>
  <tr> <td> s(month):Speciespikeperch </td> <td align="right"> 4.7500 </td> <td align="right"> 5.8212 </td> <td align="right"> 3.8995 </td> <td align="right"> 0.0011 </td> </tr>
  <tr> <td> s(month):Specieswels </td> <td align="right"> 5.6781 </td> <td align="right"> 6.8473 </td> <td align="right"> 5.9966 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> s(fi_fishid) </td> <td align="right"> 22.7090 </td> <td align="right"> 30.0000 </td> <td align="right"> 3.3681 </td> <td align="right"> &lt; 0.0001 </td> </tr>
   <a name=tab.gam></a>
</table>


\begin{table}[ht]
\centering
\begin{tabular}{lrrrr}
   \hline
A. parametric coefficients & Estimate & Std. Error & t-value & p-value \\
  (Intercept) & 2037.7990 & 164.9471 & 12.3543 & $<$ 0.0001 \\
   \hline
B. smooth terms & edf & Ref.df & F-value & p-value \\
  s(month):Speciespike & 5.1173 & 6.2406 & 3.3179 & 0.0031 \\
  s(month):Speciespikeperch & 4.7500 & 5.8212 & 3.8995 & 0.0011 \\
  s(month):Specieswels & 5.6781 & 6.8473 & 5.9966 & $<$ 0.0001 \\
  s(fi\_fishid) & 22.7090 & 30.0000 & 3.3681 & $<$ 0.0001 \\
   \hline
\end{tabular}
\caption{Summary of m_gam_1}
\label{tab.gam}
\end{table}


#### 1.5. Plot model

:books:`library(ggplot2)`
:books:`library(grid)`
:books:`library(visreg)`
:books:`library(splines)`
:books:`library(animation)`
:books:`library(gratia)`

**Plot partial effects**
```
par(mfrow=c(1, 4))
plot(m_gam_1, shade = TRUE)
```
![Dist_range_month](/Plots/Dist_range_month_1.png "Dist_range_month")

In these plots we see how the distance range changes with time differently in each species:
 - For _pike_ the peak of distance range is around the last month
 - For _pikeperch_ the distance range is maximum the first month but decreases the following two months
 - For _wels_ the lowest distance range is between months 5 and 8

**Plot model and mean distance range for each month**

```
data_m_gam_1_autoc <- visreg(m_gam_1_autoc, "month", by = "Species", breaks = c ("pike", "pikeperch", "wels"), gg = TRUE, overlay = TRUE, jitter = TRUE, lwd = 0.5, rug = FALSE, partial = FALSE, plot = FALSE)
```
```
ggplot () +
  scale_fill_manual (breaks = c ("pike", "pikeperch", "wels"), values = c("#E7B800", "#00AFBB", "#293352"), labels = c ("pike", "pikeperch", "wels")) +
  scale_color_manual (breaks = c ("pike", "pikeperch", "wels"), values = c("#E7B800", "#00AFBB", "#293352"), labels = c ("pike", "pikeperch", "wels")) +
  geom_ribbon (data = data_m_gam_1_autoc$fit, aes (x = month, ymin = visregLwr, ymax = visregUpr, fill = Species), alpha = 0.4, colour = "grey", size = 0.2) +
  geom_line (data = data_m_gam_1_autoc$fit, aes (x = month, y = visregFit, colour = Species), size = 1.1) +
  geom_point (data = data_distr, aes (x = month, y = dist.range , colour = Species), size = 1) +
  scale_y_continuous (limits = c (0, 8000), breaks = c (0, 2000, 5000, 7700), labels = c (0, 2000, 5000, 7700)) +
  labs (x = "\nmonth", y = expression (atop ("Distance range [m]")), colour = "Species", fill = "Species") +
  theme_bw () +
  theme (panel.grid.major.x = element_blank (),
         panel.grid.minor.x = element_blank (),
         panel.grid.major.y = element_blank (),
         panel.grid.minor.y = element_blank (),
         legend.position = c (0.15, 0.85),
         legend.direction = "vertical",
         legend.title = element_text (colour = "black", size = 16),
         legend.text = element_text (colour = "black", size = 14),
         axis.line = element_line (),
         axis.title = element_text (colour = "black", size = 16),
         axis.text = element_text (colour = "black", size = 14))
```
![Dist_range_month](/Plots/Dist_range_month_2.png "Dist_range_month")

**Plot summed effects surfaces (smooth) for each species**

```
layout(matrix(2:1, nrow = 2))
vis.gam(m_gam_1,ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "", main = "")
vis.gam(m_gam_1, main = "Distance range per month", plot.type = "contour", color = "terrain", contour.col = "black", lwd = 2)
```
![Dist_range_month](/Plots/Dist_range_month_3.png "Dist_range_month")

**Compared summed effects surfaces (smooth) with and without random effects**

```
par(mfrow=c(1,3), cex=1.1)

plot_smooth(m_gam_1, view="month", cond=list(Species="pike"), rug=FALSE, ylim=c(0,7700), print.summary=FALSE, ylab = "Distance range [m]", main='m_gam_1: month, Species=pike')
plot_smooth(m_gam_1, view="month", cond=list(Species="pike"), rug=FALSE, add=TRUE, col='red', rm.ranef=TRUE, ylim=c(0,7700), print.summary=FALSE, xpd=TRUE)
legend('bottomleft', legend=c("with R.E.","without R.E."), col=c("black", "red"), lwd=2, bty='n')

plot_smooth(m_gam_1, view="month", cond=list(Species="pikeperch"), rug=FALSE, ylim=c(0,7700), print.summary=FALSE, ylab = "Distance range [m]", main='m_gam_1: month, Species=pikeperch')
plot_smooth(m_gam_1, view="month", cond=list(Species="pikeperch"), rug=FALSE, add=TRUE, col='red', rm.ranef=TRUE, ylim=c(0,7700), print.summary=FALSE, xpd=TRUE)
legend('bottomleft', legend=c("with R.E.","without R.E."), col=c("black", "red"), lwd=2, bty='n')

plot_smooth(m_gam_1, view="month", cond=list(Species="wels"), rug=FALSE, ylim=c(0,7700), print.summary=FALSE, ylab = "Distance range [m]", main='m_gam_1: month, Species=wels')
plot_smooth(m_gam_1, view="month", cond=list(Species="wels"), rug=FALSE, add=TRUE, col='red', rm.ranef=TRUE, ylim=c(0,7700), print.summary=FALSE, xpd=TRUE)
legend('bottomleft', legend=c("with R.E.","without R.E."), col=c("black", "red"), lwd=2, bty='n')
```
![Dist_range_month](/Plots/Dist_range_month_4.png "Dist_range_month")







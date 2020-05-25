# Daily and weekly distance range from dam

In this part, daily and weekly seasonalities are descomposed as predictor variables to model the time series of response variable _distance from dam_
- There are 1089 measurements from tag beeps a day for each individual that we will serve to model daily seasonality
- Every week count 7 days to model weekly seasonality

## Create a dataset of daily and weekly distance range for each species

:books:`library(lubridate)`  
:books:`library(plyr)`  

Taking the main dataset _dist2dam.dt_, filter by individuals having more than 89 days of measures
```
data_distrdw<-dist2dam.dt[day.count > 89]
data_distrdw<-as.data.frame(data_distrdw)
```

Add info for each species and order by date
```
data_distrdw <- merge(dist2dam.dt, fish.info, by= c("fi_fishid"))
data_distrdw<-data_distrdw[with(data_distrdw, order(date)),]
```

Create a new numeric variable _week_num_ for day of the week based on _date_ (i.e., day)
```
data_distrdw$date <- as.Date(data_distrdw$date)
data_distrdw$week_day_num <- wday(data_distrdw$date)
```

Transform and rename variables including _week_day_ recoding with names of week days
```
data_distrdw[, week_day := as.factor(car::recode(week_day_num, "'1'='Monday';'2'='Tuesday';'3'='Wednesday';'4'='Thursday'; '5'='Friday';'6'='Saturday';'7'='Sunday'"))]
colnames(data_distrdw)[2] <- "time_min"
colnames(data_distrdw)[16] <- "Species"
data_distrdw$Species<-as.factor(data_distrdw$Species)
data_distrdw$fi_fishid<-as.factor(data_distrdw$fi_fishid)
data_distrdw
```
|          	| fi_fishid 	| time_min        	| dd_depth 	| up_easting 	| up_northing 	| distfromdam 	| is.pos 	| array_id 	| date       	| daily.no 	| prop.obser 	| day.count 	| ca_tl_mm 	| ca_weight_g 	| fi_sex 	| Species   	| week_day_num 	| week_day 	|
|----------	|-----------	|-----------------	|----------	|------------	|-------------	|-------------	|--------	|----------	|------------	|----------	|------------	|-----------	|----------	|-------------	|--------	|-----------	|--------------	|----------	|
| 1:00     	| T449202_1 	| 27/04/2017 3:41 	| 2.45     	| 461314.6   	| 5408392     	| 4500.569    	| TRUE   	| summer   	| 27/04/2017 	| 1089     	| 0.75625    	| 341       	| 430      	| 605         	| M      	| pikeperch 	| 5            	| Friday   	|
| 2:00     	| T449202_1 	| 27/04/2017 3:42 	| 2.45     	| 461313.2   	| 5408392     	| 4500.3314   	| TRUE   	| summer   	| 27/04/2017 	| 1089     	| 0.75625    	| 341       	| 430      	| 605         	| M      	| pikeperch 	| 5            	| Friday   	|
| 3:00     	| T449202_1 	| 27/04/2017 3:43 	| NA       	| 461317.3   	| 5408400     	| 4500.3314   	| TRUE   	| summer   	| 27/04/2017 	| 1089     	| 0.75625    	| 341       	| 430      	| 605         	| M      	| pikeperch 	| 5            	| Friday   	|
| 4:00     	| T449202_1 	| 27/04/2017 3:44 	| 2.45     	| 461321.2   	| 5408408     	| 4500.3314   	| TRUE   	| summer   	| 27/04/2017 	| 1089     	| 0.75625    	| 341       	| 430      	| 605         	| M      	| pikeperch 	| 5            	| Friday   	|
| 5:00     	| T449202_1 	| 27/04/2017 3:45 	| 2.683333 	| 461324.1   	| 5408418     	| 4500.3314   	| TRUE   	| summer   	| 27/04/2017 	| 1089     	| 0.75625    	| 341       	| 430      	| 605         	| M      	| pikeperch 	| 5            	| Friday   	|
| ---      	|           	|                 	|          	|            	|             	|             	|        	|          	|            	|          	|            	|           	|          	|             	|        	|           	|              	|          	|
| 9523630: 	| T449276_1 	| 11/04/2018 8:15 	| 13.65    	| 462069.2   	| 5410226     	| 1063.1949   	| FALSE  	| winter   	| 11/04/2018 	| 237      	| 0.1645833  	| 344       	| 1030     	| 6150        	| F      	| wels      	| 4            	| Thursday 	|
| 9523631: 	| T449276_1 	| 11/04/2018 3:14 	| 7.35     	| 462306.2   	| 5410725     	| 499.4637    	| FALSE  	| winter   	| 11/04/2018 	| 237      	| 0.1645833  	| 344       	| 1030     	| 6150        	| F      	| wels      	| 4            	| Thursday 	|
| 9523632: 	| T449276_1 	| 11/04/2018 3:01 	| 7.35     	| 461884.8   	| 5409746     	| 1703.599    	| FALSE  	| winter   	| 11/04/2018 	| 237      	| 0.1645833  	| 344       	| 1030     	| 6150        	| F      	| wels      	| 4            	| Thursday 	|
| 9523633: 	| T449276_1 	| 11/04/2018 3:13 	| 7.35     	| 461884.8   	| 5409746     	| 1703.599    	| FALSE  	| winter   	| 11/04/2018 	| 237      	| 0.1645833  	| 344       	| 1030     	| 6150        	| F      	| wels      	| 4            	| Thursday 	|
| 9523634: 	| T449276_1 	| 11/04/2018 3:14 	| 7.35     	| 461884.8   	| 5409746     	| 1703.599    	| FALSE  	| winter   	| 11/04/2018 	| 237      	| 0.1645833  	| 344       	| 1030     	| 6150        	| F      	| wels      	| 4            	| Thursday 	|

#### Subset data based on last month of observations and store information in new variables for further model building

```
pikeperch <- unique(data_distrdw[, Species])      # only pikeperch data
Species_sub <- (data_distrdw[, Species])          # pike, pikeperch and wels
date_sub <- unique(data_distrdw[, date])
day_week_sub <- unique(data_distrdw[, week_day])
id_sub <- (data_distrdw[, fi_fishid])
daily_num <- (data_distrdw[, daily.no])
```

Take data of distance from dam for the last month and store it in subset _data_distr_day_sub_
```
data_distrdw_sub <- data_distrdw[(fi_fishid == id_sub & Species == Species_sub & date %in% date_sub[290:320])]       # pike, pikeperch and wels
```

Calculate number of observations per day and the total number of days with measures using start and end dates
```
num_days <- data.frame(end_date=c("2018-04-11","2018-04-10"),start_day=c("2017-04-27","2017-04-27"))
num_days$diff <- as.Date(as.character(num_days$end_date), format="%Y-%m-%d")- as.Date(as.character(num_days$start_day), format="%Y-%m-%d")
num_days
```
```
   end_date   start_day   diff
1 2018-04-11 2017-04-27  349 days
2 2018-04-10 2017-04-27  348 days
```
- This gives the complete dates without accounting for missing days
- The actual total number of days is 341 and 31 for the subsetted dataset, which will be stored in object _time_day_sub_
- The number of observations per day is 1089 and will be stored in object _time_min_day_
- The number of observations in the subset is given by the number of rows corresponding to a period of 31-day measures
- The number of days in the subset is 31, which is the number of rows divided by the number of observations per day

```
time_day_sub <- with(data_distrdw_sub, ave(as.numeric(date), fi_fishid, FUN = function(x) length(unique(x))))
time_min_day <- 1089
time_min_day_full_sub <- nrow(data_distrdw_sub)
time_day_full_sub <- time_min_day_full_sub / time_min_day
```
**Note**:
- The number of observations/day is variable across individuals so this parameter applied to a model fitting is going to introduce some degree of deviance
- See code below to calculate exact number of days

## 2. Build GAM models fitted to data of daily and weekly distance range from dam

:books:`library(itsadug)`  
:books:`library(mgcv)`  

First, let further subset data to fit GAM models more easily
```
data_distrdw_sub_GAM <- data.table(Dist_dam = data_distrdw_sub[, distfromdam], daily = rep(1:time_min_day, 31), Species = data_distrdw_sub[, Species], Id = data_distrdw_sub[, fi_fishid], weekly = data_distrdw_sub[, week_day_num], body_size = data_distrdw_sub[, ca_tl_mm])
```

### 2.1. Model 1: Daily and Weekly distance from dam

We fit a GAM model wit predictor variables _daily_ and _weekly_ as smoothing functions (cubic splines and p-splines, respectively) and knots specifying unique case values for daily observartions and weekly number of days
```
m_gam_1 <- gam(Dist_dam ~ s(daily, bs = "cr", k = time_min_day) + s(weekly, bs = "ps", k = 7), data = data_distrdw_sub_GAM, family = gaussian)
```

Summary of model **m_gam_1**
```
summary(m_gam_1)
```
```
Family: gaussian
Link function: identity

Formula:
Dist_dam ~ s(daily, bs = "cr", k = 1089) + s(weekly, bs = "ps",
    k = 7)

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept) 3487.255      2.435    1432   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
             edf Ref.df      F p-value
s(daily)  10.829 13.536  22.07  <2e-16 ***
s(weekly)  5.944  5.998 321.98  <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.00316   Deviance explained = 0.318%
GCV = 4.1752e+06  Scale est. = 4.1751e+06  n = 704161
```
- We see that the R-squared value is too low and we should change the model fitting approach and include an interaction term

### 2.2. Model 2: Daily by Weekly distance from dam (interaction)

Fit a model that incorporates a smooth function to both _daily_ and _weekly_ with a tensor product interaction by using the _te_ function
```
m_gam_2 <- bam(Dist_dam ~ te(daily, weekly, bs = c("cr", "ps")), data = data_distrdw_sub_GAM, family = gaussian, na.action=na.omit)
```
```
summary(m_gam_2)
```
```
Family: gaussian
Link function: identity

Formula:
Dist_dam ~ te(daily, weekly, bs = c("cr", "ps"))

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept) 3486.799      2.436    1431   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                   edf Ref.df     F p-value
te(daily,weekly) 22.78  23.85 109.1  <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.00368   Deviance explained = 0.371%
fREML = 6.3663e+06  Scale est. = 4.1729e+06  n = 704161
```
- R-squared is a bit higher than previous model but still very low

### 2.3. Model 3: Daily by Weekly distance from dam (interaction), grouped by Species

Now, lets take model **m_gam_2** and include species as a grouping factor to interact with the smooths functions of the independent variables
```
m_gam_3 <- bam(Dist_dam ~ te(daily, weekly, bs = c("cr", "ps"), by=Species), data = data_distrdw_sub_GAM, family = gaussian, na.action=na.omit)
```
```
summary(m_gam_3)
```
```
Family: gaussian
Link function: identity

Formula:
Dist_dam ~ te(daily, weekly, bs = c("cr", "ps"), by = Species)

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept) 3486.244      2.433    1433   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                                    edf Ref.df      F p-value
te(daily,weekly):Speciespike      20.58  22.84 120.03  <2e-16 ***
te(daily,weekly):Speciespikeperch 23.31  23.95 116.28  <2e-16 ***
te(daily,weekly):Specieswels      22.81  23.86  35.54  <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.00896   Deviance explained = 0.905%
fREML = 6.3645e+06  Scale est. = 4.1508e+06  n = 704161
```
- We see that the inclusion of the grouping factor improves the model fit but still R-squared is very low

## 3. Build GAMM models fitted to data of daily and weekly distance range from dam

Lets try fitting a mixed-effects model (GAMM) by including a simple random-effects term (i.e., Id) to previous models

### 3.1. Model 4 (RE): Daily and Weekly distance from dam (interaction), grouped by Species

Add a random-effects term and grouping factor _Species_ to the smooth functions of model **m_gam_1**
```
m_gam_4_re <- bam(Dist_dam ~ s(daily, by = Species, sp = 0.1) + s(weekly, by = Species, bs = "ps", k = 7) + s(Id, bs = "re"), data = data_distrdw_sub_GAM, family = gaussian, na.action=na.omit)
```
```
summary(m_gam_4_re)
```
```
Family: gaussian
Link function: identity

Formula:
Dist_dam ~ s(daily, by = Species, sp = 0.1) + s(weekly, by = Species,
    bs = "ps", k = 7) + s(Id, bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)   4122.2      442.5   9.317   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                              edf Ref.df         F  p-value
s(daily):Speciespike        8.968  9.000 2.534e+01  < 2e-16 ***
s(daily):Speciespikeperch   8.949  8.999 3.075e+00  0.00117 **
s(daily):Specieswels        8.985  9.000 5.572e+00 9.58e-08 ***
s(weekly):Speciespike       5.958  5.999 3.364e+03  < 2e-16 ***
s(weekly):Speciespikeperch  5.968  5.999 4.511e+02  < 2e-16 ***
s(weekly):Specieswels       5.959  5.999 3.193e+02  < 2e-16 ***
s(Id)                      19.000 19.000 1.730e+05  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.825   Deviance explained = 82.5%
fREML = 5.7545e+06  Scale est. = 7.3367e+05  n = 704161
```
- We see that the R-squared value has increased significantly suggesting that the model is now a proper fit corrected for individual repeated measures
- However, R-squared is not especially high and we should try other models

### 3.2. Model 5 (RE): Daily and Weekly distance from dam (interaction), grouped by Species with tensor product

Now, we include a tensor product to the smooth functions of previous model **m_gam_4_re**
```
m_gam_5_re<-bam(Dist_dam ~ te(daily, by = Species, sp = 0.1) + te(weekly, by = Species, bs = "ps", k = 7) + s(Id, bs = "re"), data = data_distrdw_sub_GAM, family = gaussian, na.action=na.omit)
```
```
summary(m_gam_5_re)
```
```
Family: gaussian
Link function: identity

Formula:
Dist_dam ~ te(daily, by = Species, sp = 0.1) + te(weekly, by = Species,
    bs = "ps", k = 7) + s(Id, bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)   4122.3      441.1   9.346   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                               edf Ref.df         F  p-value
te(daily):Speciespike        4.000  4.000 2.948e+01  < 2e-16 ***
te(daily):Speciespikeperch   4.000  4.000 3.275e+00   0.0108 *
te(daily):Specieswels        4.000  4.000 6.191e+00 5.61e-05 ***
te(weekly):Speciespike       5.958  5.999 3.362e+03  < 2e-16 ***
te(weekly):Speciespikeperch  5.968  5.999 4.505e+02  < 2e-16 ***
te(weekly):Specieswels       5.959  5.999 3.192e+02  < 2e-16 ***
s(Id)                       19.000 19.000 1.730e+05  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.825   Deviance explained = 82.5%
fREML = 5.7546e+06  Scale est. = 7.3382e+05  n = 704161
```

### 3.3. Model 6 (RE): Daily by Weekly distance from dam (interaction), grouped by Species

Include a random-effects term to interaction model **m_gam_3
```
m_gam_6_re <- bam(Dist_dam ~ te(daily, weekly, bs = c("cr", "ps"), by=Species)+ s(Id, bs = "re"), data = data_distrdw_sub_GAM, family = gaussian, na.action=na.omit)
```
```
summary(m_gam_6_re)
```
```
Family: gaussian
Link function: identity

Formula:
Dist_dam ~ te(daily, weekly, bs = c("cr", "ps"), by = Species) +
    s(Id, bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)   4124.5      442.4   9.323   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                                    edf Ref.df         F p-value
te(daily,weekly):Speciespike      23.42  23.96    885.93  <2e-16 ***
te(daily,weekly):Speciespikeperch 22.83  23.84    119.08  <2e-16 ***
te(daily,weekly):Specieswels      22.47  23.78     75.23  <2e-16 ***
s(Id)                             19.00  19.00 172819.75  <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.825   Deviance explained = 82.5%
fREML = 5.7542e+06  Scale est. = 7.3295e+05  n = 704161
```
- We can see that the three last models are similar in terms of R-squared values
- Lets include _body_size_ as a covariate to let it improve model fit

### 3.4. Model 7 (RE): Daily and Weekly distance from dam (interaction), grouped by Species with body size as a covariate

Now re-fit model **m_gam_4_re** including _body_size_ as a smooth term covariate
```
m_gam_7_re_bs <- bam(Dist_dam ~ s(body_size) + s(daily, by = Species, sp = 0.1) + s(weekly, by = Species, bs = "ps", k = 7) + s(Id, bs = "re"), data = data_distrdw_sub_GAM, family = gaussian, na.action=na.omit)
```
```
summary(m_gam_7_re_bs)
```
```
Family: gaussian
Link function: identity

Formula:
Dist_dam ~ s(body_size) + s(daily, by = Species, sp = 0.1) +
    s(weekly, by = Species, bs = "ps", k = 7) + s(Id, bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)   4099.6      457.1   8.969   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                              edf Ref.df         F  p-value
s(body_size)                1.000  1.000 1.330e-01  0.71496
s(daily):Speciespike        8.968  9.000 2.534e+01  < 2e-16 ***
s(daily):Speciespikeperch   8.949  8.999 3.075e+00  0.00117 **
s(daily):Specieswels        8.985  9.000 5.572e+00 9.58e-08 ***
s(weekly):Speciespike       5.958  5.999 3.364e+03  < 2e-16 ***
s(weekly):Speciespikeperch  5.968  5.999 4.511e+02  < 2e-16 ***
s(weekly):Specieswels       5.959  5.999 3.193e+02  < 2e-16 ***
s(Id)                      18.000 18.000 1.770e+05  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.825   Deviance explained = 82.5%
fREML = 5.7545e+06  Scale est. = 7.3367e+05  n = 704161
```
- We see that _body_size_ does not have any effect on

### 3.5. Model 8 (RE): Daily by Weekly distance from dam (interaction), grouped by Species with body size as a covariate

Now re-fit model **m_gam_6_re** including _body_size_ as a smooth term covariate
```
m_gam_8_re_bs <- bam(Dist_dam ~ s(body_size) + te(daily, weekly, bs = c("cr", "ps"), by=Species, fx = TRUE)+ s(Id, bs = "re"), data = data_distrdw_sub_GAM, family = gaussian, na.action=na.omit)
```
```
summary(m_gam_8_re_bs)
```
```
Family: gaussian
Link function: identity

Formula:
Dist_dam ~ s(body_size) + te(daily, weekly, bs = c("cr", "ps"),
    by = Species) + s(Id, bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)   4101.7      457.3    8.97   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                                    edf Ref.df         F p-value
s(body_size)                       1.00   1.00 1.350e-01   0.713
te(daily,weekly):Speciespike      23.42  23.96 8.859e+02  <2e-16 ***
te(daily,weekly):Speciespikeperch 22.83  23.84 1.191e+02  <2e-16 ***
te(daily,weekly):Specieswels      22.47  23.78 7.523e+01  <2e-16 ***
s(Id)                             18.00  18.00 1.763e+05  <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.825   Deviance explained = 82.5%
fREML = 5.7542e+06  Scale est. = 7.3295e+05  n = 704161
```

### 3.6. Model 9 (RE): Daily by Weekly distance from dam (interaction), grouped by Species with body size as a covariate

Now we use a different function, _ti_, to include a tensor product interaction term in presence of smooth functions for main-effects grouped by Species
```
m_gam_9_re_bs_ti <- bam(Dist_dam ~  s(body_size) + s(daily, bs = "cr", k = period, by=Species) + s(weekly, bs = "ps", k = 7, by=Species) + ti(daily, weekly, bs = c("cr", "ps"), by= Species)+ s(Id, bs = "re"), data = data_distrdw_sub_GAM, family = gaussian, na.action=na.omit)
```
```
summary(m_gam_9_re_bs_ti)
```
```
Family: gaussian
Link function: identity

Formula:
Dist_dam ~ s(body_size) + s(daily, bs = "cr", k = period, by = Species) +
    s(weekly, bs = "ps", k = 7, by = Species) + ti(daily, weekly,
    bs = c("cr", "ps"), by = Species) + s(Id, bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)     4100        456   8.992   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                                     edf Ref.df         F  p-value
s(body_size)                       1.000  1.000 1.340e-01   0.7138
s(daily):Speciespike              17.178 21.281 1.286e+01  < 2e-16 ***
s(daily):Speciespikeperch          2.441  3.051 2.409e+00   0.0641 .
s(daily):Specieswels               8.204 10.244 3.737e+00 4.07e-05 ***
s(weekly):Speciespike              5.958  5.999 3.390e+03  < 2e-16 ***
s(weekly):Speciespikeperch         5.970  5.999 4.613e+02  < 2e-16 ***
s(weekly):Specieswels              5.959  5.999 3.202e+02  < 2e-16 ***
ti(daily,weekly):Speciespike      15.429 15.957 7.938e+01  < 2e-16 ***
ti(daily,weekly):Speciespikeperch 15.194 15.918 2.743e+01  < 2e-16 ***
ti(daily,weekly):Specieswels      14.117 15.462 1.495e+01  < 2e-16 ***
s(Id)                             18.000 18.000 1.781e+05  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.825   Deviance explained = 82.5%
fREML = 5.7536e+06  Scale est. = 7.3165e+05  n = 704161
```
After running all these models we have clear that:
-	The only valid models are mixed-effects models (GAMM) including (at least) one random-effects term. We have fitted models with subject identity but probably we should try more complex models adding a slope or a second intercept. Yet, the meaning of doing that in this time series analysis is not too clear.
-	All GAMM models fitted produce very similar results; however, we need to conduct further model-selection to choose one as there may be differences in their GCV (i.e, Generalized Cross Validation scores) indicating different optimal smoothing parameters and estimated D.F. (i.e., the number of basis dimensions given by EDF indicates how much a variable is smoothed, higher d.f. more complex splines).

## 4. Model selection

:books:`library(MASS)`  
:books:`library(AICcmodavg)`  

Create a list of candidate GAMM models
```
Cand.mod <- list(m_gam_4_re, m_gam_5_re, m_gam_6_re, m_gam_7_re_bs, m_gam_8_re_bs, m_gam_9_re_bs_ti)
```

Apply the _glm.convert_ function from package _MASS_ to order models based on AICc (we need to convert to a glm object in order to use the _aictab_ function)
```
Cand.mod.glm <- lapply(Cand.mod, glm.convert)
aictab(cand.set = Cand.mod.glm, second.ord = TRUE)
```
```
Model selection based on AICc:

       K     AICc Delta_AICc AICcWt Cum.Wt       LL
Mod6 238 11506763       0.00      1      1 -5753143
Mod3  94 11507994    1230.75      0      1 -5753903
Mod5 103 11507994    1230.75      0      1 -5753894
Mod1  67 11508675    1912.29      0      1 -5754271
Mod4  76 11508675    1912.29      0      1 -5754262
Mod2  52 11508798    2034.43      0      1 -5754347
```
These results suggest that **m_gam_9_re_bs_ti** is the best model. We can further check their GCV and EDF estimates by:

summary(m_gam_9_re_bs_ti)$sp.criterion
summary(m_gam_9_re_bs_ti)$s.table

**Summary table** of the winning model
```
gamtabs(m_gam_9_re_bs_ti, caption="Summary of m_gam_9_re_bs_ti", comment=FALSE, type='html')
```
<table border=1>
<caption align="bottom"> Summary of m_gam_9_re_bs_ti </caption>
  <tr> <td> A. parametric coefficients </td> <td align="right"> Estimate </td> <td align="right"> Std. Error </td> <td align="right"> t-value </td> <td align="right"> p-value </td> </tr>
  <tr> <td> (Intercept) </td> <td align="right"> 4100.4257 </td> <td align="right"> 455.9878 </td> <td align="right"> 8.9924 </td> <td align="right"> &lt; 0.0001 </td> </tr>
   <tr> <td> B. smooth terms </td> <td align="right"> edf </td> <td align="right"> Ref.df </td> <td align="right"> F-value </td> <td align="right"> p-value </td> </tr>
  <tr> <td> s(body_size) </td> <td align="right"> 1.0000 </td> <td align="right"> 1.0000 </td> <td align="right"> 0.1345 </td> <td align="right"> 0.7138 </td> </tr>
  <tr> <td> s(daily):Speciespike </td> <td align="right"> 17.1778 </td> <td align="right"> 21.2812 </td> <td align="right"> 12.8626 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> s(daily):Speciespikeperch </td> <td align="right"> 2.4413 </td> <td align="right"> 3.0510 </td> <td align="right"> 2.4091 </td> <td align="right"> 0.0641 </td> </tr>
  <tr> <td> s(daily):Specieswels </td> <td align="right"> 8.2042 </td> <td align="right"> 10.2441 </td> <td align="right"> 3.7374 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> s(weekly):Speciespike </td> <td align="right"> 5.9579 </td> <td align="right"> 5.9990 </td> <td align="right"> 3389.9584 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> s(weekly):Speciespikeperch </td> <td align="right"> 5.9700 </td> <td align="right"> 5.9995 </td> <td align="right"> 461.2675 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> s(weekly):Specieswels </td> <td align="right"> 5.9588 </td> <td align="right"> 5.9990 </td> <td align="right"> 320.1807 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> ti(daily,weekly):Speciespike </td> <td align="right"> 15.4293 </td> <td align="right"> 15.9574 </td> <td align="right"> 79.3772 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> ti(daily,weekly):Speciespikeperch </td> <td align="right"> 15.1938 </td> <td align="right"> 15.9177 </td> <td align="right"> 27.4321 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> ti(daily,weekly):Specieswels </td> <td align="right"> 14.1170 </td> <td align="right"> 15.4623 </td> <td align="right"> 14.9458 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> s(Id) </td> <td align="right"> 17.9996 </td> <td align="right"> 18.0000 </td> <td align="right"> 178085.1060 </td> <td align="right"> &lt; 0.0001 </td> </tr>
   <a name=tab.gam></a>
</table>

Form results of the winning model we can see that:
- The _weekly_ predictor is significant to response variable _Dist_dam_ in the three species
- The _daily_ predictor is significant to response variable _Dist_dam_ in _pike_ and _wels_ but not _pikeperch_
- As expected, _daily_ have more complex splines and smoothness than _weekly_(i.e., higher EDF)

## 5. Plot model

:books:`library(ggplot2)`  
:books:`library(grid)`  
:books:`library(visreg)`  
:books:`library(splines)`  
:books:`library(animation)`  
:books:`library(gratia)`  

**Plot partial effects and estimates of the three dimensional smooths (i.e., the grouping predictor "Species" )**
```
par(mfrow=c(2, 2))
plot(m_gam_9_re_bs_ti, shade = TRUE)
```
Alternatively plot with:
```
draw(m_gam_9_re_bs_ti, ncol = 2)  # library "gratia"
visreg(m_gam_9_re_bs_ti, "daily", "weekly", ylab="Distance range [m]")
visreg2d(m_gam_9_re_bs_ti, x="daily", y="weekly", plot.type="persp")
visreg2d(m_gam_9_re_bs_ti, x="daily", y="weekly", plot.type="image")
```

Extract coefficients for random intercepts and plot for each individual
```
re <- get_random(m_gam_9_re_bs_ti)
```
```
par(mfrow=c(1,1))
barplot(re[[1]])
abline(h=5)
```
![Dist_f_dam](/Plots/Dist_f_dam_1.png "Dist_f_dam")

**Plot random effects estimates of daily and weekly distance from dam
```
par(mfrow=c(1,3))
plot(m_gam_9_re_bs_ti, select=c(2), ylim=c(-20,20))
title(main="Random smooths")
abline(h=0)
plot(m_gam_9_re_bs_ti, select=c(3), ylim=c(-20,20))
title(main="Random smooths")
abline(h=0)
plot(m_gam_9_re_bs_ti, select=c(4), ylim=c(-20,20))
title(main="Random smooths")
abline(h=0)
```
![Dist_f_dam](/Plots/Dist_f_dam_2.png "Dist_f_dam")

```
par(mfrow=c(1,3))
plot(m_gam_9_re_bs_ti, select=c(5), ylim=c(0,500))
title(main="Random smooths")
abline(h=0)
plot(m_gam_9_re_bs_ti, select=c(6), ylim=c(0,500))
title(main="Random smooths")
abline(h=0)
plot(m_gam_9_re_bs_ti, select=c(7), ylim=c(0,500))
title(main="Random smooths")
abline(h=0)
```
![Dist_f_dam](/Plots/Dist_f_dam_3.png "Dist_f_dam")

**Plot surface and one-dimensional differences in daily and weekly distance form dam between the three species**
```
par(mfrow=c(1,3))
plot_diff2(m_gam_9_re_bs_ti, view=c("daily","weekly"),
        comp=list(Species=c("pike", "pikeperch")),
        zlim=c(-5,7.5),
        main='Difference pike-pikeperch',
        print.summary=FALSE)
plot_diff2(m_gam_9_re_bs_ti, view=c("daily","weekly"),
        comp=list(Species=c("pikeperch", "wels")),
        zlim=c(-5,7.5),
        main='Difference pikeperch-wels',
        print.summary=FALSE)
plot_diff2(m_gam_9_re_bs_ti, view=c("daily","weekly"),
        comp=list(Species=c("pike", "wels")),
        zlim=c(-5,7.5),
        main='Difference pike-wels',
        print.summary=FALSE)
```
![Dist_f_dam](/Plots/Dist_f_dam_4.png "Dist_f_dam")

```
par(mfrow=c(1,3))
plot_diff(m_gam_9_re_bs_ti, view="daily",
        comp=list(Species=c("pike", "pikeperch")),
        main='Daily difference in distance from dam between pike and pikeperch')
plot_diff(m_gam_9_re_bs_ti, view="daily",
        comp=list(Species=c("pikeperch", "wels")),
        main='Daily difference in distance from dam between pikeperch and wels')
plot_diff(m_gam_9_re_bs_ti, view="daily",
        comp=list(Species=c("pike", "wels")),
        main='Daily difference in distance from dam between pike and wels')
```
![Dist_f_dam](/Plots/Dist_f_dam_5.png "Dist_f_dam")

```
par(mfrow=c(1,3))
plot_diff(m_gam_9_re_bs_ti, view="weekly",
        comp=list(Species=c("pike", "pikeperch")),
        main='Daily difference in distance from dam between pike and pikeperch')
plot_diff(m_gam_9_re_bs_ti, view="weekly",
        comp=list(Species=c("pikeperch", "wels")),
        main='Daily difference in distance from dam between pikeperch and wels')
plot_diff(m_gam_9_re_bs_ti, view="weekly",
        comp=list(Species=c("pike", "wels")),
        main='Daily difference in distance from dam between pike and wels')
```
![Dist_f_dam](/Plots/Dist_f_dam_6.png "Dist_f_dam")

# Complete time series analysis

## 1. Prepare a complete dataset with imputation of missing cases

:books:`library(tidyverse)`  
:books:`library(fable)`  
:books:`library(zoo)`  
:books:`library(forecast)`  

Now we consider the full dataset which needs to be transformed prior to analysis
```
data_distrdw <- merge(dist2dam.dt, fish.info, by= c("fi_fishid"))
data_distrdw <- data_distrdw[with(data_distrdw, order(date)),]
```

Filter by individuals having more than 927 daily measures and more than 100 days of observations
```
data_distrdw<- data_distrdw[data_distrdw$daily.no>=927,]
data_distrdw<- data_distrdw[data_distrdw$day.count>=100,]
```

Create a new numeric variable _week_num_ for day of the week based on _date_ (i.e., day)
```
data_distrdw$date <- as.Date(data_distrdw$date)
data_distrdw$week_day_num <- wday(data_distrdw$date)
```

Include _season_ and create a new variabe _season_num_
```
data_distrdw <- as.data.table(data_distrdw)
data_distrdw[date > "2017-04-25" & date <= "2017-06-20", season := "spring_I"]
data_distrdw[date > "2017-06-20" & date <= "2017-09-20", season := "summer"]
data_distrdw[date > "2017-09-20" & date <= "2017-12-20", season := "autumn"]
data_distrdw[date > "2017-12-20" & date <= "2018-03-20", season := "winter"]
data_distrdw[date > "2018-03-20" & date <= "2018-08-20", season := "spring_II"]
data_distrdw$season<-as.factor(data_distrdw$season)
data_distrdw[, season_num := as.factor(car::recode(season, "'spring_I'='1';'summer'='2';'autumn'='3';'winter'='4'; 'spring_II'='5'"))]
data_distrdw$season_num<-as.numeric(data_distrdw$season_num)
```

Transform and rename variables including _week_day_ recoding with names of week days
```
data_distrdw[, week_day := as.factor(car::recode(week_day_num, "'1'='Monday';'2'='Tuesday';'3'='Wednesday';'4'='Thursday'; '5'='Friday';'6'='Saturday';'7'='Sunday'"))]
colnames(data_distrdw)[2] <- "time_min"
colnames(data_distrdw)[16] <- "Species"
data_distrdw$Species<-as.factor(data_distrdw$Species)
data_distrdw$fi_fishid<-as.factor(data_distrdw$fi_fishid)
```

Filter the data removing first and last (half) day of measures
```
data_distrdw<-data_distrdw[time_min > "2017-04-28 00:00:00" & time_min <= "2018-04-09 23:59:00"]
```

Complete the time series inserting rows for missing _time_min_ values and, for each individual, replace missing values using LOCF (Last Observation Carried Forward)
```
data_distrdw<- data_distrdw %>% complete(fi_fishid, time_min) %>% fill(everything())
data_distrdw<-as.data.frame(data_distrdw)
```
## 2. Subset data based on last month of observations and store information in new variables for further model building

Before re-fiting some of the best models of previous subset to the complete dataset, lets make some basic caculations

A day has 1440 minutes which is the number of observations per day and individual
The total number of observations in the complete dataset is given by:
```
nrow(data_distrdw)
```

Since there are 31 individuals in the dataset the exact number of days is:
```
nrow(data_distrdw)/(1440*31)
```
```
[1] 342.1072
```

Another way would be counting number of days between start and end date
```
difftime(strptime("2018-04-09", format = "%Y-%m-%d"),
strptime("2017-04-28", format = "%Y-%m-%d"),units="days")
```
```
Time difference of 346 days
```
**Note** that this does not take into account some missing/overlapping dates

Subset complete data to ease the model fitting
```
data_distrdw_GAM <- data.table(Dist_dam = data_distrdw[, distfromdam], daily = rep(1:1440, 342), Species = data_distrdw[, Species], Id = data_distrdw[, fi_fishid], weekly = data_distrdw[, week_day_num], body_size = data_distrdw[, ca_tl_mm])
```

## 3. Build GAMM models fitted to data of daily and weekly distance range from dam

### 3.1. Model 6 (RE): Daily by Weekly distance from dam (interaction), grouped by Species

```
m_gam_6_re_total <- bam(Dist_dam ~ te(daily, weekly, bs = c("cr", "ps"), by=Species)+ s(Id, bs = "re"), data = data_distrdw_GAM, family = gaussian, na.action=na.omit)
```
```
summary(m_gam_6_re_total)
```
```
Family: gaussian
Link function: identity

Formula:
Dist_dam ~ te(daily, weekly, bs = c("cr", "ps"), by = Species) +
    s(Id, bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)   3811.5      302.6    12.6   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                                    edf Ref.df      F p-value
te(daily,weekly):Speciespike      21.20  23.10   1206  <2e-16 ***
te(daily,weekly):Speciespikeperch 20.54  22.72   3249  <2e-16 ***
te(daily,weekly):Specieswels      23.09  23.89   2001  <2e-16 ***
s(Id)                             30.00  30.00 816029  <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.628   Deviance explained = 62.8%
fREML = 1.3088e+08  Scale est. = 1.6269e+06  n = 15271667
```
We see that the R-squared value has significantly decreased compared to the last month data

### 3.2. Model 9 (RE): Daily by Weekly distance from dam (interaction), grouped by Species with body size as a covariate

Now we re-fit the previous winning model changing the chunk parameter to the total number of observations/day
```
m_gam_9_re_bs_ti_total <- bam(Dist_dam ~  s(body_size) + s(daily, bs = "cr", k = 1440, by=Species) + s(weekly, bs = "ps", k = 7, by=Species) + ti(daily, weekly, bs = c("cr", "ps"), by= Species)+ s(Id, bs = "re"), data = data_distrdw_GAM, family = gaussian, na.action=na.omit)
```
```
summary(m_gam_9_re_bs_ti_total)
```
```
Family: gaussian
Link function: identity

Formula:
Dist_dam ~ s(body_size) + s(daily, bs = "cr", k = 1440, by = Species) +
    s(weekly, bs = "ps", k = 7, by = Species) + ti(daily, weekly,
    bs = c("cr", "ps"), by = Species) + s(Id, bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)   3705.1      548.1    6.76 1.38e-11 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                                     edf Ref.df         F  p-value
s(body_size)                       8.717  8.722 5.885e+04  < 2e-16 ***
s(daily):Speciespike               1.277  1.503 7.920e-01 0.551507
s(daily):Speciespikeperch          4.814  6.018 4.588e+00 0.000119 ***
s(daily):Specieswels               1.998  2.498 2.041e+00 0.096127 .
s(weekly):Speciespike              5.999  6.000 5.156e+03  < 2e-16 ***
s(weekly):Speciespikeperch         6.000  6.000 3.811e+04  < 2e-16 ***
s(weekly):Specieswels              6.000  6.000 4.806e+04  < 2e-16 ***
ti(daily,weekly):Speciespike      14.360 15.652 2.415e+01  < 2e-16 ***
ti(daily,weekly):Speciespikeperch 12.046 14.333 5.087e+00 1.99e-09 ***
ti(daily,weekly):Specieswels      15.274 15.930 3.369e+01  < 2e-16 ***
s(Id)                             27.213 30.000 3.971e+05  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

R-sq.(adj) =  0.649   Deviance explained = 64.9%
fREML = 1.3043e+08  Scale est. = 1.5349e+06  n = 15271667
```

Extract root variance components
```
gam.vcomp(m_gam_9_re_bs_ti_total)
```
```
Standard deviations and 0.95 confidence intervals:

                                        std.dev        lower        upper
s(body_size)                       9.791565e+00 6.030228e+00 1.589902e+01
s(daily):Speciespike               1.268528e-04 1.267810e-04 1.269246e-04
s(daily):Speciespikeperch          2.352179e-03 9.777672e-04 5.658550e-03
s(daily):Specieswels               2.657937e-04 6.570815e-06 1.075152e-02
s(weekly):Speciespike              2.746067e+03 1.476850e+03 5.106060e+03
s(weekly):Speciespikeperch         1.059774e+04 5.702031e+03 1.969684e+04
s(weekly):Specieswels              1.083072e+04 5.827423e+03 2.012976e+04
ti(daily,weekly):Speciespike1      9.210012e+00 4.957012e+00 1.711199e+01
ti(daily,weekly):Speciespike2      1.506590e+00 8.215237e-01 2.762932e+00
ti(daily,weekly):Speciespikeperch1 4.116024e+00 1.052920e+00 1.609017e+01
ti(daily,weekly):Speciespikeperch2 1.299507e+00 5.221944e-01 3.233889e+00
ti(daily,weekly):Specieswels1      1.057097e+01 5.847578e+00 1.910971e+01
ti(daily,weekly):Specieswels2      1.949875e+00 1.122361e+00 3.387513e+00
s(Id)                              3.049559e+03 2.332171e+03 3.987618e+03
scale                              1.238912e+03 1.238473e+03 1.239352e+03

Rank: 14/15
```
The results suggest that:
- Daily differences in distance from dam are not significant in _pike_ and _wels_ but they are in _pikeperch_
- Weekly differences in distance from dam are significant in the three species
- This model improves the R-squared value a bit but it is still low
- We should try an autoregressive model by including the autocorrelation structure

## 4. Plot model

**Plot partial effects and estimates of the three dimensional smooths (i.e., the grouping predictor "Species")**
```
plot_parametric(m_gam_9_re_bs_ti_total, pred=list(Species=c("pike", "pikeperch", "wels")), cond=list(weekly=0, Dist_dam=0), rm.ranef=TRUE, print.summary=FALSE)
```
![Dist_range_month](/Plots/Dist_range_month_71.png "Dist_range_month")

**Plot partial effects for body size by daily distance from dam**
```
layout(matrix(1:3, nrow = 1))
pvisgam(m_gam_9_re_bs_ti_total, rug=FALSE, select=8, color = "cm",main='Species=pike', cex.axis=1.5, cex.lab=1.5)
pvisgam(m_gam_9_re_bs_ti_total, select=9, rug=FALSE, color = "cm", main='Species=pikperch', cex.axis=1.5, cex.lab=1.5)
pvisgam(m_gam_9_re_bs_ti_total, select=10, rug=FALSE,color = "cm",  main='Species=wels', cex.axis=1.5, cex.lab=1.5)
```
![Dist_range_month](/Plots/Dist_range_month_72.png "Dist_range_month")

**Plot summed effects surfaces (smooth) for each species**
```
layout(matrix(1:6, nrow = 2))
vis.gam(m_gam_9_re_bs_ti_total, view=c("daily","weekly"), cond=list(Species='pike'), main = "pike", plot.type = "contour", color = "terrain", contour.col = "black", lwd = 2)
pvisgam(m_gam_9_re_bs_ti_total, view=c("daily","weekly"), select=8, cond=list(Species='pike'), main = "pike", plot.type = "contour", color = "terrain", contour.col = "black", lwd = 2)
vis.gam(m_gam_9_re_bs_ti_total, view=c("daily","weekly"), cond=list(Species='pikeperch'), main = "pikeperch", plot.type = "contour", color = "terrain", contour.col = "black", lwd = 2)
pvisgam(m_gam_9_re_bs_ti_total, view=c("daily","weekly"), select=9, cond=list(Species='pikeperch'), main = "pikeperch", plot.type = "contour", color = "terrain", contour.col = "black", lwd = 2)
vis.gam(m_gam_9_re_bs_ti_total, view=c("daily","weekly"), cond=list(Species='wels'), main = "wels", plot.type = "contour", color = "terrain", contour.col = "black", lwd = 2)
pvisgam(m_gam_9_re_bs_ti_total, view=c("daily","weekly"), select=10, cond=list(Species='wels'), main = "wels", plot.type = "contour", color = "terrain", contour.col = "black", lwd = 2)
```
![Dist_range_month](/Plots/Dist_f_dam_73.png "Dist_range_month")

```
layout(matrix(1:6, nrow = 2))
pvisgam(m_gam_9_re_bs_ti_total, view=c("daily","weekly"), select=8, cond=list(Species='pike'), main = "pike", plot.type = "persp", color = "terrain", contour.col = "black", lwd = 2)
vis.gam(m_gam_9_re_bs_ti_total, view=c("daily","weekly"), cond=list(Species='pike'), main = "pike", n.grid = 50, theta = 35, phi = 32, zlab = "", ticktype = "detailed", color = "topo")
pvisgam(m_gam_9_re_bs_ti_total, view=c("daily","weekly"), select=9, cond=list(Species='pikeperch'), main = "pikeperch", plot.type = "persp", color = "terrain", contour.col = "black", lwd = 2)
vis.gam(m_gam_9_re_bs_ti_total, view=c("daily","weekly"), cond=list(Species='pikeperch'), main = "pikeperch", n.grid = 50, theta = 35, phi = 32, zlab = "", ticktype = "detailed", color = "topo")
pvisgam(m_gam_9_re_bs_ti_total, view=c("daily","weekly"), select=10, cond=list(Species='wels'), main = "wels", plot.type = "persp", color = "terrain", contour.col = "black", lwd = 2)
vis.gam(m_gam_9_re_bs_ti_total, view=c("daily","weekly"), cond=list(Species='wels'), main = "wels", n.grid = 50, theta = 35, phi = 32, zlab = "", ticktype = "detailed", color = "topo")
```
![Dist_range_month](/Plots/Dist_f_dam_74.png "Dist_range_month")

**Plot random effects estimates of daily and weekly distance from dam**
```
par(mfrow=c(1,3))
plot(m_gam_9_re_bs_ti_total, select=c(2), ylim=c(-20,20))
title(main="Random smooths")
abline(h=0)
plot(m_gam_9_re_bs_ti_total, select=c(3), ylim=c(-20,20))
title(main="Random smooths")
abline(h=0)
plot(m_gam_9_re_bs_ti_total, select=c(4), ylim=c(-20,20))
title(main="Random smooths")
abline(h=0)
```
![Dist_f_dam](/Plots/Dist_f_dam_7.png "Dist_f_dam")

```
par(mfrow=c(1,3))
plot(m_gam_9_re_bs_ti_total, select=c(5), ylim=c(-600,600))
title(main="Random smooths")
abline(h=0)
plot(m_gam_9_re_bs_ti_total, select=c(6), ylim=c(-600,600))
title(main="Random smooths")
abline(h=0)
plot(m_gam_9_re_bs_ti_total, select=c(7), ylim=c(-600,600))
title(main="Random smooths")
abline(h=0)
```
![Dist_f_dam](/Plots/Dist_f_dam_8.png "Dist_f_dam")

**Plot surface and one-dimensional differences in daily and weekly distance form dam between the three species**
```
par(mfrow=c(1,3))
plot_diff2(m_gam_9_re_bs_ti_total, view=c("daily","weekly"),
        comp=list(Species=c("pike", "pikeperch")),
        zlim=c(-5,7.5), nlevels=20, n.grid=30,
        main='Difference pike-pikeperch',
        print.summary=FALSE)
plot_diff2(m_gam_9_re_bs_ti_total, view=c("daily","weekly"),
        comp=list(Species=c("pikeperch", "wels")),
        zlim=c(-5,7.5), nlevels=20, n.grid=30,
        main='Difference pikeperch-wels',
        print.summary=FALSE)
plot_diff2(m_gam_9_re_bs_ti_total, view=c("daily","weekly"),
        comp=list(Species=c("pike", "wels")),
        zlim=c(-5,7.5), nlevels=20, n.grid=30,
        main='Difference pike-wels',
        print.summary=FALSE)
```
![Dist_f_dam](/Plots/Dist_f_dam_9.png "Dist_f_dam")

```
par(mfrow=c(1,3))
plot_diff(m_gam_9_re_bs_ti_total, view="daily",
        comp=list(Species=c("pike", "pikeperch")),
        main='Daily difference in distance from dam between pike and pikeperch')
plot_diff(m_gam_9_re_bs_ti_total, view="daily",
        comp=list(Species=c("pikeperch", "wels")),
        main='Daily difference in distance from dam between pikeperch and wels')
plot_diff(m_gam_9_re_bs_ti_total, view="daily",
        comp=list(Species=c("pike", "wels")),
        main='Daily difference in distance from dam between pike and wels')
```
![Dist_f_dam](/Plots/Dist_f_dam_10.png "Dist_f_dam")

```
par(mfrow=c(1,3))
plot_diff(m_gam_9_re_bs_ti_total, view="weekly",
        comp=list(Species=c("pike", "pikeperch")),
        main='Daily difference in distance from dam between pike and pikeperch')
plot_diff(m_gam_9_re_bs_ti_total, view="weekly",
        comp=list(Species=c("pikeperch", "wels")),
        main='Daily difference in distance from dam between pikeperch and wels')
plot_diff(m_gam_9_re_bs_ti_total, view="weekly",
        comp=list(Species=c("pike", "wels")),
        main='Daily difference in distance from dam between pike and wels')
```
![Dist_f_dam](/Plots/Dist_f_dam_11.png "Dist_f_dam")


# Daily and weekly dstance from dam by season

Because of the high computation time in the models with the _Species*season_ interaction we split the dataset into five seasons and analyze time series separately

## SPRING I

```
spring_I <- subset(data_distrdw,season=="winter")
```

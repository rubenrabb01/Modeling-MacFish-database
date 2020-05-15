# Daily and weekly distance range from dam

In this part, daily and weekly seasonalities are descomposed as predictor variables to model the time series of response variable _distance from dam_
- There are 1089 measurements from tag beeps a day for each individual that we will serve to model daily seasonality
- Every week count 7 days and will model weekly seasonality

## 1. Create a dataframe of daily and weekly distance range for each species

:books:`library(lubridate)`  
:books:`library(plyr)`  

Taking the main dataset _dist2dam.dt_, filter by individuals having more than 89 days of measures
```
data_distr_day<-dist2dam.dt[day.count > 89]
data_distr_day<-as.data.frame(data_distr_day)
```

Add info for each species and order by date
```
data_distr_day <- merge(dist2dam.dt, fish.info, by= c("fi_fishid"))
data_distr_day<-data_distr_day[with(data_distr_day, order(date)),]
```

Create a new numeric variable _week_num_ for day of the week based on _date_ (i.e., day)
```
data_distr_day$date <- as.Date(data_distr_day$date)
data_distr_day$week_day_num <- wday(data_distr_day$date)
```

Transform and rename variables including _week_day_ recoding with names of week days
```
data_distr_day[, week_day := as.factor(car::recode(week_day_num, "'1'='Monday';'2'='Tuesday';'3'='Wednesday';'4'='Thursday'; '5'='Friday';'6'='Saturday';'7'='Sunday'"))]
colnames(data_distr_day)[2] <- "time_min"
colnames(data_distr_day)[16] <- "Species"
data_distr_day$Species<-as.factor(data_distr_day$Species)
data_distr_day$fi_fishid<-as.factor(data_distr_day$fi_fishid)
```

Create new variables storing info for further model building
```
pikeperch <- unique(data_distr_day[, Species])      # only pikeperch data
Species_sub <- (data_distr_day[, Species])          # pike, pikeperch and wels
date_sub <- unique(data_distr_day[, date])
day_week_sub <- unique(data_distr_day[, week_day])
id_sub <- (data_distr_day[, fi_fishid])
time_min_day <- 1089
```

Taking data of distance from dam for the last month and storing it in object _data_distr_day_sub_
```
data_distr_day_sub <- data_distr_day[(fi_fishid == id_sub & Species == Species_sub & date %in% date_sub[290:320])]       # pike, pikeperch and wels
```

Plot distance from dam for last month of observations
```
ggplot(data_distr_day_sub, aes(time_min, distfromdam)) +
  geom_line() +
  theme(panel.border = element_blank(),
        panel.background = element_blank(),
        panel.grid.minor = element_line(colour = "grey90"),
        panel.grid.major = element_line(colour = "grey90"),
        panel.grid.major.x = element_line(colour = "grey90"),
        axis.text = element_text(size = 10),
        axis.title = element_text(size = 12, face = "bold")) +
  labs(x = "Date", y = "Distance range from dam [m]")
```

Define number of observations in the train set
```
N <- nrow(data_distr_day_sub)
```

Calculate number of days in the train set using start and end dates
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
However, this gives the complete dates without accounting for missing days

```
data_distr_day <- data_distr_day %>% add_count(date)
```
```
time_day_full <- 341
```

## 2. Subset data to fit a gam model
```
matrix_gam <- data.table(Dist_dam = data_distr_day_sub[, distfromdam], daily = rep(1:time_min_day, time_day_full), Species = data_distr_day_sub[, Species], Id = data_distr_day_sub[, fi_fishid], weekly = data_distr_day_sub[, week_day_num])
```

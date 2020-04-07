
# MacFish database

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

Min and max was set as 0.5% and 99.5% quantiles to reduce influence of single false positions

```
range.d2d <- dist2dam.dt[day.count > 89, .(min.dist = quantile(distfromdam,0.005), max.dist = quantile(distfromdam,0.995) ), by = .(date =as.Date(dd_timestamp_utc), fi_fishid)]

range.d2d.lg.t <- melt(range.d2d, id= c("date", "fi_fishid"), measure.vars = c("min.dist",  "max.dist"))

range.d2d.lg <- merge(range.d2d.lg.t, fish.info, by= c("fi_fishid"))
```

Daily difference between min and max distance from dam 

```
mean.ranged2d.t <- range.d2d[, .(ranged2d = max.dist - min.dist, meand2d = (min.dist + max.dist)/2), by = .(date, fi_fishid)]

mean.ranged2d <- merge(mean.ranged2d.t, fish.info, by= c("fi_fishid"))

mean.ranged2d[, fish.name := paste(ca_weight_g/1000,"kg ", fi_sex, sep="")]

mean.ranged2d[, month := month(date)]
```

Setting of seasons

```
mean.ranged2d[date > "2017-04-25" & date <= "2017-06-20", season := "spring_I"]

mean.ranged2d[date > "2017-06-20" & date <= "2017-09-20", season := "summer"]

mean.ranged2d[date > "2017-09-20" & date <= "2017-12-20", season := "autumn"]

mean.ranged2d[date > "2017-12-20" & date <= "2018-03-20", season := "winter"]

mean.ranged2d[date > "2018-03-20" & date <= "2018-08-20", season := "spring_II"]

setkey(mean.ranged2d,ca_weight_g)
```

Calculation of difference between maximum and minimum distance as daily range

```
full.range.season.t <- range.d2d[, .(ranged2d = max(max.dist)-min(min.dist)), by = .( fi_fishid)]

full.range.season <- merge(full.range.season.t, fish.info, by= c("fi_fishid"))

full.range.season[, fish.name := paste(ca_weight_g/1000,"kg ", fi_sex, sep="")]

setkey(full.range.season,ca_weight_g)
```

Calculation of body condition (residuals of the regression of body mass on body length)

```
condition <- lm(ca_weight_g~ca_tl_mm, mean.ranged2d, na.action=na.exclude)

summary(condition)

mean.ranged2d$r3_condition<-rstandard(condition)
```

Combine dfs by _fi_fishid_

```
fish.capture2 <- as.data.table(fish.capture[,1:3])

mean.ranged2d <- merge(mean.ranged2d,fish.capture2, by="fi_fishid")
```


group_ranged2d <- data.table(group = ranged2d.cld$.group, season = ranged2d.cld$season, species = ranged2d.cld$fi_species)
group_ranged2d[, label := paste(group, species, sep = "-")]





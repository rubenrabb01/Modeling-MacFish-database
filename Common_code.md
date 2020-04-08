
# MacFish database (Initial steps)

### Connect to the server and import/load data

:books:`library(tidyverse)`     
:books:`library(data.table)`  
:books:`library(RPostgreSQL)`

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
```
group_ranged2d <- data.table(group = ranged2d.cld$.group, season = ranged2d.cld$season, species = ranged2d.cld$fi_species)
group_ranged2d[, label := paste(group, species, sep = "-")]
```

### Calculation of overall longtudional range from mean daily distance from a dam 

```
distance.range.t <- mean.ranged2d[, .(dist.range = max(meand2d)-min(meand2d)), by = .(fi_fishid, season)] 
distance.range <- merge(distance.range.t, fish.info)
distance.range$season <- factor(distance.range$season, levels=c("spring_I", "summer", "autumn","winter", "spring_II"))
```
```
distance.range.full.t <- mean.ranged2d[, .(dist.range = max(meand2d)-min(meand2d)), by = .(fi_fishid)] 
distance.range.full <- merge(distance.range.full.t, fish.info)
```
```
ggplot(distance.range.full, aes(x = ca_weight_g/1000, y=dist.range )) +
  geom_point()+
  facet_wrap(~fi_species, scale="free_x")+
  ylab("range of longitudional movement (m)")+ xlab("")
```
![distance.range.full](/Plots/distance.range.full.png "distance.range.full")

### Calculation of mean depth range 

```
mean_depth_dt <- dist2dam.dt[day.count > 89, .(mean_depth = mean(dd_depth,na.rm =T)), by = .(date =as.Date(dd_timestamp_utc), fi_fishid)]
mean_depth_dt[date > "2017-04-25" & date <= "2017-06-20", season := "spring_I" ]
mean_depth_dt[date > "2017-06-20" & date <= "2017-09-20", season := "summer" ]
mean_depth_dt[date > "2017-09-20" & date <= "2017-12-20", season := "autumn" ]
mean_depth_dt[date > "2017-12-20" & date <= "2018-03-20", season := "winter" ]
mean_depth_dt[date > "2018-03-20" & date <= "2018-08-20", season := "spring_II"]
mean_depth_dt <- merge(mean_depth_dt, fish.info, by= c("fi_fishid"))
```
### Calculation of cumulative distance from dam

```
cum.activity <- mean.ranged2d
setkey(cum.activity, date)
cum.activity[, diff_mean_dist := c( 0, abs(diff(meand2d, lag = 1))), by = .(fi_fishid)]
cum.activity[, cum_displac := cumsum(diff_mean_dist), by = .(fi_fishid)]
```
```
ggplot(cum.activity, aes(x = date , y =  cum_displac, group = fi_fishid, col = fi_fishid))+geom_line()+facet_wrap(~fi_species)
```
![cum_activity](/Plots/cum_activity.png "cum_activity")


### Calculation of distance to dam development

```
roundUp <- function(x,to=100)
{
  to*(x%/%to + as.logical(x%%to))
}

mean.ranged2d[, round.d2d := roundUp(meand2d)]

jpeg("All_species_d2d_develop.jpg", width = 9, height=5,  units = 'in', res = 300 )
ggplot(mean.ranged2d,aes(x=date,y=meand2d))+
  stat_density_2d(geom = "raster", aes(fill = stat(density)), contour = FALSE)+
  scale_fill_gradient(low = "blue", high = "red")+
  geom_point(size = 0.01, col = "black")+
  geom_vline(xintercept = as.Date("2017-11-27"), color = "white", size=0.5)+
  labs(x ="", y = "distance from dam (m)")+
  facet_wrap(~fi_species)+
  theme_bw()+ theme(
    text = element_text(size=15),
    axis.text.x = element_text(angle=45, hjust=1),
    panel.grid = element_blank(),
    strip.background = element_blank(),
    legend.title=element_blank())
dev.off()
```
![All_species_d2d_develop](/Plots/All_species_d2d_develop.png "All_species_d2d_develop")

### Calculation of time spent out in the river 

Select species

```
select.fish.qu <- 
  paste("SELECT fi_fishid
        FROM teri.fish
        WHERE
        fi_species in ('pike', 'wels', 'pikeperch')  ;", sep = "")
```
Get tag numbers

```
tagid.list <- data.table(dbGetQuery(con,select.fish.qu ))
tagid.list <- tagid.list[,fi_fishid]
```
Get detections from single receivers the tributary (1500118,119)
```
select.detections.qu <- paste("
                              SELECT hys_id, dd_timestamp_utc, ht_hsn, fi_fishid, dd_depth, dd_bodytemp FROM teri.detsdepth_rec_fish
                              where 
                              ht_hsn IN (1500118, 1500119) AND
                              NOT dead AND
                              dd_timestamp_utc BETWEEN ","'",start.summer.time,"'", "AND","'", end.summer.time,"'", " AND 
                              fi_fishid IN ('", paste(tagid.list, collapse = "','"),"');", sep = "") 
```
```
det_rivout_summer <- data.table(dbGetQuery(con, select.detections.qu ))
```
Rename receivers to get the same name for winter array

*rec_position == 1*, the receiver closest to the river
```
det_rivout_summer[ht_hsn == 1500118 , rec_position := 2]
det_rivout_summer[ht_hsn == 1500119 , rec_position := 1]
```
Get detections from winter
```
select.detections.wint.qu <- paste("
                                   SELECT hys_id, dd_timestamp_utc, ht_hsn, fi_fishid, dd_depth, dd_bodytemp FROM teri.detsdepth_rec_fish
                                   where 
                                   ht_hsn IN (1500048, 1500049) AND
                                   dd_timestamp_utc BETWEEN ","'", start.winter.time,"'", "AND","'", end.winter.time,"'", " AND 
                                   NOT dead AND
                                   fi_fishid IN ('", paste(tagid.list, collapse = "','"),"');", sep = "") 
```
```
det_rivout_winter <- data.table(dbGetQuery(con, select.detections.wint.qu))
det_rivout_winter[ht_hsn == 1500048 , rec_position := 2]
det_rivout_winter[ht_hsn == 1500049 , rec_position := 1]
det_rivout <- rbind(det_rivout_summer, det_rivout_winter )
```
Get only relevant fish (fish with more than x counts specified by number of days)

```
valid.fish <- unique(dist2dam.dt$fi_fishid)

det_rivout <- det_rivout[fi_fishid %in% valid.fish,] 
det_rivout[, id_detect := rleid(rec_position), by = fi_fishid]
det_rivout[, id_det_count := length(rec_position), by = .(id_detect, fi_fishid)]

time_rivout <- det_rivout[rec_position == 1 & id_det_count >= 2, ]
time.riv <- time_rivout[,.(time_inriv = range(dd_timestamp_utc), ardep = c("ar", "dep")), by = .(id_detect, fi_fishid)]

time.riv.wide <- data.table::dcast(time.riv, fi_fishid + id_detect ~ ardep, value.var = "time_inriv" )
time.riv.wide[, time.diff := as.numeric(dep - ar)]

sumtime.rivout <- time.riv.wide[, .(sum.time = sum(time.diff)), by = .(fi_fishid)] 
setkey(sumtime.rivout, sum.time)

time.riv.info <- merge(time.riv, fish.info, by = c("fi_fishid"))
time.riv.info[, fi_fishid_fac := factor(fi_fishid, levels = sumtime.rivout$fi_fishid, ordered = T)]
```
Get death time of fish 

```
select.deathdate.qu <- paste("SELECT fi_fishid, fs_active_till_utc FROM teri.fishstatus
                             WHERE
                             fi_fishid IN ('", paste(tagid.list, collapse = "','"),"');", sep = "") 

death.date <- data.table(dbGetQuery(con,select.deathdate.qu))

tag.list.tributary <- unique(time.riv.info$fi_fishid)
  
death.date <- death.date[fi_fishid %in% tag.list.tributary, ]
death.date.info <- merge(death.date, fish.info, by = c("fi_fishid"))
```
Plot fish detection and death date
```
jpeg("All_river_excursions.jpg", width = 9, height=5,  units = 'in', res = 300 )

ggplot(data = time.riv.info)+
  geom_line(data = time.riv.info, aes(x = time_inriv, y  = fi_fishid_fac, group = interaction(fi_fishid,id_detect)), size = 1) +
  geom_point(data = death.date.info, aes(y = fi_fishid, x = fs_active_till_utc), col = "red", size = 2) +
  facet_wrap(~fi_species, scale = "free_y", ncol = 1) +
  #geom_vline(xintercept = as.POSIXct("2017-11-21"), color = "red", size=1) +
  theme(
    text = element_text(size=15),
    #axis.text.y=element_blank(),
    axis.title.y=element_blank(),
    axis.title.x=element_blank(),
    strip.background = element_blank(),
    panel.background = element_blank(),
    panel.grid.major.x = element_line( size=.1, color="black" ),
    panel.grid.major.y = element_line( size=.1, color="black" )
  )
dev.off()
```
![All_river_excursions](/Plots/All_river_excursions.png "All_river_excursions")

### Calculation of time spent in the river 

Select release date

```
select.releasedate.qu <- paste("SELECT fi_fishid, ca_timestamp_release_utc FROM teri.capture
                             WHERE
                             fi_fishid IN ('", paste(tagid.list, collapse = "','"),"');", sep = "") 

release.date <- data.table(dbGetQuery(con,select.releasedate.qu))
release.date <- release.date[fi_fishid %in% tag.list.tributary, ]

timeactive <- merge(release.date,death.date, by = c("fi_fishid") )
timeactive[, timeactive := as.numeric(fs_active_till_utc - ca_timestamp_release_utc)*24*60]

prop_time_trib <- merge(sumtime.rivout, timeactive[,.(fi_fishid , timeactive)], by = c("fi_fishid"))
prop_time_trib[, prop_time := (sum.time/timeactive)*100]
prop_time_trib <- merge(prop_time_trib, fish.info, by = c("fi_fishid"))
```
Select activity out in the river and tributary
```
select.fish.qu <- 
  paste("SELECT fi_fishid
        FROM teri.fish
        WHERE
        fi_species in ('pike', 'wels', 'pikeperch')  ;", sep = "")
```
Get tag numbers
```
tagid.list <- data.table(dbGetQuery(con,select.fish.qu ))
tagid.list <- tagid.list[,fi_fishid]
```
Get detections from single receivers the tributary (1500118,119) 
```
select.detections.qu <- paste("
                              SELECT hys_id, dd_timestamp_utc, ht_hsn, fi_fishid, dd_depth, dd_bodytemp FROM teri.detsdepth_rec_fish
                              where 
                              ht_hsn IN (1500116, 1500118, 1500119) AND
                              NOT dead AND
                              dd_timestamp_utc BETWEEN ","'",start.summer.time,"'", "AND","'", end.summer.time,"'", " AND 
                              fi_fishid IN ('", paste(tagid.list, collapse = "','"),"');", sep = "") 

det_rivout_summer <- data.table(dbGetQuery(con, select.detections.qu ))
```
Rename receivers to get the same name for winter array

*rec_position == 1* , the receiver closest to the river

```
det_rivout_summer[ht_hsn == 1500119 , rec_position := 2]
det_rivout_summer[ht_hsn == 1500118 , rec_position := 1]
det_rivout_summer[ht_hsn == 1500116 , rec_position := 3]
```
Get detections from winter
```
select.detections.wint.qu <- paste("
                                   SELECT hys_id, dd_timestamp_utc, ht_hsn, fi_fishid, dd_depth, dd_bodytemp FROM teri.detsdepth_rec_fish
                                   where 
                                   ht_hsn IN (1500048, 1500049,1500039) AND
                                   dd_timestamp_utc BETWEEN ","'", start.winter.time,"'", "AND","'", end.winter.time,"'", " AND 
                                   NOT dead AND
                                   fi_fishid IN ('", paste(tagid.list, collapse = "','"),"');", sep = "") 

det_rivout_winter <- data.table(dbGetQuery(con, select.detections.wint.qu))
det_rivout_winter[ht_hsn == 1500039 , rec_position := 3]
det_rivout_winter[ht_hsn == 1500048 , rec_position := 1]
det_rivout_winter[ht_hsn == 1500049 , rec_position := 2]

det_rivout <- rbind(det_rivout_summer, det_rivout_winter )
det_rivout[, no_det := length(dd_timestamp_utc), by = .(fi_fishid)]
```
Get only relevant fish (fish with more than x counts specified by number of days)

```
valid.fish <- unique(dist2dam.dt$fi_fishid)
det_rivout <- det_rivout[no_det > 1 & rec_position %in% c(1,2) & fi_fishid %in% valid.fish,] 
setkey(det_rivout, fi_fishid, dd_timestamp_utc)
```
Application of function for detection of movement direction
```
det_rivout[, move_direc := move_direction_river(rec_position), by = .(fi_fishid)]
det_rivout[, date := as.Date(dd_timestamp_utc)]
```
:warning: The _move_direction_river_ function is missing 

Subset of records indicating location change
```
river_time <- det_rivout[move_direc %in% c("Upstream", "Downstream"),]
river_time[, run_valid := valid_river_run(move_direc), by = .(fi_fishid)]
```
Calculation of number of observations
```
no_obsr <- river_time[,.(number = length(ht_hsn)), by =.(fi_fishid)]

river_time[, even_obs :=  ifelse(is_even(length(hys_id)) == T, "even", "odd") ,by = .(fi_fishid)] 
river_time[, obs_id := c(1:nrow(river_time))]
river_time_exclude <- river_time[even_obs == "odd" ,.SD[.N],  by = fi_fishid]

river_time_sub <- river_time[!obs_id %in% river_time_exclude$obs_id ]
river_time_sub[, event_id := rep(1:c(nrow(river_time_sub)/2), each = 2)]

river_time_f <- data.table::dcast(river_time_sub[,.(dd_timestamp_utc, fi_fishid, move_direc,event_id)], fi_fishid + event_id ~ move_direc, value.var = "dd_timestamp_utc" )
river_time_f[, time_diff := as.numeric(Downstream -Upstream)]

river_time_sum <- river_time_f[,.(sum_time = sum(time_diff)), by = .(fi_fishid)]
river_time_sum[, time_days := sum_time/(60*24)]

river_time_sum <- merge(river_time_sum, fish.info, by = c("fi_fishid"))
setkey(river_time_sum, time_days)

range(river_time_f$time_diff)
table(river_time$even_obs)
```
:books: `library(sjmisc)`

```
river_time[run_valid == "Valid", .(no_det = (length(dd_timestamp_utc))), by = .(fi_fishid, move_direc)]
river_time[run_valid == "Valid", .(no_det = (length(dd_timestamp_utc))), by = .(fi_fishid, move_direc)]
```
Plot movement direction detected
```
ggplot(det_rivout, aes(y = fi_fishid, x = dd_timestamp_utc, col = move_direc)) + geom_point( size = 2)
``` 
```
str(det_rivout)
table(det_rivout[, .(move_direc) ])
unique(det_rivout[, .(fi_fishid, rec_position) ])
table(det_rivout[fi_fishid == "T449319_1", ]$move_direc)

time.riv <- time_rivout[,.(time_inriv = range(dd_timestamp_utc), ardep = c("ar", "dep")), by = .(id_detect, fi_fishid)]
time.riv.wide <- data.table::dcast(time.riv, fi_fishid + id_detect ~ ardep, value.var = "time_inriv" )
time.riv.wide[, time.diff := as.numeric(dep - ar)]

sumtime.rivout <- time.riv.wide[, .(sum.time = sum(time.diff)), by = .(fi_fishid)] 
setkey(sumtime.rivout, sum.time)

time.riv.info <- merge(time.riv, fish.info, by = c("fi_fishid"))
time.riv.info[, fi_fishid_fac := factor(fi_fishid, levels = sumtime.rivout$fi_fishid, ordered = T)]
```
Get death time of fish 
```
select.deathdate.qu <- paste("SELECT fi_fishid, fs_active_till_utc FROM teri.fishstatus
                             WHERE
                             fi_fishid IN ('", paste(tagid.list, collapse = "','"),"');", sep = "") 

death.date <- data.table(dbGetQuery(con,select.deathdate.qu))
tag.list.tributary <- unique(time.riv.info$fi_fishid)
death.date <- death.date[fi_fishid %in% tag.list.tributary, ]
death.date.info <- merge(death.date, fish.info, by = c("fi_fishid"))
```
Plot fish detection and death date
```
jpeg("All_river_excursions.jpg", width = 9, height=5,  units = 'in', res = 300 )

ggplot(data = time.riv.info)+
  geom_line(data = time.riv.info, aes(x = time_inriv, y  = fi_fishid_fac, group = interaction(fi_fishid,id_detect)), size = 1)+
  geom_point(data = death.date.info, aes(y = fi_fishid, x = fs_active_till_utc), col = "red", size = 2)+
  facet_wrap(~fi_species, scale = "free_y", ncol = 1)+
  #geom_vline(xintercept = as.POSIXct("2017-11-21"), color = "red", size=1)+
  theme(
    text = element_text(size=15),
    #axis.text.y=element_blank(),
    axis.title.y=element_blank(),
    axis.title.x=element_blank(),
    strip.background = element_blank(),
    panel.background = element_blank(),
    panel.grid.major.x = element_line( size=.1, color="black" ),
    panel.grid.major.y = element_line( size=.1, color="black" )
  )
dev.off()
```
![All_river_excursions_2](/Plots/All_river_excursions2.png "All_river_excursions_2")

### Calculation of step length and NSD 

**NSD  - D2Dt0 distance to dam in time 0**

```
cum.activity[, D2Dt0 := meand2d[1], by = fi_fishid]
cum.activity[, NetDist := sqrt((meand2d - D2Dt0)^2)]
cum.activity[, NSD := (meand2d - D2Dt0)^2]
```
```
jpeg("pikeperch_NSD.jpg", width = 9, height=5,  units = 'in', res = 300 )
ggplot(cum.activity[fi_species == "pikeperch",], aes(x = date , y =  NSD, group = fi_fishid, col = fi_fishid))+geom_line()+facet_wrap(~fi_fishid)+
  ggtitle("pikeperch")+
  theme(
    axis.text.x = element_text(angle = 30, vjust = 0.8)
  )
dev.off()
```
![NSD_pikeperch](/Plots/pikeperch_NSD.png "NSD_pikeperch")
```
jpeg("pike_NSD.jpg", width = 9, height=5,  units = 'in', res = 300 )
ggplot(cum.activity[fi_species == "pike",], aes(x = date , y =  NSD, group = fi_fishid, col = fi_fishid))+geom_line()+facet_wrap(~fi_fishid)+
  ggtitle("pike")+
theme(
  axis.text.x = element_text(angle = 30, vjust = 0.8)
)
dev.off()
```
![NSD_pike](/Plots/pike_NSD.png "NSD_pike")
```
jpeg("wels_NSD.jpg", width = 9, height=5,  units = 'in', res = 300 )
ggplot(cum.activity[fi_species == "wels",], aes(x = date , y =  NSD, group = fi_fishid, col = fi_fishid))+geom_path()+facet_wrap(~fi_fishid)+
  ggtitle("wels")+
  theme(
    axis.text.x = element_text(angle = 30, vjust = 0.8)
  )
dev.off()
```
![NSD_wels](/Plots/wels_NSD.png "NSD_wels")







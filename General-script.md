
### CONNECT TO THE SERVER AND IMPORT/LOAD THE DATA


`con <-  dbConnect(drv = PostgreSQL(), dbname ="teridb", host="localhost", user= "teriuser", password = "t3r1us3r!")`

`start.summer.time <- as.POSIXct('2017-04-27 00:00:00', tz = "UTC")
end.summer.time <-  as.POSIXct('2017-11-20 23:59:59', tz="UTC")`

`start.winter.time <- as.POSIXct('2017-11-27 00:00:00', tz = "UTC")
end.winter.time <-  as.POSIXct('2018-05-01 23:59:59', tz="UTC")`

`setwd("~/Teri/longit_displacement")
dist2dam.dt <- data.table(read_csv("./data/Teri_dis2data_predatory_fullarray.csv"))`

### Extracting info for fish

`fish.info <- data.table(dbGetQuery(con, "SELECT ca_tl_mm, ca_weight_g, b.* FROM teri.capture a INNER JOIN teri.fish b ON a.fi_fishid = b.fi_fishid"))`
`fish.capture <- data.table(dbGetQuery(con, "SELECT ca_lat_catch, ca_lon_catch, b.* FROM teri.capture a INNER JOIN teri.fish b ON a.fi_fishid = b.fi_fishid"))`

                                                                                
### For my local R env I set working dir and load the data already downloaded to R Studio:                                                                                                                                          
`setwd("C:/IBU/Fishecudb/")`                                                      
                                                                       
`dist2dam.dt <- data.table(readr::read_csv("Teri_dis2data_predatory_fullarray.csv"))`  

**Note**: Include the function *readr* for reading timestamp or there may be reading errors
                                                                            
### Fast load of full dataset                                                 

`dist2dam.dt <- read.csv("Teri_dis2data_predatory_fullarray.csv")`
                                             
### Export data from R Studio to my local R env directory:                                  
                                                                                
`write.table(fish.capture, "fish_capture", sep="\t")`            

`write.csv(fish.capture, "fish_capture.csv")`                                   

`fish.capture <- data.table(read_csv("fish_capture.csv"))`                      
                                                                                
`write.csv(fish.info, "fish_info.csv")`                                          

`fish.info <- data.table(read_csv("fish_info.csv"))`                              

`m1<-lmer(ca_lon_catch ~ ca_lat_catch*fi_species + (1| fi_species), data=fish.capture,na.action=na.omit)
probe_interaction(m1, modx  = fi_species, pred = ca_lat_catch, plot.points=TRUE,cond.int = TRUE, interval = TRUE,jnplot = TRUE)`


### Calculation of range of distances (calculation only from fish we have more than 3 months of data)

#### min aand max was set as 0.5% and 99.5% quantiles to reduce influence of single false positions

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

`model.ranged2d <- lmer(sqrt(ranged2d+1)~fi_species*season+(1|fi_fishid), data =mean.ranged2d,
                       REML = T, control = lmerControl(optimizer = "bobyqa"))`

`model.ranged2d <- lmer(sqrt(ranged2d+1)~season+(1+season|fi_fishid), data =mean.ranged2d[fi_species == "pikeperch"],
                       REML = T, control = lmerControl(optimizer = "bobyqa"))`

`model.ranged2d <- lme(sqrt(ranged2d+1)~fi_species*season, random =~1+fi_species|fi_fishid, data =mean.ranged2d, method = "REML")`

`summary(model.ranged2d)`

`qqnorm(model.ranged2d)`

`plot(model.ranged2d)`


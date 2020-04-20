
# Is there selection on the use of different reservoir parts?

### Create a dataframe including reservoir parts 

:books:`library(plyr)`

Subset preference of reservoir parts and merge with the full 'data' dataset 
```
data.parts.pref.info <- subset(parts.pref.full,  select=c(fi_fishid, date, res_part))
data_poglm <- full_join(data, data.parts.pref.info, by = c("fi_fishid", "date"))
```
Count use of reservoir parts by season
```
data_poglm <- data_poglm %>% add_count(season, fi_fishid, res_part)
```
Add a new ordinal variable for res_part ordered 0-4
```
data_poglm$order_res_part <- revalue(data_poglm$res_part,c("dam"="0", "middle"="1", "upper"="2", "tributary"="3"))
```
Convert variables fro analysis
```
data_poglm$season <- as.factor(data_poglm$season)
data_poglm$res_part <- as.factor(data_poglm$res_part)
data_poglm$order_res_part <- as.factor(data_poglm$order_res_part)
data_poglm$fi_fishid <- as.factor(data_poglm$fi_fishid)
data_poglm$fi_species <- as.factor(data_poglm$fi_species)
```
Create five datasets for each of the five time periods 
```
spring_I <- subset(data_poglm,season=="spring_I")
spring_II <- subset(data_poglm,season=="spring_II")
summer <- subset(data_poglm,season=="summer")
autumn <- subset(data_poglm,season=="autumn")
winter <- subset(data_poglm,season=="winter")
```




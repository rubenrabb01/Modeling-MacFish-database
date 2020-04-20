
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
Create a new ordered response variable for res_part with 4 categories (0-4) and rename variable body size
```
data_poglm$res_part_order <- revalue(data_poglm$res_part,c("dam"="0", "middle"="1", "upper"="2", "tributary"="3"))
colnames(data_poglm)[5] <- "body_size"
```
Convert variables for analysis
```
data_poglm$season <- as.factor(data_poglm$season)
data_poglm$res_part <- as.factor(data_poglm$res_part)
data_poglm$res_part_order <- as.factor(data_poglm$res_part_order)
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
### Fit Proportional Odds Model for ordered response of use of reservoir parts

Our logic is as follows:

- Dam: Fish show closeness to source and low displacement from it (Stationarity)
- Middle: Fish move from dam (out of stationarity)
- Upper: Fish cover long distance from dam
- Tributary: Maximum displacement from dam (high prey activity - predation)



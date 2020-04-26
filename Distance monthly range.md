# How does distance monthly range change?

## Create a dataframe including monthly range and reservoir parts

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
```
data_distr
```

| fi_fishid | mon_yr  | month | dist.range | day_count | ca_tl_mm | ca_weight_g | fi_sex | fi_species | dam | middle | tributary | upper |
|-----------|---------|-------|------------|-----------|----------|-------------|--------|------------|-----|--------|-----------|-------|
| T449202_1 | 10_2017 | 10    | 3609.092   | 31        | 430      | 605         | M      | pikeperch  | 0   | 3      | 9         | 27    |
| T449202_1 | 12_2017 | 12    | 3165.064   | 31        | 430      | 605         | M      | pikeperch  | 0   | 2      | 20        | 15    |
| T449202_1 | 1_2018  | 1     | 2573.331   | 31        | 430      | 605         | M      | pikeperch  | 0   | 3      | 23        | 12    |
| T449202_1 | 2_2018  | 2     | 2573.331   | 28        | 430      | 605         | M      | pikeperch  | 0   | 0      | 25        | 6     |
| T449319_1 | 6_2017  | 6     | 3042.14    | 29        | 1070     | 7700        | M      | wels       | 3   | 29     | 4         | 5     |
| T449319_1 | 7_2017  | 7     | 5408.336   | 31        | 1070     | 7700        | M      | wels       | 4   | 29     | 3         | 8     |
| T449319_1 | 8_2017  | 8     | 2496.028   | 30        | 1070     | 7700        | M      | wels       | 13  | 30     | 0         | 6     |
| T449319_1 | 9_2017  | 9     | 1086.458   | 29        | 1070     | 7700        | M      | wels       | 14  | 29     | 0         | 0     |
| T449209_1 | 11_2017 | 11    | 577.9322   | 27        | 640      | 1750        | X      | pike       | 0   | 27     | 0         | 22    |
| T449209_1 | 12_2017 | 12    | 587.6172   | 31        | 640      | 1750        | X      | pike       | 1   | 31     | 0         | 21    |
| T449209_1 | 2_2018  | 2     | 4383.3361  | 28        | 640      | 1750        | X      | pike       | 5   | 27     | 0         | 22    |
| T449209_1 | 3_2018  | 3     | 5059.2163  | 31        | 640      | 1750        | X      | pike       | 17  | 22     | 2         | 12    |

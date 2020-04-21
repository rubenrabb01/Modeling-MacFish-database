
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
Create a new ordered response variable for res_part with 4 categories (0-3) and rename variable body size
```
data_poglm$res_part_order <- revalue(data_poglm$res_part,c("dam"="0", "middle"="1", "upper"="2", "tributary"="3"))
colnames(data_poglm)[5] <- "body_size"
```
| fi_fishid | date       | season | _species fish.name | res_part  | res_part_order | ranged2d  | meand2d   | mean_depth | body_size | ca_lat_catch c | ca_lon_catch |
|-----------|------------|--------|--------------------|-----------|----------------|-----------|-----------|------------|-----------|----------------|--------------|
| T449202_1 | 18/08/2017 | summer | pikeperch          | middle    | 1              | 784.0089  | 8159.387  | 1.4189065  | 430       | 48.82551       | 14.4848      |
| T449202_1 | 25/01/2018 | winter | pikeperch          | dam       | 0              | 0         | 8551.391  | 1.6873192  | 430       | 48.82551       | 14.4848      |
| T449202_1 | 24/01/2018 | winter | pikeperch          | upper     | 2              | 2301.8921 | 7400.445  | 5.5925592  | 430       | 48.82551       | 14.4848      |
| T449276_1 | 19/08/2017 | summer | wels               | middle    | 1              | 784.0089  | 8159.387  | 0.6493313  | 1030      | 48.81964       | 14.48151     |
| T449276_1 | 20/08/2017 | summer | wels               | dam       | 0              | 2129.6148 | 7486.584  | 0.6721918  | 1030      | 48.81964       | 14.48151     |
| T449276_1 | 21/08/2017 | summer | wels               | tributary | 3              | 2093.8532 | 7504.465  | 0.8017406  | 1030      | 48.81964       | 14.48151     |
| T449271_1 | 17/08/2017 | summer | pike               | upper     | 2              | 12:17:24  | 2402.5503 | 5.6171613  | 915       | 48.83096       | 14.47816     |
| T449271_1 | 18/08/2017 | summer | pike               | tributary | 3              | 22:07:05  | 2371.9243 | 5.7136752  | 915       | 48.83096       | 14.47816     |
| T449271_1 | 23/12/2017 | winter | pike               | upper     | 2              | 17:04:54  | 6905.8552 | 3.0325587  | 915       | 48.83096       | 14.47816     |

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
### Fit Proportional Odds Model (POGLMM) to the use of reservoir parts

Our logic for the ordered response in use of reservoir parts is as follows:

- Dam: "0" (Fish show closeness to source and low displacement from it)
- Middle: "1" (Fish move from dam)
- Upper: "2" (Fish cover long distance from dam)
- Tributary: "3" (Maximum displacement from dam (high prey activity - predation))

:books:`library(ART)`     
:books:`library(mlogit)`  
:books:`library(AICcmodavg)`  
:books:`library(MASS)`  
:books:`library(effects)`  
:books:`library(lme4)`  
:books:`library(languageR)`  
:books:`library(ordinal)`  

Select random-effects by fitting a series of (null) _POGLMMs_ including slope models allowing variation of mean_depth and body_size across species and individuals 
```
Cand.mod<-matrix(ncol=1,nrow=11)
colnames(Cand.mod)<-c("BIC")
rownames(Cand.mod)<-c(1:11)

Cand.mod[1]<-AIC(clmm(res_part_order ~ 1  + (1| fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[2]<-AIC(clmm(res_part_order ~ 1  + (1| fi_species/fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[3]<-AIC(clmm(res_part_order ~ 1  + (1 + fi_species| fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[4]<-AIC(clmm(res_part_order ~ 1  + (1 + fi_species| fi_fishid) + (0 + fi_species| fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[5]<-AIC(clmm(res_part_order ~ 1  + (1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[6]<-AIC(clmm(res_part_order ~ 1  + (1 + mean_depth| fi_species) + (1 | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[7]<-AIC(clmm(res_part_order ~ 1  + (1 + mean_depth| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[8]<-AIC(clmm(res_part_order ~ 1  + (1| fi_species) + (1 + body_size | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[9]<-AIC(clmm(res_part_order ~ 1  + (1 + body_size| fi_species) + (1 | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[10]<-AIC(clmm(res_part_order ~ 1  + (1 + body_size| fi_species) + (1 + body_size | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[11]<-AIC(clmm(res_part_order ~ 1  + (1| fi_species) + (1 + date | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))

Cand.mod
sort(Cand.mod)
both<-data.frame(1:11,Cand.mod)
names(both)<-c("model","BIC")
both[do.call(order, both[c("BIC")]), ]


   model      BIC
5      5 10849.23
7      7 10851.23
6      6 11500.64
11    11 12426.27
2      2 12426.80
8      8 12429.32
9      9 12430.80
10    10 12433.32
3      3 12433.84
4      4 12445.84
1      1 26286.37
```
:part_alternation_mark: _Alternatively_, compare the two most common RF models
```
m_id<-clmm(res_part_order ~ 1  + (1| fi_fishid),data = data_poglm, link="logit",Hess=T)
m_id_sp<-clmm(res_part_order ~ 1  + (1| fi_fishid) + (1 | fi_species),data = data_poglm, link="logit",Hess=T)
```
**m_id_sp** is preferred over **m_id 

Fit models including predictors using the random-effects structure of the best-fit model (5)
```
Cand.mod<-matrix(ncol=1,nrow=11)
colnames(Cand.mod)<-c("BIC")
rownames(Cand.mod)<-c(1:11)

Cand.mod[[1]]<-AIC(clmm(res_part_order ~ 1 + (1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[[2]]<-AIC(clmm(res_part_order ~ 1 + body_size+mean_depth+fi_species+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[[3]]<-AIC(clmm(res_part_order ~ 1 + body_size*mean_depth+fi_species+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[[4]]<-AIC(clmm(res_part_order ~ 1 + body_size*fi_species+mean_depth+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[[5]]<-AIC(clmm(res_part_order ~ 1 + body_size+fi_species+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[[6]]<-AIC(clmm(res_part_order ~ 1 + body_size+mean_depth+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[[7]]<-AIC(clmm(res_part_order ~ 1 + body_size*mean_depth+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[[8]]<-AIC(clmm(res_part_order ~ 1 + body_size+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[[9]]<-AIC(clmm(res_part_order ~ 1 + mean_depth+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[[10]]<-AIC(clmm(res_part_order ~ 1 + mean_depth*fi_species+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))
Cand.mod[[11]]<-AIC(clmm(res_part_order ~ 1 + mean_depth+fi_species+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T))

Cand.mod
sort(Cand.mod)
both<-data.frame(1:11,Cand.mod)
names(both)<-c("model","BIC")
both[do.call(order, both[c("BIC")]), ]

   model      BIC
9      9 10845.35
6      6 10846.90
11    11 10847.46
4      4 10848.34
7      7 10848.60
2      2 10848.79
1      1 10849.23
10    10 10849.92
3      3 10850.47
8      8 10850.79
5      5 10852.72
```
Rename five best models
```
m9<-clmm(res_part_order ~ 1 + mean_depth+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T)
m6<-clmm(res_part_order ~ 1 + body_size+mean_depth+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T)
m11<-clmm(res_part_order ~ 1 + mean_depth+fi_species+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T)
m4<-clmm(res_part_order ~ 1 + body_size*fi_species+mean_depth+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T)
m7<-clmm(res_part_order ~ 1 + body_size*mean_depth+(1| fi_species) + (1 + mean_depth | fi_species:fi_fishid),data = data_poglm, link="logit",Hess=T)
```
Compare the first best model with the others 
```
lrtest(m9,m6)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + mean_depth + (1 | fi_species) + (1 + mean_depth | 
    fi_species:fi_fishid)
Model 2: res_part_order ~ 1 + body_size + mean_depth + (1 | fi_species) + 
    (1 + mean_depth | fi_species:fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   8 -5414.7                     
2   9 -5414.5  1 0.4488     0.5029
```
```
lrtest(m9,m11)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + mean_depth + (1 | fi_species) + (1 + mean_depth | 
    fi_species:fi_fishid)
Model 2: res_part_order ~ 1 + mean_depth + fi_species + (1 | fi_species) + 
    (1 + mean_depth | fi_species:fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   8 -5414.7                     
2  10 -5413.7  2 1.8917     0.3883
```
```
lrtest(m9,m4)
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + mean_depth + (1 | fi_species) + (1 + mean_depth | 
    fi_species:fi_fishid)
Model 2: res_part_order ~ 1 + body_size * fi_species + mean_depth + (1 | 
    fi_species) + (1 + mean_depth | fi_species:fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   8 -5414.7                     
2  13 -5411.2  5 7.0085       0.22
```
```
lrtest(m9,m7)             
```
```
Likelihood ratio test

Model 1: res_part_order ~ 1 + mean_depth + (1 | fi_species) + (1 + mean_depth | 
    fi_species:fi_fishid)
Model 2: res_part_order ~ 1 + body_size * mean_depth + (1 | fi_species) + 
    (1 + mean_depth | fi_species:fi_fishid)
  #Df  LogLik Df  Chisq Pr(>Chisq)
1   8 -5414.7                     
2  10 -5414.3  2 0.7465     0.6885
```
Model m9 is the absolute best model. However, we want test the hypothesis that body size differences ebtween species relate to selection on reservoir parts. 

- Taking m4, are body size and depth related to reservoir use?
```
plot(allEffects(m6,xlevels=list(res_part=seq(0,3,length=2))),rug = FALSE)
```
![Res_part_use](/Plots/Res_part_use_1.png "Res_part_use")

![Res_part_use](/Plots/Res_part_use_2.png "Res_part_use")

![Res_part_use](/Plots/Res_part_use_3.png "Res_part_use")


- Taking 





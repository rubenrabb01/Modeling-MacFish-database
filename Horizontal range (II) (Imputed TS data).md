# Analysis of range of distances for horizontal movement using impu (II)

This section is a follow-up to the analysis of horizontal range data where the process for GAMM models building and selection of importance variables is about the same with minor differences regarding:
- The definition of smooths terms and surface interactions change with the addition of new variables:
  - Daily water temperature
  - Moon phase
  - Day length
- Weekly temporal trends are not accounted for by in the models

## 1. Classification of variables using Random Forest

With the inclusion of new variables we want to classify them based on their importance for which we use a ML recursive partitioning method such as Random Forest. The aims are:
- Estimate which variables have more weight on average on horizontal range values
- Rule out those variables with higher mean minimal depth (or less MSE increase) thoughout the tree forest

:books:`library(randomForest)`  
:books:`library(randomForestExplainer)`  
:books:`library(randomForestSRC)`  

### 1.1. Set the random seed to be reproducible

```
set.seed(500)
```

### 1.2. Create training and test sets

Split data according to random samples into 60% training and 40% test datasets
```
samp.size <- sample(nrow(data_longit_sub_complete), nrow(data_longit_sub_complete) * 0.60)
data_longit_sub_complete.train <- data_longit_sub_complete[samp.size, ]
data_longit_sub_complete.test <- data_longit_sub_complete[-samp.size, ]

# Alternatively, using the _floor_ and _seq_len_ functions to create training and test sets:
samp.size <- floor(0.60 * nrow(data_longit_sub_complete))
data_longit_sub_complete.train_ind <- sample(seq_len(nrow(data_longit_sub_complete)),size = samp.size)
data_longit_sub_complete.train <- data_longit_sub_complete[data_longit_sub_complete.train_ind, ]
data_longit_sub_complete.test <- data_longit_sub_complete[-data_longit_sub_complete.train_ind, ]
```

### 1.3. Grow regression tree forest

```
model.rf<-randomForest(sqrt(ranged2d+1) ~ body_size + day_length + day_temp + lunar_phase + Species + season + weekly + seasonally,
                                          data = data_longit_sub_complete.train,
                                          importance=TRUE,
                                          ntree=500, mtry=15, do.trace=100)
```

### 1.4. Summary of tree forest

```
print(model.rf)
```
```
Call:
 randomForest(formula = sqrt(ranged2d + 1) ~ body_size + day_length +      day_temp + lunar_phase + Species + season + weekly + seasonally,      data = data_longit_sub_complete.train, importance = TRUE,      ntree = 500, mtry = 15, do.trace = 100)
               Type of random forest: regression
                     Number of trees: 500
No. of variables tried at each split: 8

          Mean of squared residuals: 118.9338
                    % Var explained: 53.13
```
**Note** that if we included subject identity _Id_ in the tree model the % of variance explained would increase by ~8 %. However, provided that variation between individuals is expected to be high, and that _Id_ is a random-effects term, including that variable has confounding effects for variable classification

### 1.4.1. Variable importance

```
# plot(model.rf)
# varImpPlot(model.rf,sort = T, main="Variable Importance", n.var=8)
variable.imp <- data.frame(importance(model.rf))
variable.imp$Variables <- row.names(variable.imp)
```

Order variables from best to worse MSE
```
print(variable.imp[order(variable.imp$X.IncMSE ,decreasing = T),])
```
```
          X.IncMSE IncNodePurity   Variables
body_size   312.19231     529784.14   body_size
day_length  127.09721     307674.25  day_length
day_temp    106.92909     282790.59    day_temp
Species      57.92725      52254.30     Species
lunar_phase  49.21568     178007.22 lunar_phase
season       41.92512      60196.10      season
weekly       30.51111      83056.01      weekly
seasonally   25.83547      17640.16  seasonally
```

Order according to Node Purity
```
print(variable.imp[order(variable.imp$IncNodePurity ,decreasing = T),])
```
```
        X.IncMSE IncNodePurity   Variables
body_size   312.19231     529784.14   body_size
day_length  127.09721     307674.25  day_length
day_temp    106.92909     282790.59    day_temp
lunar_phase  49.21568     178007.22 lunar_phase
weekly       30.51111      83056.01      weekly
season       41.92512      60196.10      season
Species      57.92725      52254.30     Species
seasonally   25.83547      17640.16  seasonally
```

Create a data frame with various measures of importance of variables in the random forest
```
importance_frame <- measure_importance(model.rf)
save(importance_frame, file = "importance_frame.rda")
load("importance_frame.rda")
importance_frame
```
```
     variable mean_min_depth no_of_nodes mse_increase node_purity_increase no_of_trees times_a_root p_value
1   body_size          0.000      136027    243.02293            529784.14         500          500       0
2  day_length          1.962      188984    120.60953            307674.25         500            0       0
3    day_temp          1.946      187746    134.96650            282790.59         500            0       0
4 lunar_phase          3.482      194976     28.66061            178007.22         500            0       0
5      season          2.300       11439     64.88152             60196.10         500            0       1
6  seasonally          4.886       11271     10.62709             17640.16         500            0       1
7     Species          3.136       14426     52.34484             52254.30         500            0       1
8      weekly          4.048      135901     12.50093             83056.01         500            0       0
```

**Plot Relations between measures of importance**

```
plot_importance_ggpairs(importance_frame)  # or use model.rf (but takes longer)
```
![Horiz_range](/Plots/Horiz_range_II_imputed_0.png "Horiz_range")

**Plot Relations between rankings according to different measures**

```
plot_importance_rankings(importance_frame) # or use model.rf (but takes longer)
```
![Horiz_range](/Plots/Horiz_range_II_imputed_01.png "Horiz_range")

### 1.4.2. Multi-way importance plots

In the first plot variables are ranked according to the best scores in relation to three importance measures derived from the forest tree:
- Mean depth of first split on the variable
- Number of trees in which the root is split on the variable
- The total number of nodes in the forest that split on that variable

In the second multi-way importance plot the importance measures used take into account the predictive capability of a variable and wether it has a significant effect on the response based on its p-value

```
multi_way_imp_1 <- plot_multi_way_importance(importance_frame, size_measure = "no_of_nodes")   # or use model.rf (but takes longer)
multi_way_imp_2 <- plot_multi_way_importance(importance_frame, x_measure = "mse_increase", y_measure = "node_purity_increase", size_measure = "p_value", no_of_labels = 5)
grid.arrange(multi_way_imp_1, multi_way_imp_2, nrow = 1)
```
![Horiz_range](/Plots/Horiz_range_II_imputed_1.png "Horiz_range")

- We see that _body_size_ is ranked high and far from the remaining variables, which actually match the previous importance frame

### 1.4.3. Distribution of minimal depth

Calculate minimal depth for each variable among the forest trees
```
min_depth_frame <- min_depth_distribution(model.rf)
save(min_depth_frame, file = "min_depth_frame.rda")
load("min_depth_frame.rda")
head(min_depth_frame, n = 10)
```
```
   tree    variable minimal_depth
1     1   body_size             0
2     1  day_length             3
3     1    day_temp             3
4     1 lunar_phase             4
5     1      season             2
6     1  seasonally             4
7     1     Species             2
8     1      weekly             4
9     2   body_size             0
10    2  day_length             2
```

**Plot minimal depth distribution**

```
plot_min_depth_distribution(model.rf)
```
![Horiz_range](/Plots/Horiz_range_II_imputed_2.png "Horiz_range")

- We see that the mean of the distribution of minimal depth is lowest for body_size with zero trees in which that variable was used for splitting

### 1.4.4. Variable interactions

Create a data frame of potential interactions ordered from more to less important in terms of frequency in the tree foerest
```
(vars <- important_variables(importance_frame, k = 5, measures = c("mean_min_depth", "no_of_trees"))) # or use model.rf (but takes longer)
interactions_frame <- min_depth_interactions(model.rf, vars)
save(interactions_frame, file = "interactions_frame.rda")
load("interactions_frame.rda")
head(interactions_frame[order(interactions_frame$occurrences, decreasing = TRUE), ])
```

**Plot variable interactions**

In this plot:
- The 20 interactions that appeared most frequently are reported according to the mean of conditional minimal depth – a generalization of minimal depth that measures the depth of the second variable in a tree of which the first variable is a root (a subtree of a tree from the forest)
- The horizontal line shows the minimal value of the depicted statistic among interactions for which it was calculated
- The interactions considered are ones with the following variables as first (root variables): and all possible values of the second variable
```
plot_min_depth_interactions(interactions_frame)
```
![Horiz_range](/Plots/Horiz_range_II_imputed_3.png "Horiz_range")

As with non-imputed data the _body_size_x _day_length_ interaction appears with high frequency

### 1.4.5. Predict horizontal range

```
pred <- predict(model.rf, newdata = data_longit_sub_complete.test)
table(pred, data_longit_sub_complete.test$ranged2d)
data_longit_sub_complete.train$predicted.response <- predict(model.rf, data_longit_sub_complete.train)
print(importance(model.rf,type = 2))
```

**Plot interactions predictions**

We further explore the most frequent interactions by plotting predictions of the forest for each term in the interaction
```
p1 <- plot_predict_interaction(model.rf, grid = 1000, data_longit_sub_complete.train, "body_size", "day_length")
p2 <- plot_predict_interaction(model.rf, grid = 1000, data_longit_sub_complete.train, "body_size","day_temp")
p3 <- plot_predict_interaction(model.rf, grid = 1000, data_longit_sub_complete.train, "body_size","lunar_phase")
grid.arrange(p1, p2, p3, nrow = 1)
```
![Horiz_range](/Plots/Horiz_range_II_imputed_4.png "Horiz_range")

From the most frequent interaction _day_length x body_size_ there is to highlight that:
  - The predicted horizontal range is highest when body size is 700-800 cm and day length is inferior to 9
  - On the other hand, horizontal range is lowest when body size is smaller than 500 cm or between 1500-1600 almost independently of day length

### 1.4.6. Report random forest analysis

```
explain_forest(model.rf, interactions = TRUE, data = data_longit_sub_complete)
```
In the following link you can view a complete report of the [Random Forest output](http://172.21.3.20:8787/files/Teri_longit_move/Your_forest_explained.html)

## 2. Build GAMM models with uncorrelated errors and conduct model-selection

- Fit several models assuming all observations are independent
- Include smooth terms for _seasonally_ and _daily_ grouped by Species using rank 5 P-spline marginals (k=5)
- Add one random-term for subject identity
- Add an interaction between factors season and Species
- Based on previous classification of variable importance and interactions we include a _te_ term to create a tensor product smooth of three variables that are seemingly related in their effects on the response variable
- We include a tensor product for _day_length x body_size_ interaction (see interactions plot above)

### Fit GAMM models

:books:`library(mgcv)`

```
m_gam_complete_1 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + s(seasonally, bs = "cr", k = 5, by = Species) + s(day_temp) + te(seasonally,lunar_phase,day_length,bs="ps",k=5, by = Species)+ s(Id, bs = "re"), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_gam_complete_2 <- bam(sqrt(ranged2d+1) ~ s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(lunar_phase,day_length,bs="ps",k=5, by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_gam_complete_3 <- bam(sqrt(ranged2d+1) ~ s(day_length)  + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(day_temp,lunar_phase, seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_gam_complete_4 <- bam(sqrt(ranged2d+1) ~ s(Id, bs = "re") + s(body_size) + s(lunar_phase) + te(body_size, day_length, day_temp, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_gam_complete_5 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + s(seasonally, bs = "cr", k = 5, by = Species) + s(day_temp) + te(lunar_phase,day_length,day_temp,bs="ps",k=5, by = Species)+ s(Id, bs = "re"), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_gam_complete_6 <- bam(sqrt(ranged2d+1) ~ Species * season + s(Id, bs = "re") + s(body_size) + s(lunar_phase) + te(lunar_phase,day_temp,day_length,bs=c("tp","cr"), d=c(2,1), k=c(20,5), by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_gam_complete_7 <- bam(sqrt(ranged2d+1) ~ Species * season + s(Id, bs = "re") + s(lunar_phase) + te(lunar_phase,day_temp,seasonally,bs="ps",k=5, by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_gam_complete_8 <- bam(sqrt(ranged2d+1) ~ s(Id, bs = "re") + s(body_size) + s(lunar_phase) + te(body_size, day_length,bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_gam_complete_9 <- bam(sqrt(ranged2d+1) ~ s(Id, bs = "re") + s(body_size) + s(lunar_phase) + s(day_temp) + te(day_temp,as.numeric(daily), bs = "ps", k = 5, by = Species) + te(lunar_phase, as.numeric(daily), bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_gam_complete_10 <- bam(sqrt(ranged2d+1) ~ s(Id, bs = "re") + s(body_size) + s(lunar_phase) + s(day_temp) + te(lunar_phase,seasonally, bs = "ps", k = 5, by = Species) + te(day_temp, seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_gam_complete_11 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + s(day_temp) + te(lunar_phase,day_temp,day_length,bs="ps",k=5, by = Species)+ s(Id, bs = "re"), family = gaussian, data = data_longit_sub_complete, method = "REML")
m_gam_complete_12 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + s(day_temp, by = Species) + te(lunar_phase,day_temp,day_length,bs="ps",k=5, by = Species)+ s(Id, bs = "re"), family = gaussian, data = data_longit_sub_complete, method = "REML")
```

### Model-selection

:books:`library(MASS)`  
:books:`library(AICcmodavg)`  
:books:`library(lmtest)`  

```
Cand.mod <- list(m_gam_complete_1,m_gam_complete_2,m_gam_complete_3,m_gam_complete_4,m_gam_complete_5,m_gam_complete_6,m_gam_complete_7,m_gam_complete_8,m_gam_complete_9,m_gam_complete_10,m_gam_complete_11,m_gam_complete_12)

Cand.mod.glm <- lapply(Cand.mod, glm.convert)
aictab(cand.set = Cand.mod.glm, second.ord = TRUE)

```
```
Model selection based on AICc:

        K     AICc Delta_AICc AICcWt Cum.Wt        LL
Mod11 670 82529.16       0.00   0.66   0.66 -40549.59
Mod12 686 82531.19       2.03   0.24   0.90 -40532.36
Mod5  682 82533.05       3.89   0.10   1.00 -40537.85
Mod1  547 82619.09      89.93   0.00   1.00 -40732.91
Mod4  422 82647.66     118.50   0.00   1.00 -40884.40
Mod3  631 82814.49     285.33   0.00   1.00 -40736.49
Mod2  353 83063.96     534.81   0.00   1.00 -41166.86
Mod6  358 84771.33    2242.17   0.00   1.00 -42015.19
Mod9  191 84816.27    2287.11   0.00   1.00 -42213.63
Mod7  424 85031.97    2502.81   0.00   1.00 -42074.39
Mod8  122 85132.45    2603.29   0.00   1.00 -42442.80
Mod10 185 85136.98    2607.82   0.00   1.00 -42380.21
```

Compare the three best-fit models
```
lrtest(m_gam_complete_11,m_gam_complete_12)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + s(day_temp) + te(lunar_phase,
    day_length, day_temp, bs = "ps", k = 5, by = Species) + s(Id,
    bs = "re")
Model 2: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + s(day_temp, by = Species) + te(lunar_phase,
    day_length, day_temp, bs = "ps", k = 5, by = Species) + s(Id,
    bs = "re")
     #Df LogLik     Df  Chisq Pr(>Chisq)
1 278.63 -40955
2 282.18 -40953 3.5419 3.4201     0.4901
```
```
lrtest(m_gam_complete_11,m_gam_complete_5)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + s(day_temp) + te(lunar_phase,
    day_length, day_temp, bs = "ps", k = 5, by = Species) + s(Id,
    bs = "re")
Model 2: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + s(seasonally, bs = "cr", k = 5,
    by = Species) + s(day_temp) + te(lunar_phase, day_length,
    day_temp, bs = "ps", k = 5, by = Species) + s(Id, bs = "re")
     #Df LogLik     Df  Chisq Pr(>Chisq)
1 278.63 -40955
2 281.43 -40954 2.7963 2.1882     0.5343
```
- The first three models are nested and the best one is the reduced form, **m_gam_complete_11**, excluding both _Species x seasonally_ and _Species x day_temp_ (non-significant) interaction terms from other model formulas
- We see that the model with the tensor product _day_length_ by _body_size_ is not selected between the best models despite the interaction had high occurrence in the RF interactions frame

### Summary of best-fit model

```
summary(m_gam_complete_11)
```
```
Family: gaussian
Link function: identity

Formula:
sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + s(day_temp) + te(lunar_phase,
    day_length, day_temp, bs = "ps", k = 5, by = Species) + s(Id,
    bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)    25.41       1.16    21.9   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                                                         edf  Ref.df       F  p-value
s(body_size)                                          1.4385   1.443   3.240 0.099458 .
s(Species,Id)                                        20.2972  30.000 824.434  4.4e-06 ***
s(season,Id)                                         99.1369 130.000  74.411 0.000506 ***
s(day_temp)                                           3.4429   3.985   1.082 0.383485
te(lunar_phase,day_length,day_temp):Speciespike      55.3730  62.938   6.662  < 2e-16 ***
te(lunar_phase,day_length,day_temp):Speciespikeperch 40.8426  48.298   6.496  < 2e-16 ***
te(lunar_phase,day_length,day_temp):Specieswels      42.0415  50.044   4.812  < 2e-16 ***
s(Id)                                                 0.2206  30.000   0.270 0.441149
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Rank: 669/670
R-sq.(adj) =  0.488   Deviance explained = 50.1%
-REML =  41444  Scale est. = 130.08    n = 10664
```

We can see that:
- The interaction term is significant in the three species
- the smooth terms for _body_size_, _day_term_ and the random-effects _Id_ are not significant so we should compare best-fit model to a model without those terms

### Remove non-significant terms and compare with the larger model

```
m_gam_complete_11_drop <- bam(sqrt(ranged2d+1) ~ s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(lunar_phase,day_length,day_temp,bs="ps",k=5, by = Species), family = gaussian, data = data_longit_sub_complete, method = "REML")
```
```
lrtest(m_gam_complete_11,m_gam_complete_11_drop)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + s(day_temp) + te(lunar_phase,
    day_length, day_temp, bs = "ps", k = 5, by = Species) + s(Id,
    bs = "re")
Model 2: sqrt(ranged2d + 1) ~ s(Species, Id, bs = "re") + s(season, Id,
    bs = "re") + te(lunar_phase, day_length, day_temp, bs = "ps",
    k = 5, by = Species)
     #Df LogLik     Df Chisq Pr(>Chisq)
1 278.63 -40955
2 274.61 -40955 -4.029  1.36     0.8511
```

We see that dropping the non-significant smooth predictors doesnt change overall fit of the model (same r-squared) and the reduced model is preferred

### Summary of final selected model

View GCV and EDF estimates
```
summary(m_gam_complete_11_drop)$sp.criterion
```
```
   REML
41447.87
attr(,"Dp")
[1] 5321.001
```
```
summary(m_gam_complete_11_drop)$s.table
```
```
                                                          edf    Ref.df           F      p-value
s(Species,Id)                                        22.23064  31.00000 1286.469558 5.157310e-09
s(season,Id)                                         98.97489 130.00000  104.881546 1.512015e-03
te(lunar_phase,day_length,day_temp):Speciespike      56.23693  63.80825    8.732630 8.806355e-78
te(lunar_phase,day_length,day_temp):Speciespikeperch 41.32812  48.94071    6.924311 3.337553e-44
te(lunar_phase,day_length,day_temp):Specieswels      43.82333  51.91737    6.493693 1.533013e-42
```

**Summary table**

:books:`library(itsadug)`

```
gamtabs(m_gam_complete_11_drop, caption="Summary of m_gam_complete_sp2", comment=FALSE, type='html')
```
<table border=1>
<caption align="bottom"> Summary of m_gam_complete_sp2 </caption>
  <tr> <td> A. parametric coefficients </td> <td align="right"> Estimate </td> <td align="right"> Std. Error </td> <td align="right"> t-value </td> <td align="right"> p-value </td> </tr>
  <tr> <td> (Intercept) </td> <td align="right"> 25.4013 </td> <td align="right"> 1.2085 </td> <td align="right"> 21.0182 </td> <td align="right"> &lt; 0.0001 </td> </tr>
   <tr> <td> B. smooth terms </td> <td align="right"> edf </td> <td align="right"> Ref.df </td> <td align="right"> F-value </td> <td align="right"> p-value </td> </tr>
  <tr> <td> s(Species,Id) </td> <td align="right"> 22.2306 </td> <td align="right"> 31.0000 </td> <td align="right"> 1286.4696 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> s(season,Id) </td> <td align="right"> 98.9749 </td> <td align="right"> 130.0000 </td> <td align="right"> 104.8815 </td> <td align="right"> 0.0015 </td> </tr>
  <tr> <td> te(lunar_phase,day_length,day_temp):Speciespike </td> <td align="right"> 56.2369 </td> <td align="right"> 63.8083 </td> <td align="right"> 8.7326 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> te(lunar_phase,day_length,day_temp):Speciespikeperch </td> <td align="right"> 41.3281 </td> <td align="right"> 48.9407 </td> <td align="right"> 6.9243 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> te(lunar_phase,day_length,day_temp):Specieswels </td> <td align="right"> 43.8233 </td> <td align="right"> 51.9174 </td> <td align="right"> 6.4937 </td> <td align="right"> &lt; 0.0001 </td> </tr>
   <a name=tab.gam></a>
</table>

### Plot model

**Plot summed effects surfaces (smooth) with and without random effects**
```
par(mfrow=c(1,3), cex=1.1)
plot_smooth(m_gam_complete_11_drop, view="day_length", cond=list(Species="pike"), rug=FALSE, ylim=c(-10,80), print.summary=FALSE, ylab = "Horizontal range [m]", main='Day length, Species=pike')
plot_smooth(m_gam_complete_11_drop, view="day_length", cond=list(Species="pike"), rug=FALSE, add=TRUE, col='red', rm.ranef=TRUE, print.summary=FALSE, xpd=TRUE)
legend('bottomleft', legend=c("with R.E.","without R.E."), col=c("black", "red"), lwd=2, bty='n')
plot_smooth(m_gam_complete_11_drop, view="day_length", cond=list(Species="pikeperch"), rug=FALSE, ylim=c(-10,80), print.summary=FALSE, ylab = "Horizontal range [m]", main='Day length, Species=pikeperch')
plot_smooth(m_gam_complete_11_drop, view="day_length", cond=list(Species="pikeperch"), rug=FALSE, add=TRUE, col='red', rm.ranef=TRUE, print.summary=FALSE, xpd=TRUE)
legend('bottomleft', legend=c("with R.E.","without R.E."), col=c("black", "red"), lwd=2, bty='n')
plot_smooth(m_gam_complete_11_drop, view="day_length", cond=list(Species="wels"), rug=FALSE, ylim=c(-10,80), print.summary=FALSE, ylab = "Horizontal range [m]", main='Day length, Species=wels')
plot_smooth(m_gam_complete_11_drop, view="day_length", cond=list(Species="wels"), rug=FALSE, add=TRUE, col='red', rm.ranef=TRUE,  print.summary=FALSE, xpd=TRUE)
legend('bottomleft', legend=c("with R.E.","without R.E."), col=c("black", "red"), lwd=2, bty='n')
```
![Horiz_range](/Plots/Horiz_range_II_imputed_9.png "Horiz_range")

**Plot fitted model values (day length)**

```
data_m_gam_complete_11_drop <- visreg(m_gam_complete_11_drop, "day_length", by = "Species", breaks = c ("pike", "pikeperch", "wels"), gg = TRUE, overlay = TRUE, jitter = TRUE, lwd = 0.5, rug = FALSE, partial = FALSE, plot = FALSE)
plot(data_m_gam_complete_11_drop, plot.type="rgl") + theme_bw()
```
![Horiz_range](/Plots/Horiz_range_II_imputed_10.png "Horiz_range")

**Plot _day_length_ x _lunar_phase_ x _day_temp in _pike_, _pikeperch_ and _wels_**

```
plot(m_gam_complete_11_drop)
```
![Horiz_range](/Plots/Horiz_range_II_imputed_51.png "Horiz_range")

![Horiz_range](/Plots/Horiz_range_II_imputed_52.png "Horiz_range")

![Horiz_range](/Plots/Horiz_range_II_imputed_53.png "Horiz_range")

**Plot summed effects surfaces of _day_length_ by _lunar_phase_ smooths for the three species**
```
layout(matrix(1:6, nrow = 2))
vis.gam(m_gam_complete_11_drop, view=c("day_length","lunar_phase"), cond=list(Species='pike'), main = "pike", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(28,48), pos=.15)
vis.gam(m_gam_complete_11_drop, view=c("day_length","lunar_phase"), cond=list(Species='pike'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_gam_complete_11_drop, view=c("day_length","lunar_phase"), cond=list(Species='pikeperch'), main = "pikeperch", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(14,24), pos=.15)
vis.gam(m_gam_complete_11_drop, view=c("day_length","lunar_phase"), cond=list(Species='pikeperch'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_gam_complete_11_drop, view=c("day_length","lunar_phase"), cond=list(Species='wels'), main = "wels", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(28,35), pos=.15)
vis.gam(m_gam_complete_11_drop, view=c("day_length","lunar_phase"), cond=list(Species='wels'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
```
![Horiz_range](/Plots/Horiz_range_II_imputed_6.png "Horiz_range")

**Plot summed effects surfaces of _day_length_ by _day_temp_ smooths for the three species**

```
layout(matrix(1:6, nrow = 2))
vis.gam(m_gam_complete_11_drop, view=c("day_length","day_temp"), cond=list(Species='pike'), main = "pike", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(28,48), pos=.15)
vis.gam(m_gam_complete_11_drop, view=c("day_length","day_temp"), cond=list(Species='pike'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_gam_complete_11_drop, view=c("day_length","day_temp"), cond=list(Species='pikeperch'), main = "pikeperch", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(14,24), pos=.15)
vis.gam(m_gam_complete_11_drop, view=c("day_length","day_temp"), cond=list(Species='pikeperch'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_gam_complete_11_drop, view=c("day_length","day_temp"), cond=list(Species='wels'), main = "wels", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(28,35), pos=.15)
vis.gam(m_gam_complete_11_drop, view=c("day_length","day_temp"), cond=list(Species='wels'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
```
![Horiz_range](/Plots/Horiz_range_II_imputed_7.png "Horiz_range")

We see that:
- In _pike_, the highest peak occurs when _day_length_ is above 16 and lunar phase 3-6
- In _pikeperch_, the highest peak occurs  when _day_length_ is 16 and lunar phase 5-6
- In _wels_, the highest peak occurs  when _day_length_ is above 16 and lunar phase 2-4

**Plot surface and one-dimensional _day_length_ by _lunar_phase_ and _day_temp_ differences in horizontal range between the three species**
```
layout(matrix(1:9, nrow = 3))
plot_diff2(m_gam_complete_11_drop, view=c("day_length","lunar_phase"), comp=list(Species=c("pike", "pikeperch")), main='Day length by lunar phase difference pike-pikeperch',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff2(m_gam_complete_11_drop, view=c("day_length","day_temp"), comp=list(Species=c("pike", "pikeperch")), main='Day length by water temperature difference pike-pikeperch',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_gam_complete_11_drop, view="day_length", comp=list(Species=c("pike", "pikeperch")), main='Day length difference pike-pikeperch')
plot_diff2(m_gam_complete_11_drop, view=c("day_length","lunar_phase"), comp=list(Species=c("pikeperch", "wels")), main='Day length by lunar phase difference pikeperch-wels',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff2(m_gam_complete_11_drop, view=c("day_length","day_temp"), comp=list(Species=c("pikeperch", "wels")), main='Day length by water temperature difference pikeperch-wels',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_gam_complete_11_drop, view="day_length", comp=list(Species=c("pikeperch", "wels")), main='Day length difference pikeperch-wels')
plot_diff2(m_gam_complete_11_drop, view=c("day_length","lunar_phase"), comp=list(Species=c("pike", "wels")), main='Day length by lunar phase difference pike-wels',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff2(m_gam_complete_11_drop, view=c("day_length","day_temp"), comp=list(Species=c("pike", "wels")), main='Day length by water temperature difference pike-wels',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_gam_complete_11_drop, view="day_length", comp=list(Species=c("pike", "wels")), main='Day length difference pike-wels')
```
![Horiz_range](/Plots/Horiz_range_II_imputed_8.png "Horiz_range")


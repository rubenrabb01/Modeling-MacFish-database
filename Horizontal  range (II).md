# Analysis of range of distances for horizontal movement (II)

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
samp.size <- sample(nrow(data_longit_sub), nrow(data_longit_sub) * 0.60)
data_longit_sub.train <- data_longit_sub[samp.size, ]
data_longit_sub.test <- data_longit_sub[-samp.size, ]

# Alternatively, using the _floor_ and _seq_len_ functions to create training and test sets:
samp.size <- floor(0.60 * nrow(data_longit_sub))
data_longit_sub.train_ind <- sample(seq_len(nrow(data_longit_sub)),size = samp.size)
data_longit_sub.train <- data_longit_sub[data_longit_sub.train_ind, ]
data_longit_sub.test <- data_longit_sub[-data_longit_sub.train_ind, ]
```

### 1.3. Grow regression tree forest

```
model.rf<-randomForest(sqrt(ranged2d+1) ~ body_size + day_length + day_temp + lunar_phase + Species + season + weekly + seasonally,
                                          data = data_longit_sub.train,
                                          importance=TRUE,
                                          ntree=500, mtry=15, do.trace=100)
```

### 1.4. Summary of tree forest

```
print(model.rf)

Call:
 randomForest(formula = sqrt(ranged2d + 1) ~ body_size + day_length + day_temp + lunar_phase + Species + season + weekly + seasonally, data = data_longit_sub.train, importance = TRUE, ntree = 500, mtry = 15, do.trace = 100)
               Type of random forest: regression
                     Number of trees: 500
No. of variables tried at each split: 8

          Mean of squared residuals: 138.7859
                    % Var explained: 41.79
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
body_size   278.64809     460904.91   body_size
day_temp    112.81204     300561.98    day_temp
day_length  108.10442     287815.93  day_length
Species      58.96291      51158.85     Species
season       58.06306      52670.37      season
lunar_phase  36.85303     188701.82 lunar_phase
seasonally   23.48321      14506.37  seasonally
weekly      -15.19021      89594.85      weekly
```

Order according to Node Purity
```
print(variable.imp[order(variable.imp$IncNodePurity ,decreasing = T),])
```
```
             X.IncMSE IncNodePurity   Variables
body_size   278.64809     460904.91   body_size
day_temp    112.81204     300561.98    day_temp
day_length  108.10442     287815.93  day_length
lunar_phase  36.85303     188701.82 lunar_phase
weekly      -15.19021      89594.85      weekly
season       58.06306      52670.37      season
Species      58.96291      51158.85     Species
seasonally   23.48321      14506.37  seasonally
```

Create a data frame with various measures of importance of variables in the random forest
```
importance_frame <- measure_importance(model.rf)
save(importance_frame, file = "importance_frame.rda")
load("importance_frame.rda")
importance_frame
```
```
     variable mean_min_depth no_of_nodes mse_increase node_purity_increase no_of_trees times_a_root   p_value
1   body_size          0.000      130228   205.610949            460904.91         500          500 0.9998216
2  day_length          2.138      234528   105.880126            287815.93         500            0 0.0000000
3    day_temp          2.970      230287   110.109965            300561.98         500            0 0.0000000
4 lunar_phase          3.714      244839    13.388058            188701.82         500            0 0.0000000
5      season          1.970       13851    47.948352             52670.37         500            0 1.0000000
6  seasonally          4.968       13611     7.550513             14506.37         500            0 1.0000000
7     Species          3.610       12784    47.175310             51158.85         500            0 1.0000000
8      weekly          4.658      171366    -3.895798             89594.85         500            0 0.0000000
```

**Plot Relations between measures of importance**

```
plot_importance_ggpairs(importance_frame)  # or use model.rf (but takes longer)
```
![Horiz_range](/Plots/Horiz_range_II_0.png "Horiz_range")

**Plot Relations between rankings according to different measures**

```
plot_importance_rankings(importance_frame) # or use model.rf (but takes longer)
```
![Horiz_range](/Plots/Horiz_range_II_01.png "Horiz_range")

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
![Horiz_range](/Plots/Horiz_range_II_1.png "Horiz_range")

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
![Horiz_range](/Plots/Horiz_range_II_2.png "Horiz_range")

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
![Horiz_range](/Plots/Horiz_range_II_3.png "Horiz_range")

It is worth highlighting the occurrence of the _body_size_x _day_length_ interaction which appears with high frquency across the tree forest

### 1.4.5. Predict horizontal range

```
pred <- predict(model.rf, newdata = data_longit_sub.test)
table(pred, data_longit_sub.test$ranged2d)
data_longit_sub.train$predicted.response <- predict(model.rf, data_longit_sub.train)
print(importance(model.rf,type = 2))
```

**Plot interactions predictions**

We further explore the most frequent interactions by plotting predictions of the forest for each term in the interaction
```
p1 <- plot_predict_interaction(model.rf, grid = 1000, data_longit_sub.train, "body_size", "day_length")
p2 <- plot_predict_interaction(model.rf, grid = 1000, data_longit_sub.train, "body_size","day_temp")
p3 <- plot_predict_interaction(model.rf, grid = 1000, data_longit_sub.train, "body_size","lunar_phase")
grid.arrange(p1, p2, p3, nrow = 1)
```
![Horiz_range](/Plots/Horiz_range_II_4.png "Horiz_range")

From the most frequent interaction _day_length x body_size_ we need to highlight that:
  - The predicted horizontal range is highest when body size is 700-800 cm and day length is inferior to 9
  - On the other hand, horizontal range is lowest when body size is smaller than 500 cm or between 1500-1600 almost independently of day length

### 1.4.6. Report random forest analysis

```
explain_forest(model.rf, interactions = TRUE, data = data_longit_sub)
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

```
m_gam_1 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + s(seasonally, bs = "cr", k = 5, by = Species) + s(day_temp) + te(seasonally,lunar_phase,day_length,bs="ps",k=5, by = Species)+ s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_2 <- bam(sqrt(ranged2d+1) ~ s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(lunar_phase,day_length,bs="ps",k=5, by = Species) + s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_3 <- bam(sqrt(ranged2d+1) ~ s(day_length)  + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(day_temp,lunar_phase, seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_4 <- bam(sqrt(ranged2d+1) ~ s(Id, bs = "re") + s(body_size) + s(lunar_phase) + te(body_size, day_length, day_temp, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_5 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + s(seasonally, bs = "cr", k = 5, by = Species) + s(day_temp) + te(lunar_phase,day_length,day_temp,bs="ps",k=5, by = Species)+ s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_6 <- bam(sqrt(ranged2d+1) ~ Species * season + s(Id, bs = "re") + s(body_size) + s(lunar_phase) + te(lunar_phase,day_temp,day_length,bs=c("tp","cr"), d=c(2,1), k=c(20,5), by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_7 <- bam(sqrt(ranged2d+1) ~ Species * season + s(Id, bs = "re") + s(lunar_phase) + te(lunar_phase,day_temp,seasonally,bs="ps",k=5, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_8 <- bam(sqrt(ranged2d+1) ~ s(Id, bs = "re") + s(body_size) + s(lunar_phase) + te(body_size, day_length,bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_9 <- bam(sqrt(ranged2d+1) ~ s(Id, bs = "re") + s(body_size) + s(lunar_phase) + s(day_temp) + te(day_temp,as.numeric(daily), bs = "ps", k = 5, by = Species) + te(lunar_phase, as.numeric(daily), bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_10 <- bam(sqrt(ranged2d+1) ~ s(Id, bs = "re") + s(body_size) + s(lunar_phase) + s(day_temp) + te(lunar_phase,seasonally, bs = "ps", k = 5, by = Species) + te(day_temp, seasonally, bs = "ps", k = 5, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_11 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + s(day_temp) + te(lunar_phase,day_temp,day_length,bs="ps",k=5, by = Species)+ s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
m_gam_12 <- bam(sqrt(ranged2d+1) ~ s(body_size) + s(Species, Id, bs = 're') + s(season, Id, bs = 're') + s(day_temp, by = Species) + te(lunar_phase,day_temp,day_length,bs="ps",k=5, by = Species)+ s(Id, bs = "re"), family = gaussian, data = data_longit_sub, method = "REML")
```

### Model-selection

:books:`library(MASS)`  
:books:`library(AICcmodavg)`  
:books:`library(lmtest)`  

```
Cand.mod <- list(m_gam_1,m_gam_2,m_gam_3,m_gam_4,m_gam_5,m_gam_6,m_gam_7,m_gam_8,m_gam_9,m_gam_10,m_gam_11,m_gam_12)

Cand.mod.glm <- lapply(Cand.mod, glm.convert)
aictab(cand.set = Cand.mod.glm, second.ord = TRUE)
```
```
Model selection based on AICc:

        K     AICc Delta_AICc AICcWt Cum.Wt        LL
Mod11 670 69550.33       0.00   0.69   0.69 -34050.17
Mod5  682 69552.24       1.91   0.26   0.95 -34037.06
Mod12 686 69555.56       5.23   0.05   1.00 -34034.02
Mod1  548 69586.68      36.35   0.00   1.00 -34209.08
Mod3  631 69648.34      98.01   0.00   1.00 -34144.62
Mod4  422 69754.13     203.80   0.00   1.00 -34433.87
Mod2  353 69758.15     207.82   0.00   1.00 -34511.36
Mod8  122 70999.16    1448.83   0.00   1.00 -35375.86
Mod6  358 71086.27    1535.94   0.00   1.00 -35169.99
Mod9  191 71118.71    1568.38   0.00   1.00 -35364.12
Mod7  424 71185.63    1635.30   0.00   1.00 -35147.42
Mod10 185 71189.88    1639.55   0.00   1.00 -35405.97
```

Compare the three best-fit models
```
lrtest(m_gam_11,m_gam_5)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + s(day_temp) + te(lunar_phase,
    day_temp, day_length, bs = "ps", k = 5, by = Species) + s(Id,
    bs = "re")
Model 2: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + s(seasonally, bs = "cr", k = 5,
    by = Species) + s(day_temp) + te(lunar_phase, day_length,
    day_temp, bs = "ps", k = 5, by = Species) + s(Id, bs = "re")
     #Df LogLik      Df Chisq Pr(>Chisq)
1 248.01 -34501
2 229.28 -34498 -18.722   6.3     0.9971
```
```
lrtest(m_gam_11,m_gam_12)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + s(day_temp) + te(lunar_phase,
    day_temp, day_length, bs = "ps", k = 5, by = Species) + s(Id,
    bs = "re")
Model 2: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + s(day_temp, by = Species) + te(lunar_phase,
    day_temp, day_length, bs = "ps", k = 5, by = Species) + s(Id,
    bs = "re")
     #Df LogLik      Df  Chisq Pr(>Chisq)
1 248.01 -34501
2 242.97 -34500 -5.0415 0.7669     0.9791
```
- The first three models are nested and the best one is the reduced form, **m_gam_11**, excluding both _Species x seasonally_ and _Species x day_temp_ (non-significant) interaction terms from other model formulas
- We see that the model with the tensor product _day_length_ by _body_size_ is not selected between the best models despite the interaction had high occurrence in the RF interactions frame

### Summary of best-fit model

```
summary(m_gam_11)
```
```
Family: gaussian
Link function: identity

Formula:
sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + s(day_temp) + te(lunar_phase,
    day_temp, day_length, bs = "ps", k = 5, by = Species) + s(Id,
    bs = "re")

Parametric coefficients:
            Estimate Std. Error t value Pr(>|t|)
(Intercept)   26.263      1.125   23.34   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Approximate significance of smooth terms:
                                                        edf  Ref.df       F  p-value
s(body_size)                                          1.634   1.641   3.573  0.08853 .
s(Species,Id)                                        15.202  29.000 516.050  0.00515 **
s(season,Id)                                         97.128 129.000  69.024  0.00082 ***
s(day_temp)                                           1.221   1.299   1.716  0.18823
te(lunar_phase,day_temp,day_length):Speciespike      31.999  39.314   2.391 4.02e-06 ***
te(lunar_phase,day_temp,day_length):Speciespikeperch 34.546  41.528   4.154  < 2e-16 ***
te(lunar_phase,day_temp,day_length):Specieswels      31.016  38.115   5.337  < 2e-16 ***
s(Id)                                                 4.758  29.000  86.139  0.31791
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Rank: 669/670
R-sq.(adj) =  0.387   Deviance explained = 40.2%
-REML =  34848  Scale est. = 146.56    n = 8846
```

We can see that:
- The interaction term is significant in the three species
- the smooth terms for _body_size_, _day_term_ and the random-effects _Id_ are not significant so we should compare best-fit model to a model without those terms

### Remove non-significant terms and compare with the larger model

```
m_gam_11_drop <- bam(sqrt(ranged2d+1) ~ s(Species, Id, bs = 're') + s(season, Id, bs = 're') + te(lunar_phase,day_length,day_temp,bs="ps",k=5, by = Species), family = gaussian, data = data_longit_sub, method = "REML")
```
```
lrtest(m_gam_11,m_gam_11_drop)
```
```
Likelihood ratio test

Model 1: sqrt(ranged2d + 1) ~ s(body_size) + s(Species, Id, bs = "re") +
    s(season, Id, bs = "re") + s(day_temp) + te(lunar_phase,
    day_temp, day_length, bs = "ps", k = 5, by = Species) + s(Id,
    bs = "re")
Model 2: sqrt(ranged2d + 1) ~ s(Species, Id, bs = "re") + s(season, Id,
    bs = "re") + te(lunar_phase, day_length, day_temp, bs = "ps",
    k = 5, by = Species)
     #Df LogLik      Df  Chisq Pr(>Chisq)
1 248.01 -34501
2 233.18 -34501 -14.827 1.0917          1
```

We see that dropping the non-significant smooth predictors doesnt change overall fit of the model (same r-squared) and the reduced model is preferred

### Summary of final selected model

View GCV and EDF estimates
```
summary(m_gam_11_drop)$sp.criterion
```
```
   REML
34851.53
attr(,"Dp")
[1] 4411.99
```
```
summary(m_gam_11_drop)$s.table
```
```
                                                          edf    Ref.df           F      p-value
s(Species,Id)                                        22.25455  30.00000 1117.316952 2.264447e-12
s(season,Id)                                         96.49780 130.00000   92.788212 2.121805e-03
te(lunar_phase,day_length,day_temp):Speciespike      32.00720  39.33682    3.989553 1.615793e-15
te(lunar_phase,day_length,day_temp):Speciespikeperch 34.49028  41.47867    4.362169 6.664108e-19
te(lunar_phase,day_length,day_temp):Specieswels      32.06779  39.19729    6.619267 1.164325e-33
```

**Summary table**

:books:`library(itsadug)`    

```
gamtabs(m_gam_11_drop, caption="Summary of m_gam_11_drop", comment=FALSE, type='html')
```
<table border=1>
<caption align="bottom"> Summary of m_gam_11_drop </caption>
  <tr> <td> A. parametric coefficients </td> <td align="right"> Estimate </td> <td align="right"> Std. Error </td> <td align="right"> t-value </td> <td align="right"> p-value </td> </tr>
  <tr> <td> (Intercept) </td> <td align="right"> 26.1539 </td> <td align="right"> 1.2034 </td> <td align="right"> 21.7331 </td> <td align="right"> &lt; 0.0001 </td> </tr>
   <tr> <td> B. smooth terms </td> <td align="right"> edf </td> <td align="right"> Ref.df </td> <td align="right"> F-value </td> <td align="right"> p-value </td> </tr>
  <tr> <td> s(Species,Id) </td> <td align="right"> 22.2546 </td> <td align="right"> 30.0000 </td> <td align="right"> 1117.3170 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> s(season,Id) </td> <td align="right"> 96.4978 </td> <td align="right"> 130.0000 </td> <td align="right"> 92.7882 </td> <td align="right"> 0.0021 </td> </tr>
  <tr> <td> te(lunar_phase,day_length,day_temp):Speciespike </td> <td align="right"> 32.0072 </td> <td align="right"> 39.3368 </td> <td align="right"> 3.9896 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> te(lunar_phase,day_length,day_temp):Speciespikeperch </td> <td align="right"> 34.4903 </td> <td align="right"> 41.4787 </td> <td align="right"> 4.3622 </td> <td align="right"> &lt; 0.0001 </td> </tr>
  <tr> <td> te(lunar_phase,day_length,day_temp):Specieswels </td> <td align="right"> 32.0678 </td> <td align="right"> 39.1973 </td> <td align="right"> 6.6193 </td> <td align="right"> &lt; 0.0001 </td> </tr>
   <a name=tab.gam></a>
</table>

### Plot model

**Plot summed effects surfaces (smooth) with and without random effects**
```
par(mfrow=c(1,3), cex=1.1)
plot_smooth(m_gam_11_drop, view="day_length", cond=list(Species="pike"), rug=FALSE, ylim=c(-10,80), print.summary=FALSE, ylab = "Horizontal range [m]", main='Day length, Species=pike')
plot_smooth(m_gam_11_drop, view="day_length", cond=list(Species="pike"), rug=FALSE, add=TRUE, col='red', rm.ranef=TRUE, print.summary=FALSE, xpd=TRUE)
legend('bottomleft', legend=c("with R.E.","without R.E."), col=c("black", "red"), lwd=2, bty='n')
plot_smooth(m_gam_11_drop, view="day_length", cond=list(Species="pikeperch"), rug=FALSE, ylim=c(-10,80), print.summary=FALSE, ylab = "Horizontal range [m]", main='Day length, Species=pikeperch')
plot_smooth(m_gam_11_drop, view="day_length", cond=list(Species="pikeperch"), rug=FALSE, add=TRUE, col='red', rm.ranef=TRUE, print.summary=FALSE, xpd=TRUE)
legend('bottomleft', legend=c("with R.E.","without R.E."), col=c("black", "red"), lwd=2, bty='n')
plot_smooth(m_gam_11_drop, view="day_length", cond=list(Species="wels"), rug=FALSE, ylim=c(-10,80), print.summary=FALSE, ylab = "Horizontal range [m]", main='Day length, Species=wels')
plot_smooth(m_gam_11_drop, view="day_length", cond=list(Species="wels"), rug=FALSE, add=TRUE, col='red', rm.ranef=TRUE,  print.summary=FALSE, xpd=TRUE)
legend('bottomleft', legend=c("with R.E.","without R.E."), col=c("black", "red"), lwd=2, bty='n')
```
![Horiz_range](/Plots/Horiz_range_II_5.png "Horiz_range")

**Plot fitted model values (day length)**
```
data_m_gam_11_drop <- visreg(m_gam_11_drop, "day_length", by = "Species", breaks = c ("pike", "pikeperch", "wels"), gg = TRUE, overlay = TRUE, jitter = TRUE, lwd = 0.5, rug = FALSE, partial = FALSE, plot = FALSE)
plot(data_m_gam_11_drop, plot.type="rgl") + theme_bw()
```
![Horiz_range](/Plots/Horiz_range_II_6.png "Horiz_range")

**Plot _day_length_ x _lunar_phase_ x _day_temp in _pike_, _pikeperch_ and _wels_**
```
plot(m_gam_11_drop)
```
![Horiz_range](/Plots/Horiz_range_II_61.png "Horiz_range")

![Horiz_range](/Plots/Horiz_range_II_62.png "Horiz_range")

![Horiz_range](/Plots/Horiz_range_II_63.png "Horiz_range")

**Plot summed effects surfaces of _day_length_ by _lunar_phase_ smooths for the three species**
```
layout(matrix(1:6, nrow = 2))
vis.gam(m_gam_11_drop, view=c("day_length","lunar_phase"), cond=list(Species='pike'), main = "pike", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(28,48), pos=.15)
vis.gam(m_gam_11_drop, view=c("day_length","lunar_phase"), cond=list(Species='pike'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_gam_11_drop, view=c("day_length","lunar_phase"), cond=list(Species='pikeperch'), main = "pikeperch", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(14,24), pos=.15)
vis.gam(m_gam_11_drop, view=c("day_length","lunar_phase"), cond=list(Species='pikeperch'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_gam_11_drop, view=c("day_length","lunar_phase"), cond=list(Species='wels'), main = "wels", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(28,35), pos=.15)
vis.gam(m_gam_11_drop, view=c("day_length","lunar_phase"), cond=list(Species='wels'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
```
![Horiz_range](/Plots/Horiz_range_II_7.png "Horiz_range")

**Plot summed effects surfaces of _day_length_ by _day_temp_ smooths for the three species**
```
layout(matrix(1:6, nrow = 2))
vis.gam(m_gam_11_drop, view=c("day_length","day_temp"), cond=list(Species='pike'), main = "pike", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(28,48), pos=.15)
vis.gam(m_gam_11_drop, view=c("day_length","day_temp"), cond=list(Species='pike'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_gam_11_drop, view=c("day_length","day_temp"), cond=list(Species='pikeperch'), main = "pikeperch", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(14,24), pos=.15)
vis.gam(m_gam_11_drop, view=c("day_length","day_temp"), cond=list(Species='pikeperch'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
vis.gam(m_gam_11_drop, view=c("day_length","day_temp"), cond=list(Species='wels'), main = "wels", n.grid = 450, plot.type = "contour", color = "topo", contour.col = "black", lwd = 2)
gradientLegend(valRange=c(28,35), pos=.15)
vis.gam(m_gam_11_drop, view=c("day_length","day_temp"), cond=list(Species='wels'), plot.type="rgl", ticktype="detailed",color="topo", n.grid = 50, theta=-35, zlab = "Horizontal range [m]")
```
![Horiz_range](/Plots/Horiz_range_II_8.png "Horiz_range")

We see that:
- In _pike_, the highest peak occurs when _day_length_ is above 16 and lunar phase 3-6
- In _pikeperch_, the highest peak occurs  when _day_length_ is 16 and lunar phase 5-6
- In _wels_, the highest peak occurs  when _day_length_ is above 16 and lunar phase 2-4

**Plot surface and one-dimensional _day_length_ by _lunar_phase_ and _day_temp_ differences in horizontal range between the three species**
```
layout(matrix(1:9, nrow = 3))
plot_diff2(m_gam_11_drop, view=c("day_length","lunar_phase"), comp=list(Species=c("pike", "pikeperch")), main='Day length by lunar phase difference pike-pikeperch',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff2(m_gam_11_drop, view=c("day_length","day_temp"), comp=list(Species=c("pike", "pikeperch")), main='Day length by water temperature difference pike-pikeperch',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_gam_11_drop, view="day_length", comp=list(Species=c("pike", "pikeperch")), main='Day length difference pike-pikeperch')
plot_diff2(m_gam_11_drop, view=c("day_length","lunar_phase"), comp=list(Species=c("pikeperch", "wels")), main='Day length by lunar phase difference pikeperch-wels',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff2(m_gam_11_drop, view=c("day_length","day_temp"), comp=list(Species=c("pikeperch", "wels")), main='Day length by water temperature difference pikeperch-wels',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_gam_11_drop, view="day_length", comp=list(Species=c("pikeperch", "wels")), main='Day length difference pikeperch-wels')
plot_diff2(m_gam_11_drop, view=c("day_length","lunar_phase"), comp=list(Species=c("pike", "wels")), main='Day length by lunar phase difference pike-wels',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff2(m_gam_11_drop, view=c("day_length","day_temp"), comp=list(Species=c("pike", "wels")), main='Day length by water temperature difference pike-wels',
        transform.view = TRUE,
        color = "heat",
        n.grid = 420,
        print.summary=FALSE)
plot_diff(m_gam_11_drop, view="day_length", comp=list(Species=c("pike", "wels")), main='Day length difference pike-wels')
```
![Horiz_range](/Plots/Horiz_range_II_9.png "Horiz_range")

### Bayesian mixed-effects model fitted with stan 

`m2<-stan_lmer(formula= ranged2d ~ 1 + season*fi_species +(1 | fi_fishid), data = mean.ranged2d, seed=350)`

### Results for m2 fit

`summary(m2)`

```{r, echo=FALSE, eval=TRUE}

Model Info:

 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + season * fi_species + (1 | fi_fishid)
 algorithm:    sampling
 priors:       see help('prior_summary')
 sample:       4000 (posterior sample size)
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
                                           mean     sd       2.5%     25%      50%      75%      97.5% 
(Intercept)                                  26.6      2.2     22.1     25.1     26.5     28.0     31.1
seasonspring_I                               -1.8      0.8     -3.5     -2.4     -1.8     -1.2     -0.1
seasonspring_II                              14.6      1.7     11.4     13.5     14.6     15.7     18.1
seasonsummer                                 -3.3      0.7     -4.7     -3.7     -3.3     -2.8     -1.9
seasonwinter                                  3.7      0.8      2.2      3.2      3.7      4.2      5.2
fi_speciespikeperch                          -7.6      3.4    -14.3     -9.7     -7.7     -5.5     -1.0
fi_specieswels                                0.2      3.0     -5.9     -1.8      0.2      2.2      6.1
seasonspring_I:fi_speciespikeperch            7.6      1.3      5.0      6.7      7.6      8.4     10.1
seasonspring_II:fi_speciespikeperch         -13.7      2.5    -18.6    -15.3    -13.6    -12.0     -8.9
seasonsummer:fi_speciespikeperch             13.0      1.1     10.8     12.2     13.0     13.7     15.1
seasonwinter:fi_speciespikeperch             -3.5      1.2     -6.0     -4.3     -3.5     -2.7     -1.1
seasonspring_I:fi_specieswels                 6.8      1.1      4.8      6.1      6.8      7.5      8.9
seasonspring_II:fi_specieswels              -10.2      2.2    -14.5    -11.7    -10.2     -8.7     -6.1
seasonsummer:fi_specieswels                   4.3      0.9      2.6      3.7      4.3      4.9      6.1
seasonwinter:fi_specieswels                  -2.9      1.0     -4.8     -3.6     -2.9     -2.2     -1.0
b[(Intercept) fi_fishid:T449202_1]           -1.0      2.7     -6.4     -2.7     -0.9      0.7      4.2
b[(Intercept) fi_fishid:T449203_1]            6.1      2.3      1.7      4.6      6.1      7.7     10.7
b[(Intercept) fi_fishid:T449204_1]           -2.1      2.5     -6.9     -3.8     -2.1     -0.5      2.9
b[(Intercept) fi_fishid:T449207_1]           -8.2      2.3    -12.8     -9.7     -8.2     -6.7     -3.7
b[(Intercept) fi_fishid:T449208_1]          -10.0      2.7    -15.6    -11.7     -9.9     -8.3     -4.8
b[(Intercept) fi_fishid:T449209_1]           -0.5      2.3     -5.0     -1.9     -0.5      1.1      4.0
b[(Intercept) fi_fishid:T449213_1]           10.4      2.7      5.0      8.7     10.5     12.2     15.8
b[(Intercept) fi_fishid:T449215_1]            6.4      2.7      0.9      4.7      6.5      8.2     11.8
b[(Intercept) fi_fishid:T449268_1]            6.7      1.9      2.8      5.4      6.7      8.0     10.4
b[(Intercept) fi_fishid:T449269_1]            4.8      2.0      1.0      3.5      4.8      6.0      8.6
b[(Intercept) fi_fishid:T449270_2]            5.5      2.4      0.7      3.9      5.5      7.0     10.1
b[(Intercept) fi_fishid:T449271_1]            0.3      2.3     -4.3     -1.2      0.3      1.8      4.8
b[(Intercept) fi_fishid:T449272_1]           11.3      2.3      6.7      9.8     11.3     12.8     15.8
b[(Intercept) fi_fishid:T449274_1]            7.5      1.9      3.6      6.2      7.5      8.8     11.1
b[(Intercept) fi_fishid:T449275_1]            6.1      1.9      2.3      4.8      6.1      7.4      9.9
b[(Intercept) fi_fishid:T449276_1]            1.8      1.9     -2.1      0.5      1.9      3.1      5.6
b[(Intercept) fi_fishid:T449277_1]            0.5      2.3     -4.0     -1.0      0.6      2.0      5.2
b[(Intercept) fi_fishid:T449278_1]           -9.8      2.0    -13.7    -11.1     -9.8     -8.4     -6.1
b[(Intercept) fi_fishid:T449280_1]           -2.4      2.0     -6.3     -3.8     -2.5     -1.1      1.3
b[(Intercept) fi_fishid:T449282_1]          -15.0      2.0    -18.9    -16.4    -15.0    -13.7    -11.2
b[(Intercept) fi_fishid:T449284_1]           -5.7      1.9     -9.6     -7.0     -5.7     -4.4     -2.0
b[(Intercept) fi_fishid:T449285_1]           -6.0      2.4    -10.9     -7.6     -6.0     -4.4     -1.3
b[(Intercept) fi_fishid:T449286_1]           -1.5      2.0     -5.4     -2.8     -1.5     -0.1      2.3
b[(Intercept) fi_fishid:T449287_1]           -7.0      2.3    -11.6     -8.5     -7.0     -5.5     -2.5
b[(Intercept) fi_fishid:T449288_1]            3.5      2.0     -0.4      2.1      3.5      4.8      7.4
b[(Intercept) fi_fishid:T449310_1]            1.1      2.7     -4.2     -0.6      1.2      2.9      6.4
b[(Intercept) fi_fishid:T449313_1]           -0.8      2.6     -6.1     -2.5     -0.6      0.9      4.5
b[(Intercept) fi_fishid:T449314_1]           -1.3      2.0     -5.3     -2.6     -1.3      0.0      2.5
b[(Intercept) fi_fishid:T449317_1]           -6.1      2.7    -11.7     -7.9     -6.0     -4.4     -0.8
b[(Intercept) fi_fishid:T449318_1]            1.3      2.0     -2.5      0.0      1.4      2.6      5.1
b[(Intercept) fi_fishid:T449319_1]            4.3      1.9      0.6      3.0      4.3      5.6      8.0
sigma                                        13.7      0.1     13.5     13.6     13.7     13.7     13.9
Sigma[fi_fishid:(Intercept),(Intercept)]     48.1     14.2     28.0     38.1     45.5     55.3     82.6
mean_PPD                                     26.9      0.2     26.5     26.8     26.9     27.1     27.3
log-posterior                            -35748.5      6.3 -35761.6 -35752.7 -35748.3 -35744.1 -35736.9

Diagnostics:
                                         mcse Rhat n_eff
(Intercept)                              0.1  1.0   663 
seasonspring_I                           0.0  1.0  1943 
seasonspring_II                          0.0  1.0  2236 
seasonsummer                             0.0  1.0  1970 
seasonwinter                             0.0  1.0  2179 
fi_speciespikeperch                      0.1  1.0   903 
fi_specieswels                           0.1  1.0   845 
seasonspring_I:fi_speciespikeperch       0.0  1.0  2126 
seasonspring_II:fi_speciespikeperch      0.1  1.0  2077 
seasonsummer:fi_speciespikeperch         0.0  1.0  2034 
seasonwinter:fi_speciespikeperch         0.0  1.0  2255 
seasonspring_I:fi_specieswels            0.0  1.0  2015 
seasonspring_II:fi_specieswels           0.0  1.0  2371 
seasonsummer:fi_specieswels              0.0  1.0  2018 
seasonwinter:fi_specieswels              0.0  1.0  2197 
b[(Intercept) fi_fishid:T449202_1]       0.1  1.0  1066 
b[(Intercept) fi_fishid:T449203_1]       0.1  1.0   673 
b[(Intercept) fi_fishid:T449204_1]       0.1  1.0   719 
b[(Intercept) fi_fishid:T449207_1]       0.1  1.0   675 
b[(Intercept) fi_fishid:T449208_1]       0.1  1.0  1058 
b[(Intercept) fi_fishid:T449209_1]       0.1  1.0   732 
b[(Intercept) fi_fishid:T449213_1]       0.1  1.0  1114 
b[(Intercept) fi_fishid:T449215_1]       0.1  1.0  1133 
b[(Intercept) fi_fishid:T449268_1]       0.1  1.0   868 
b[(Intercept) fi_fishid:T449269_1]       0.1  1.0   856 
b[(Intercept) fi_fishid:T449270_2]       0.1  1.0   729 
b[(Intercept) fi_fishid:T449271_1]       0.1  1.0   670 
b[(Intercept) fi_fishid:T449272_1]       0.1  1.0   695 
b[(Intercept) fi_fishid:T449274_1]       0.1  1.0   876 
b[(Intercept) fi_fishid:T449275_1]       0.1  1.0   877 
b[(Intercept) fi_fishid:T449276_1]       0.1  1.0   866 
b[(Intercept) fi_fishid:T449277_1]       0.1  1.0   708 
b[(Intercept) fi_fishid:T449278_1]       0.1  1.0   908 
b[(Intercept) fi_fishid:T449280_1]       0.1  1.0   889 
b[(Intercept) fi_fishid:T449282_1]       0.1  1.0  1009 
b[(Intercept) fi_fishid:T449284_1]       0.1  1.0   859 
b[(Intercept) fi_fishid:T449285_1]       0.1  1.0   720 
b[(Intercept) fi_fishid:T449286_1]       0.1  1.0   916 
b[(Intercept) fi_fishid:T449287_1]       0.1  1.0   777 
b[(Intercept) fi_fishid:T449288_1]       0.1  1.0   941 
b[(Intercept) fi_fishid:T449310_1]       0.1  1.0  1073 
b[(Intercept) fi_fishid:T449313_1]       0.1  1.0  1109 
b[(Intercept) fi_fishid:T449314_1]       0.1  1.0   874 
b[(Intercept) fi_fishid:T449317_1]       0.1  1.0  1060 
b[(Intercept) fi_fishid:T449318_1]       0.1  1.0   887 
b[(Intercept) fi_fishid:T449319_1]       0.1  1.0   911 
sigma                                    0.0  1.0  3728 
Sigma[fi_fishid:(Intercept),(Intercept)] 0.4  1.0  1010 
mean_PPD                                 0.0  1.0  3698 
log-posterior                            0.2  1.0   777 

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).
```

### Quick Summary

`print(m2, digits = 2)`

```
stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + season * fi_species + (1 | fi_fishid)
 observations: 8846
------
                                    Median MAD_SD
(Intercept)                          26.55   2.15
seasonspring_I                       -1.80   0.84
seasonspring_II                      14.58   1.64
seasonsummer                         -3.29   0.68
seasonwinter                          3.69   0.76
fi_speciespikeperch                  -7.66   3.12
fi_specieswels                        0.24   2.91
seasonspring_I:fi_speciespikeperch    7.58   1.26
seasonspring_II:fi_speciespikeperch -13.61   2.46
seasonsummer:fi_speciespikeperch     12.97   1.12
seasonwinter:fi_speciespikeperch     -3.50   1.18
seasonspring_I:fi_specieswels         6.85   1.08
seasonspring_II:fi_specieswels      -10.20   2.17
seasonsummer:fi_specieswels           4.34   0.87
seasonwinter:fi_specieswels          -2.90   1.00
sigma                                13.67   0.10

Error terms:
 Groups    Name        Std.Dev.
 fi_fishid (Intercept)  6.93   
 Residual              13.67   
Num. levels: fi_fishid 31 

Sample avg. posterior predictive distribution of y:
         Median MAD_SD
mean_PPD 26.92   0.20 

------
For info on the priors used see help('prior_summary.stanreg').

```
### Check priors used 

`prior_summary(object = m2)`

```{r, echo=FALSE, eval=TRUE}
Priors for model 'm2' 
------
Intercept (after predictors centered)
 ~ normal(location = 0, scale = 10)
     **adjusted scale = 154.58

Coefficients
 ~ normal(location = [0,0,0,...], scale = [2.5,2.5,2.5,...])
     **adjusted scale = [38.65,38.65,38.65,...]

Auxiliary (sigma)
 ~ exponential(rate = 1)
     **adjusted scale = 15.46 (adjusted rate = 1/adjusted scale)

Covariance
 ~ decov(reg. = 1, conc. = 1, shape = 1, scale = 1)
------
See help('prior_summary.stanreg') for more details
```

### Posterior means, s.d., 95% credible intervals, MC errors

`summary(m2, pars = c("(Intercept)", "sigma", "Sigma[fi_fishid:(Intercept),(Intercept)]"),
          probs = c(0.025, 0.975), digits = 2)`

```
Model Info:

 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + season * fi_species + (1 | fi_fishid)
 algorithm:    sampling
 priors:       see help('prior_summary')
 sample:       4000 (posterior sample size)
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
                                           mean   sd    2.5%   97.5%
(Intercept)                              26.57   2.25 22.14  31.07  
sigma                                    13.67   0.10 13.47  13.87  
Sigma[fi_fishid:(Intercept),(Intercept)] 48.08  14.24 28.00  82.63  

Diagnostics:
                                         mcse Rhat n_eff
(Intercept)                              0.09 1.01  663 
sigma                                    0.00 1.00 3728 
Sigma[fi_fishid:(Intercept),(Intercept)] 0.45 1.00 1010 

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).

```

### Extract the posterior draws for all parameters

`sims <- as.matrix(m2)
dim(sims)
para_name <- colnames(sims)
para_name`

```{r, echo=FALSE, eval=TRUE}

[1] 4000   48


 [1] "(Intercept)"                              "seasonspring_I"                          
 [3] "seasonspring_II"                          "seasonsummer"                            
 [5] "seasonwinter"                             "fi_speciespikeperch"                     
 [7] "fi_specieswels"                           "seasonspring_I:fi_speciespikeperch"      
 [9] "seasonspring_II:fi_speciespikeperch"      "seasonsummer:fi_speciespikeperch"        
[11] "seasonwinter:fi_speciespikeperch"         "seasonspring_I:fi_specieswels"           
[13] "seasonspring_II:fi_specieswels"           "seasonsummer:fi_specieswels"             
[15] "seasonwinter:fi_specieswels"              "b[(Intercept) fi_fishid:T449202_1]"      
[17] "b[(Intercept) fi_fishid:T449203_1]"       "b[(Intercept) fi_fishid:T449204_1]"      
[19] "b[(Intercept) fi_fishid:T449207_1]"       "b[(Intercept) fi_fishid:T449208_1]"      
[21] "b[(Intercept) fi_fishid:T449209_1]"       "b[(Intercept) fi_fishid:T449213_1]"      
[23] "b[(Intercept) fi_fishid:T449215_1]"       "b[(Intercept) fi_fishid:T449268_1]"      
[25] "b[(Intercept) fi_fishid:T449269_1]"       "b[(Intercept) fi_fishid:T449270_2]"      
[27] "b[(Intercept) fi_fishid:T449271_1]"       "b[(Intercept) fi_fishid:T449272_1]"      
[29] "b[(Intercept) fi_fishid:T449274_1]"       "b[(Intercept) fi_fishid:T449275_1]"      
[31] "b[(Intercept) fi_fishid:T449276_1]"       "b[(Intercept) fi_fishid:T449277_1]"      
[33] "b[(Intercept) fi_fishid:T449278_1]"       "b[(Intercept) fi_fishid:T449280_1]"      
[35] "b[(Intercept) fi_fishid:T449282_1]"       "b[(Intercept) fi_fishid:T449284_1]"      
[37] "b[(Intercept) fi_fishid:T449285_1]"       "b[(Intercept) fi_fishid:T449286_1]"      
[39] "b[(Intercept) fi_fishid:T449287_1]"       "b[(Intercept) fi_fishid:T449288_1]"      
[41] "b[(Intercept) fi_fishid:T449310_1]"       "b[(Intercept) fi_fishid:T449313_1]"      
[43] "b[(Intercept) fi_fishid:T449314_1]"       "b[(Intercept) fi_fishid:T449317_1]"      
[45] "b[(Intercept) fi_fishid:T449318_1]"       "b[(Intercept) fi_fishid:T449319_1]"      
[47] "sigma"                                    "Sigma[fi_fishid:(Intercept),(Intercept)]"
```

### Obtain fi_fishid-level varying intercept a_j

#### draws for overall mean

`mu_a_sims <- as.matrix(m2, pars = "(Intercept)")`

#### draws for 30 fishes' fi_fishid-level error

`fish_err <- as.matrix(m2, regex_pars = "b\\[\\(Intercept\\) fi_fishid\\:")`

#### draws for 30 fishes' varying intercepts

`fish_inter <- as.numeric(mu_a_sims) + fish_err`


### Obtain sigma_y and sigma_alpha^2

#### draws for sigma_y

`signma_y <- as.matrix(m2, pars = "sigma")`

#### draws for sigma_alpha^2

`sigma_alpha <- as.matrix(m2, pars = "Sigma[fi_fishid:(Intercept),(Intercept)]")`


### Obtaining means, SD, medians and 95% credible intervals of varying intercepts

#### In fish_inter, we have saved 4,000 posterior draws (from all 4 chains) for the varying intercepts αj of the 30 fishes. For example, the first column of the 4,000 by 30 matrix is a vector of 4,000 posterior simulation draws for the first fish's (fi_fishid:T449202_1) varying intercept α1. One quantitative way to summarize the posterior probability distribution of these 4,000 estimates for α1 is to examine their quantiles.

#### Posterior mean and SD of each alpha

`a_mean <- apply(X = fish_inter,     # posterior mean
                MARGIN = 2,
                FUN = mean)`
                
`a_sd <- apply(X = fish_inter,       # posterior SD
              MARGIN = 2,
              FUN = sd)`
```
a_mean



```

              
##### Posterior median and 95% credible interval

`a_quant <- apply(X = fish_inter,
                 MARGIN = 2,
                 FUN = quantile,
                 probs = c(0.025, 0.50, 0.975))`
                 
`a_quant <- data.frame(t(a_quant))`

`names(a_quant) <- c("Q2.5", "Q50", "Q97.5")`


##### Combine summary statistics of posterior simulation draws

`a_df <- data.frame(a_mean, a_sd, a_quant)
round(head(a_df), 2)`


### Plots

#### Caterpillar plot to show the fully Bayes estimates for the fi_fishid varying intercepts in rank order together with their 95% credible interval

##### Sort dataframe containing an estimated alpha's mean and sd for every fi_fishid

`a_df <- a_df[order(a_df$a_mean), ]
a_df$a_rank <- c(1 : dim(a_df)[1])  # a vector of fi_fishid rank`

###### Plot fish-level alphas's posterior mean and 95% credible interval

```
ggplot(data = a_df,
       aes(x = a_rank,
           y = a_mean)) +
  geom_pointrange(aes(ymin = Q2.5,
                      ymax = Q97.5),
                  position = position_jitter(width = 0.1,
                                             height = 0)) +
  geom_hline(yintercept = mean(a_df$a_mean),
             size = 0.5,
             col = "red") +
  scale_x_continuous("Rank",
                     breaks = seq(from = 0,
                                  to = 80,
                                  by = 5)) +
  scale_y_continuous(expression(paste("varying intercept, ", alpha[j]))) +
  theme_bw( base_family = "serif")
```

###### The difference between the two fish averages (fish #21 and #29)

`fish_diff <- a_sims[, 21] - a_sims[, 29]`


##### We can investigate the posterior distribution of the difference with descriptive statistics and a histogram as follows:

###### Investigate differences of two distributions

`mean <- mean(fish_diff)
sd <- sd(fish_diff)
quantile <- quantile(fish_diff, probs = c(0.025, 0.50, 0.975))
quantile <- data.frame(t(quantile))
names(quantile) <- c("Q2.5", "Q50", "Q97.5")
diff_df <- data.frame(mean, sd, quantile)
round(diff_df, 2)`

###### Histogram of the differences

```
ggplot(data = data.frame(fish_diff),
       aes(x = fish_diff)) +
  geom_histogram(color = "black",
                 fill = "gray",
                 binwidth = 0.75) +
  scale_x_continuous("Score diffence between two fishes: #21, #29",
                     breaks = seq(from = -20,
                                  to = 20,
                                  by = 10)) +
  geom_vline(xintercept = c(mean(fish_diff),
                            quantile(fish_diff,
                                     probs = c(0.025, 0.975))),
             colour = "red",
             linetype = "longdash") +
  geom_text(aes(0.45, 20, label = "mean = 0.45"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 3.39"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`
```

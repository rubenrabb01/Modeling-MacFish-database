## Bayesian mixed-effects model fitted with stan 

*2-level Longitudinal conditional growth model*

- Random intercept model + dropping random slope / Random Intercept with One Fixed Level-1 Factor (Non-Random Slope) 

`m2 <- stan_lmer(formula= ranged2d ~ 1 + season*fi_species + (1 | fi_fishid), data = mean.ranged2d, seed=350)`

### Model 2 results 

```
SAMPLING FOR MODEL 'continuous' NOW (CHAIN 1).
Chain 1: 
Chain 1: Gradient evaluation took 0 seconds
Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 0 seconds.
Chain 1: Adjust your expectations accordingly!
Chain 1: 
Chain 1: 
Chain 1: Iteration:    1 / 2000 [  0%]  (Warmup)
Chain 1: Iteration:  200 / 2000 [ 10%]  (Warmup)
Chain 1: Iteration:  400 / 2000 [ 20%]  (Warmup)
Chain 1: Iteration:  600 / 2000 [ 30%]  (Warmup)
Chain 1: Iteration:  800 / 2000 [ 40%]  (Warmup)
Chain 1: Iteration: 1000 / 2000 [ 50%]  (Warmup)
Chain 1: Iteration: 1001 / 2000 [ 50%]  (Sampling)
Chain 1: Iteration: 1200 / 2000 [ 60%]  (Sampling)
Chain 1: Iteration: 1400 / 2000 [ 70%]  (Sampling)
Chain 1: Iteration: 1600 / 2000 [ 80%]  (Sampling)
Chain 1: Iteration: 1800 / 2000 [ 90%]  (Sampling)
Chain 1: Iteration: 2000 / 2000 [100%]  (Sampling)
Chain 1: 
Chain 1:  Elapsed Time: 163.639 seconds (Warm-up)
Chain 1:                135.509 seconds (Sampling)
Chain 1:                299.148 seconds (Total)
Chain 1: 

SAMPLING FOR MODEL 'continuous' NOW (CHAIN 2).
Chain 2: 
Chain 2: Gradient evaluation took 0.001 seconds
Chain 2: 1000 transitions using 10 leapfrog steps per transition would take 10 seconds.
Chain 2: Adjust your expectations accordingly!
Chain 2: 
Chain 2: 
Chain 2: Iteration:    1 / 2000 [  0%]  (Warmup)
Chain 2: Iteration:  200 / 2000 [ 10%]  (Warmup)
Chain 2: Iteration:  400 / 2000 [ 20%]  (Warmup)
Chain 2: Iteration:  600 / 2000 [ 30%]  (Warmup)
Chain 2: Iteration:  800 / 2000 [ 40%]  (Warmup)
Chain 2: Iteration: 1000 / 2000 [ 50%]  (Warmup)
Chain 2: Iteration: 1001 / 2000 [ 50%]  (Sampling)
Chain 2: Iteration: 1200 / 2000 [ 60%]  (Sampling)
Chain 2: Iteration: 1400 / 2000 [ 70%]  (Sampling)
Chain 2: Iteration: 1600 / 2000 [ 80%]  (Sampling)
Chain 2: Iteration: 1800 / 2000 [ 90%]  (Sampling)
Chain 2: Iteration: 2000 / 2000 [100%]  (Sampling)
Chain 2: 
Chain 2:  Elapsed Time: 158.662 seconds (Warm-up)
Chain 2:                130.993 seconds (Sampling)
Chain 2:                289.655 seconds (Total)
Chain 2: 

SAMPLING FOR MODEL 'continuous' NOW (CHAIN 3).
Chain 3: 
Chain 3: Gradient evaluation took 0.002 seconds
Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 20 seconds.
Chain 3: Adjust your expectations accordingly!
Chain 3: 
Chain 3: 
Chain 3: Iteration:    1 / 2000 [  0%]  (Warmup)
Chain 3: Iteration:  200 / 2000 [ 10%]  (Warmup)
Chain 3: Iteration:  400 / 2000 [ 20%]  (Warmup)
Chain 3: Iteration:  600 / 2000 [ 30%]  (Warmup)
Chain 3: Iteration:  800 / 2000 [ 40%]  (Warmup)
Chain 3: Iteration: 1000 / 2000 [ 50%]  (Warmup)
Chain 3: Iteration: 1001 / 2000 [ 50%]  (Sampling)
Chain 3: Iteration: 1200 / 2000 [ 60%]  (Sampling)
Chain 3: Iteration: 1400 / 2000 [ 70%]  (Sampling)
Chain 3: Iteration: 1600 / 2000 [ 80%]  (Sampling)
Chain 3: Iteration: 1800 / 2000 [ 90%]  (Sampling)
Chain 3: Iteration: 2000 / 2000 [100%]  (Sampling)
Chain 3: 
Chain 3:  Elapsed Time: 155.521 seconds (Warm-up)
Chain 3:                137.486 seconds (Sampling)
Chain 3:                293.007 seconds (Total)
Chain 3: 

SAMPLING FOR MODEL 'continuous' NOW (CHAIN 4).
Chain 4: 
Chain 4: Gradient evaluation took 0.001 seconds
Chain 4: 1000 transitions using 10 leapfrog steps per transition would take 10 seconds.
Chain 4: Adjust your expectations accordingly!
Chain 4: 
Chain 4: 
Chain 4: Iteration:    1 / 2000 [  0%]  (Warmup)
Chain 4: Iteration:  200 / 2000 [ 10%]  (Warmup)
Chain 4: Iteration:  400 / 2000 [ 20%]  (Warmup)
Chain 4: Iteration:  600 / 2000 [ 30%]  (Warmup)
Chain 4: Iteration:  800 / 2000 [ 40%]  (Warmup)
Chain 4: Iteration: 1000 / 2000 [ 50%]  (Warmup)
Chain 4: Iteration: 1001 / 2000 [ 50%]  (Sampling)
Chain 4: Iteration: 1200 / 2000 [ 60%]  (Sampling)
Chain 4: Iteration: 1400 / 2000 [ 70%]  (Sampling)
Chain 4: Iteration: 1600 / 2000 [ 80%]  (Sampling)
Chain 4: Iteration: 1800 / 2000 [ 90%]  (Sampling)
Chain 4: Iteration: 2000 / 2000 [100%]  (Sampling)
Chain 4: 
Chain 4:  Elapsed Time: 160.03 seconds (Warm-up)
Chain 4:                141.466 seconds (Sampling)
Chain 4:                301.496 seconds (Total)
Chain 4: 
```

### Quick Summary

`print(m2, digits = 2)`

```
stan_lmer
 family:       gaussian [identity]
 formula:      ranged2d ~ 1 + season * fi_species + (1 | fi_fishid)
 observations: 8846
------
                                    Median  MAD_SD 
(Intercept)                          966.82  122.00
seasonspring_I                      -135.45   49.07
seasonspring_II                     1014.34  104.74
seasonsummer                        -249.56   42.42
seasonwinter                         196.17   46.95
fi_speciespikeperch                 -413.91  184.80
fi_specieswels                       -82.15  162.40
seasonspring_I:fi_speciespikeperch   534.21   76.61
seasonspring_II:fi_speciespikeperch -967.62  147.40
seasonsummer:fi_speciespikeperch     784.70   67.35
seasonwinter:fi_speciespikeperch    -168.71   74.23
seasonspring_I:fi_specieswels        562.69   62.63
seasonspring_II:fi_specieswels      -740.38  134.57
seasonsummer:fi_specieswels          412.09   53.80
seasonwinter:fi_specieswels         -154.31   59.60

Auxiliary parameter(s):
      Median MAD_SD
sigma 822.64   5.99

Error terms:
 Groups    Name        Std.Dev.
 fi_fishid (Intercept) 372     
 Residual              823     
Num. levels: fi_fishid 31 

------
* For help interpreting the printed output see ?print.stanreg
* For info on the priors used see ?prior_summary.stanreg
```

`summary(m2)`

```
Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      ranged2d ~ 1 + season * fi_species + (1 | fi_fishid)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
                                           mean     sd       10%      50%      90%   
(Intercept)                                 968.3    123.4    810.5    966.8   1124.3
seasonspring_I                             -135.1     49.8   -199.3   -135.4    -71.4
seasonspring_II                            1016.6    104.7    883.9   1014.3   1149.7
seasonsummer                               -249.7     42.6   -305.2   -249.6   -195.8
seasonwinter                                196.6     46.9    136.6    196.2    256.5
fi_speciespikeperch                        -414.0    187.0   -655.8   -413.9   -181.9
fi_specieswels                              -87.3    161.0   -294.8    -82.2    113.3
seasonspring_I:fi_speciespikeperch          533.9     77.0    436.3    534.2    632.1
seasonspring_II:fi_speciespikeperch        -967.8    152.3  -1163.4   -967.6   -772.7
seasonsummer:fi_speciespikeperch            783.2     66.9    698.1    784.7    868.4
seasonwinter:fi_speciespikeperch           -169.8     73.3   -265.1   -168.7    -75.3
seasonspring_I:fi_specieswels               561.4     63.4    479.8    562.7    642.3
seasonspring_II:fi_specieswels             -742.8    134.4   -914.5   -740.4   -573.5
seasonsummer:fi_specieswels                 412.6     53.7    344.7    412.1    483.5
seasonwinter:fi_specieswels                -154.3     59.0   -230.4   -154.3    -78.4
b[(Intercept) fi_fishid:T449202_1]          -43.5    146.1   -231.1    -45.6    146.4
b[(Intercept) fi_fishid:T449203_1]          530.3    127.4    367.7    532.3    688.8
b[(Intercept) fi_fishid:T449204_1]         -119.1    136.5   -297.0   -116.2     55.8
b[(Intercept) fi_fishid:T449207_1]         -480.7    127.3   -644.6   -477.4   -322.5
b[(Intercept) fi_fishid:T449208_1]         -463.6    146.5   -648.6   -469.2   -275.7
b[(Intercept) fi_fishid:T449209_1]         -123.3    127.1   -289.2   -121.0     34.8
b[(Intercept) fi_fishid:T449213_1]          541.8    147.1    355.2    537.3    731.5
b[(Intercept) fi_fishid:T449215_1]          363.5    151.1    174.6    360.3    557.8
b[(Intercept) fi_fishid:T449268_1]          390.1    106.7    253.2    391.5    527.0
b[(Intercept) fi_fishid:T449269_1]          310.8    108.0    172.7    312.1    450.7
b[(Intercept) fi_fishid:T449270_2]          297.2    129.1    129.7    298.8    464.0
b[(Intercept) fi_fishid:T449271_1]           38.3    128.3   -127.8     42.4    195.1
b[(Intercept) fi_fishid:T449272_1]          657.6    128.1    492.5    659.9    819.5
b[(Intercept) fi_fishid:T449274_1]          367.9    105.3    230.9    367.7    503.2
b[(Intercept) fi_fishid:T449275_1]          282.8    105.9    147.1    283.2    415.4
b[(Intercept) fi_fishid:T449276_1]           91.3    106.5    -47.6     91.1    226.8
b[(Intercept) fi_fishid:T449277_1]          -75.1    127.1   -234.2    -72.7     84.8
b[(Intercept) fi_fishid:T449278_1]         -527.6    106.3   -666.1   -526.9   -391.4
b[(Intercept) fi_fishid:T449280_1]           18.5    107.0   -120.2     21.2    154.6
b[(Intercept) fi_fishid:T449282_1]         -613.3    113.1   -758.6   -610.7   -470.6
b[(Intercept) fi_fishid:T449284_1]         -278.9    105.7   -413.8   -277.6   -143.0
b[(Intercept) fi_fishid:T449285_1]         -342.5    132.4   -511.8   -341.9   -174.0
b[(Intercept) fi_fishid:T449286_1]         -224.4    106.2   -361.0   -223.5    -90.7
b[(Intercept) fi_fishid:T449287_1]         -392.2    127.6   -557.8   -389.2   -230.9
b[(Intercept) fi_fishid:T449288_1]           98.3    108.6    -41.5     97.1    235.6
b[(Intercept) fi_fishid:T449310_1]          -42.3    145.8   -225.8    -45.1    139.2
b[(Intercept) fi_fishid:T449313_1]            9.0    145.9   -174.3      3.4    196.0
b[(Intercept) fi_fishid:T449314_1]         -168.5    105.9   -301.8   -166.8    -33.1
b[(Intercept) fi_fishid:T449317_1]         -404.0    151.9   -595.8   -409.0   -203.8
b[(Intercept) fi_fishid:T449318_1]           44.8    107.8    -93.2     46.8    183.6
b[(Intercept) fi_fishid:T449319_1]          200.7    105.8     64.3    201.0    337.5
sigma                                       822.6      6.2    814.9    822.6    830.7
Sigma[fi_fishid:(Intercept),(Intercept)] 138145.7  39534.5  94977.8 132102.8 189374.5

Fit Diagnostics:
           mean   sd    10%   50%   90%
mean_PPD 962.6   12.6 946.3 962.6 978.9

The mean_ppd is the sample average posterior predictive distribution of the outcome variable (for details see help('summary.stanreg')).

MCMC diagnostics
                                         mcse   Rhat   n_eff
(Intercept)                                 3.8    1.0 1063 
seasonspring_I                              1.0    1.0 2359 
seasonspring_II                             2.2    1.0 2297 
seasonsummer                                0.9    1.0 2045 
seasonwinter                                1.1    1.0 1937 
fi_speciespikeperch                         5.9    1.0 1020 
fi_specieswels                              5.2    1.0  967 
seasonspring_I:fi_speciespikeperch          1.5    1.0 2577 
seasonspring_II:fi_speciespikeperch         3.0    1.0 2608 
seasonsummer:fi_speciespikeperch            1.4    1.0 2253 
seasonwinter:fi_speciespikeperch            1.5    1.0 2415 
seasonspring_I:fi_specieswels               1.3    1.0 2387 
seasonspring_II:fi_specieswels              2.8    1.0 2301 
seasonsummer:fi_specieswels                 1.1    1.0 2199 
seasonwinter:fi_specieswels                 1.3    1.0 2226 
b[(Intercept) fi_fishid:T449202_1]          4.4    1.0 1079 
b[(Intercept) fi_fishid:T449203_1]          3.9    1.0 1079 
b[(Intercept) fi_fishid:T449204_1]          3.9    1.0 1217 
b[(Intercept) fi_fishid:T449207_1]          3.8    1.0 1110 
b[(Intercept) fi_fishid:T449208_1]          4.4    1.0 1101 
b[(Intercept) fi_fishid:T449209_1]          3.8    1.0 1101 
b[(Intercept) fi_fishid:T449213_1]          4.5    1.0 1080 
b[(Intercept) fi_fishid:T449215_1]          4.4    1.0 1190 
b[(Intercept) fi_fishid:T449268_1]          3.3    1.0 1063 
b[(Intercept) fi_fishid:T449269_1]          3.3    1.0 1096 
b[(Intercept) fi_fishid:T449270_2]          3.8    1.0 1148 
b[(Intercept) fi_fishid:T449271_1]          3.8    1.0 1131 
b[(Intercept) fi_fishid:T449272_1]          3.9    1.0 1104 
b[(Intercept) fi_fishid:T449274_1]          3.2    1.0 1075 
b[(Intercept) fi_fishid:T449275_1]          3.3    1.0 1047 
b[(Intercept) fi_fishid:T449276_1]          3.3    1.0 1056 
b[(Intercept) fi_fishid:T449277_1]          3.8    1.0 1117 
b[(Intercept) fi_fishid:T449278_1]          3.3    1.0 1032 
b[(Intercept) fi_fishid:T449280_1]          3.3    1.0 1077 
b[(Intercept) fi_fishid:T449282_1]          3.4    1.0 1112 
b[(Intercept) fi_fishid:T449284_1]          3.3    1.0 1040 
b[(Intercept) fi_fishid:T449285_1]          3.8    1.0 1215 
b[(Intercept) fi_fishid:T449286_1]          3.3    1.0 1063 
b[(Intercept) fi_fishid:T449287_1]          3.9    1.0 1059 
b[(Intercept) fi_fishid:T449288_1]          3.3    1.0 1090 
b[(Intercept) fi_fishid:T449310_1]          4.3    1.0 1123 
b[(Intercept) fi_fishid:T449313_1]          4.5    1.0 1060 
b[(Intercept) fi_fishid:T449314_1]          3.3    1.0 1042 
b[(Intercept) fi_fishid:T449317_1]          4.4    1.0 1201 
b[(Intercept) fi_fishid:T449318_1]          3.2    1.0 1115 
b[(Intercept) fi_fishid:T449319_1]          3.2    1.0 1061 
sigma                                       0.1    1.0 3282 
Sigma[fi_fishid:(Intercept),(Intercept)] 1185.9    1.0 1111 
mean_PPD                                    0.2    1.0 4031 
log-posterior                               0.2    1.0  988 

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).
```

### Check priors used 

`prior_summary(object = m2)`

```{r, echo=FALSE, eval=TRUE}

Priors for model 'm2' 
------
Intercept (after predictors centered)
  Specified prior:
    ~ normal(location = 0, scale = 10)
  Adjusted prior:
    ~ normal(location = 0, scale = 9149)

Coefficients
  Specified prior:
    ~ normal(location = [0,0,0,...], scale = [2.5,2.5,2.5,...])
  Adjusted prior:
    ~ normal(location = [0,0,0,...], scale = [2287.26,2287.26,2287.26,...])

Auxiliary (sigma)
  Specified prior:
    ~ exponential(rate = 1)
  Adjusted prior:
    ~ exponential(rate = 0.0011)

Covariance
 ~ decov(reg. = 1, conc. = 1, shape = 1, scale = 1)
------
See help('prior_summary.stanreg') for more details
```

### Posterior means, s.d., 95% credible intervals, MC errors

`summary(m2, pars = c("(Intercept)", "sigma", "Sigma[fi_fishid:(Intercept),(Intercept)]"), probs = c(0.025, 0.975), digits = 2)`

```
Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      ranged2d ~ 1 + season * fi_species + (1 | fi_fishid)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
                                           mean      sd        2.5%      97.5%  
(Intercept)                                 968.30    123.44    730.22   1216.03
sigma                                       822.63      6.16    810.43    834.61
Sigma[fi_fishid:(Intercept),(Intercept)] 138145.65  39534.52  81222.48 234009.14

MCMC diagnostics
                                         mcse    Rhat    n_eff
(Intercept)                                 3.79    1.01 1063 
sigma                                       0.11    1.00 3282 
Sigma[fi_fishid:(Intercept),(Intercept)] 1185.90    1.00 1111 

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).
```

### Extract the posterior draws for all parameters

`sims <- as.matrix(m2)`

`dim(sims)`

```
[1] 4000   48

```

`para_name <- colnames(sims)`

`para_name`

```
 [1] "(Intercept)"                              "seasonspring_I"                           "seasonspring_II"                         
 [4] "seasonsummer"                             "seasonwinter"                             "fi_speciespikeperch"                     
 [7] "fi_specieswels"                           "seasonspring_I:fi_speciespikeperch"       "seasonspring_II:fi_speciespikeperch"     
[10] "seasonsummer:fi_speciespikeperch"         "seasonwinter:fi_speciespikeperch"         "seasonspring_I:fi_specieswels"           
[13] "seasonspring_II:fi_specieswels"           "seasonsummer:fi_specieswels"              "seasonwinter:fi_specieswels"             
[16] "b[(Intercept) fi_fishid:T449202_1]"       "b[(Intercept) fi_fishid:T449203_1]"       "b[(Intercept) fi_fishid:T449204_1]"      
[19] "b[(Intercept) fi_fishid:T449207_1]"       "b[(Intercept) fi_fishid:T449208_1]"       "b[(Intercept) fi_fishid:T449209_1]"      
[22] "b[(Intercept) fi_fishid:T449213_1]"       "b[(Intercept) fi_fishid:T449215_1]"       "b[(Intercept) fi_fishid:T449268_1]"      
[25] "b[(Intercept) fi_fishid:T449269_1]"       "b[(Intercept) fi_fishid:T449270_2]"       "b[(Intercept) fi_fishid:T449271_1]"      
[28] "b[(Intercept) fi_fishid:T449272_1]"       "b[(Intercept) fi_fishid:T449274_1]"       "b[(Intercept) fi_fishid:T449275_1]"      
[31] "b[(Intercept) fi_fishid:T449276_1]"       "b[(Intercept) fi_fishid:T449277_1]"       "b[(Intercept) fi_fishid:T449278_1]"      
[34] "b[(Intercept) fi_fishid:T449280_1]"       "b[(Intercept) fi_fishid:T449282_1]"       "b[(Intercept) fi_fishid:T449284_1]"      
[37] "b[(Intercept) fi_fishid:T449285_1]"       "b[(Intercept) fi_fishid:T449286_1]"       "b[(Intercept) fi_fishid:T449287_1]"      
[40] "b[(Intercept) fi_fishid:T449288_1]"       "b[(Intercept) fi_fishid:T449310_1]"       "b[(Intercept) fi_fishid:T449313_1]"      
[43] "b[(Intercept) fi_fishid:T449314_1]"       "b[(Intercept) fi_fishid:T449317_1]"       "b[(Intercept) fi_fishid:T449318_1]"      
[46] "b[(Intercept) fi_fishid:T449319_1]"       "sigma"                                    "Sigma[fi_fishid:(Intercept),(Intercept)]"
``` 

- Obtain fi_fishid-level varying intercept a_j

 - draws for overall mean

`mu_a_sims <- as.matrix(m2, pars = "(Intercept)")`

 - draws for 30 fishes' fi_fishid-level error

`fish_err <- as.matrix(m2, regex_pars = "b\\[\\(Intercept\\) fi_fishid\\:")`

 - draws for 30 fishes' varying intercepts

`fish_inter <- as.numeric(mu_a_sims) + fish_err`


- Obtain sigma_y and sigma_alpha^2

 -draws for sigma_y

`signma_y <- as.matrix(m2, pars = "sigma")`

 - draws for sigma_alpha^2

`sigma_alpha <- as.matrix(m2, pars = "Sigma[fi_fishid:(Intercept),(Intercept)]")`


### Obtaining means, SD, medians and 95% credible intervals of varying intercepts

In *fish_inter*, we have saved 4,000 posterior draws (from all 4 chains) for the varying intercepts αj of the 30 fishes. For example, the first column of the 4,000 by 30 matrix is a vector of 4,000 posterior simulation draws for the first fish's (*fi_fishid*:T449202_1) varying intercept α1. One quantitative way to summarize the posterior probability distribution of these 4,000 estimates for α1 is to examine their quantiles.

- Posterior mean of each alpha

`a_mean <- apply(X = fish_inter, MARGIN = 2, FUN = mean)`

`a_mean' 

```
b[(Intercept) fi_fishid:T449202_1] b[(Intercept) fi_fishid:T449203_1] b[(Intercept) fi_fishid:T449204_1] b[(Intercept) fi_fishid:T449207_1] 
                     924.809551472                     1498.573236990                      849.198390936                      487.584765701 
b[(Intercept) fi_fishid:T449208_1] b[(Intercept) fi_fishid:T449209_1] b[(Intercept) fi_fishid:T449213_1] b[(Intercept) fi_fishid:T449215_1] 
                     504.700363657                      844.966894634                     1510.080568054                     1331.841436573 
b[(Intercept) fi_fishid:T449268_1] b[(Intercept) fi_fishid:T449269_1] b[(Intercept) fi_fishid:T449270_2] b[(Intercept) fi_fishid:T449271_1] 
                    1358.384251851                     1279.072876306                     1265.520046318                     1006.631783706 
b[(Intercept) fi_fishid:T449272_1] b[(Intercept) fi_fishid:T449274_1] b[(Intercept) fi_fishid:T449275_1] b[(Intercept) fi_fishid:T449276_1] 
                    1625.874169370                     1336.248297568                     1251.140568122                     1059.565422139 
b[(Intercept) fi_fishid:T449277_1] b[(Intercept) fi_fishid:T449278_1] b[(Intercept) fi_fishid:T449280_1] b[(Intercept) fi_fishid:T449282_1] 
                     893.246688289                      440.730476414                      986.817759524                      355.020305693 
b[(Intercept) fi_fishid:T449284_1] b[(Intercept) fi_fishid:T449285_1] b[(Intercept) fi_fishid:T449286_1] b[(Intercept) fi_fishid:T449287_1] 
                     689.406591633                      625.838764840                      743.935105063                      576.065198794 
b[(Intercept) fi_fishid:T449288_1] b[(Intercept) fi_fishid:T449310_1] b[(Intercept) fi_fishid:T449313_1] b[(Intercept) fi_fishid:T449314_1] 
                    1066.567426346                      926.043166974                      977.291008562                      799.838217954 
b[(Intercept) fi_fishid:T449317_1] b[(Intercept) fi_fishid:T449318_1] b[(Intercept) fi_fishid:T449319_1] 
                     564.342155705                     1013.101214510                     1169.013826030 
```       
      
- Posterior SD of each alpha  

`a_sd <- apply(X = fish_inter, MARGIN = 2, FUN = sd)`

`a_sd`

```
b[(Intercept) fi_fishid:T449202_1] b[(Intercept) fi_fishid:T449203_1] b[(Intercept) fi_fishid:T449204_1] b[(Intercept) fi_fishid:T449207_1] 
                    188.6835981608                      52.6323505672                      82.0120393960                      52.6899293050 
b[(Intercept) fi_fishid:T449208_1] b[(Intercept) fi_fishid:T449209_1] b[(Intercept) fi_fishid:T449213_1] b[(Intercept) fi_fishid:T449215_1] 
                    189.4396460829                      51.9438346630                     188.5762477297                     192.2625964195 
b[(Intercept) fi_fishid:T449268_1] b[(Intercept) fi_fishid:T449269_1] b[(Intercept) fi_fishid:T449270_2] b[(Intercept) fi_fishid:T449271_1] 
                    165.2177758195                     166.7127483204                      60.2278926606                      56.1807941353 
b[(Intercept) fi_fishid:T449272_1] b[(Intercept) fi_fishid:T449274_1] b[(Intercept) fi_fishid:T449275_1] b[(Intercept) fi_fishid:T449276_1] 
                     55.0555003235                     164.3991425317                     166.3687116862                     165.2479668204 
b[(Intercept) fi_fishid:T449277_1] b[(Intercept) fi_fishid:T449278_1] b[(Intercept) fi_fishid:T449280_1] b[(Intercept) fi_fishid:T449282_1] 
                     53.4992940733                     165.8050300906                     165.7580870197                     168.9331760725 
b[(Intercept) fi_fishid:T449284_1] b[(Intercept) fi_fishid:T449285_1] b[(Intercept) fi_fishid:T449286_1] b[(Intercept) fi_fishid:T449287_1] 
                    164.6380060016                      70.7972904300                     165.0037290420                      56.6101060746 
b[(Intercept) fi_fishid:T449288_1] b[(Intercept) fi_fishid:T449310_1] b[(Intercept) fi_fishid:T449313_1] b[(Intercept) fi_fishid:T449314_1] 
                    167.3209480228                     188.1765508947                     188.5249112539                     164.2500019094 
b[(Intercept) fi_fishid:T449317_1] b[(Intercept) fi_fishid:T449318_1] b[(Intercept) fi_fishid:T449319_1] 
                    192.7697680466                     165.6339600243                     164.7892489994
```

- Posterior median and 95% credible interval 

`a_quant <- apply(X = fish_inter, MARGIN = 2, FUN = quantile, probs = c(0.025, 0.50, 0.975))`
                 
`a_quant <- data.frame(t(a_quant))`

`a_quant`

```
                                            X2.5.           X50.         X97.5.
b[(Intercept) fi_fishid:T449202_1]  560.714425049  921.411964824 1306.737093943
b[(Intercept) fi_fishid:T449203_1] 1394.581448391 1498.937309220 1601.130433653
b[(Intercept) fi_fishid:T449204_1]  686.507451907  850.341355086 1005.647369341
b[(Intercept) fi_fishid:T449207_1]  383.813835110  487.543939081  589.695563543
b[(Intercept) fi_fishid:T449208_1]  139.125960467  503.573418186  883.316137331
b[(Intercept) fi_fishid:T449209_1]  743.722301815  845.620324379  944.402220260
b[(Intercept) fi_fishid:T449213_1] 1144.861542628 1510.487236421 1874.066990943
b[(Intercept) fi_fishid:T449215_1]  958.397420464 1329.990218477 1711.154546514
b[(Intercept) fi_fishid:T449268_1] 1038.487869401 1354.989854453 1689.545860547
b[(Intercept) fi_fishid:T449269_1]  956.546611240 1273.993467657 1612.082897627
b[(Intercept) fi_fishid:T449270_2] 1147.487027256 1265.565251279 1382.730134208
b[(Intercept) fi_fishid:T449271_1]  895.230799170 1007.157535561 1112.989961532
b[(Intercept) fi_fishid:T449272_1] 1518.452490987 1625.734326963 1729.871028413
b[(Intercept) fi_fishid:T449274_1] 1026.372783619 1331.773929191 1672.569881825
b[(Intercept) fi_fishid:T449275_1]  926.538762176 1246.311176139 1585.365795670
b[(Intercept) fi_fishid:T449276_1]  744.980124807 1052.463227551 1393.852514016
b[(Intercept) fi_fishid:T449277_1]  789.832759968  893.657299774  997.657403421
b[(Intercept) fi_fishid:T449278_1]  126.608151820  432.730487423  776.480294267
b[(Intercept) fi_fishid:T449280_1]  669.805189502  981.102762659 1316.693374412
b[(Intercept) fi_fishid:T449282_1]   33.915041925  351.111792811  695.081601765
b[(Intercept) fi_fishid:T449284_1]  371.028217128  685.128967542 1017.682728656
b[(Intercept) fi_fishid:T449285_1]  487.835819147  624.043112802  767.748929191
b[(Intercept) fi_fishid:T449286_1]  428.119190876  738.230492001 1079.122789603
b[(Intercept) fi_fishid:T449287_1]  464.865548699  575.690398650  681.946778444
b[(Intercept) fi_fishid:T449288_1]  748.782713206 1064.011197310 1404.293582437
b[(Intercept) fi_fishid:T449310_1]  555.973321954  922.233388778 1301.203899999
b[(Intercept) fi_fishid:T449313_1]  616.027593548  973.754325427 1358.904603179
b[(Intercept) fi_fishid:T449314_1]  494.598475793  796.062189300 1130.116587249
b[(Intercept) fi_fishid:T449317_1]  192.738653095  563.400059857  942.739678607
b[(Intercept) fi_fishid:T449318_1]  692.378159196 1009.487946094 1345.215324511
b[(Intercept) fi_fishid:T449319_1]  854.222585625 1165.537354164 1502.891679909

```

`names(a_quant) <- c("Q2.5", "Q50", "Q97.5")`

- Combine summary statistics of posterior simulation draws

`a_df <- data.frame(a_mean, a_sd, a_quant)`

`round(head(a_df), 2)`

```
                                    a_mean   a_sd    Q2.5     Q50   Q97.5
b[(Intercept) fi_fishid:T449202_1]  924.81 188.68  560.71  921.41 1306.74
b[(Intercept) fi_fishid:T449203_1] 1498.57  52.63 1394.58 1498.94 1601.13
b[(Intercept) fi_fishid:T449204_1]  849.20  82.01  686.51  850.34 1005.65
b[(Intercept) fi_fishid:T449207_1]  487.58  52.69  383.81  487.54  589.70
b[(Intercept) fi_fishid:T449208_1]  504.70 189.44  139.13  503.57  883.32
b[(Intercept) fi_fishid:T449209_1]  844.97  51.94  743.72  845.62  944.40
```

### Plots

Caterpillar plot to show the fully Bayes estimates for the *fi_fishid* varying intercepts in rank order together with their 95% credible interval

- Sort dataframe containing an estimated alpha's mean and sd for every fi_fishid

`a_df <- a_df[order(a_df$a_mean), ]`

`a_df$a_rank <- c(1 : dim(a_df)[1])`    # a vector of fi_fishid rank

- Plot fish-level alphas's posterior mean and 95% credible interval

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
![M2_s1](/Plots/M2_s1.png "M2_s1")

 
### Differences between species averages

Here I'll explore the posterior distribution of the differences between each two species with descriptive statistics and histograms

 - The difference between the two fish averages (fish #21 and #29)

`fish_diff <- fish_inter[, 21] - fish_inter[, 29]`

`mean <- mean(fish_diff1)`
`sd <- sd(fish_diff1)`
`quantile <- quantile(fish_diff1, probs = c(0.025, 0.50, 0.975))`
`quantile <- data.frame(t(quantile))`
`names(quantile) <- c("Q2.5", "Q50", "Q97.5")`
`diff_df1 <- data.frame(mean, sd, quantile)`
`round(diff_df1, 2)`

```
   mean     sd    Q2.5    Q50  Q97.5
1 125.06 185.18 -259.03 133.09 494.62
```

  - Histogram of the differences

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
  geom_text(aes(0.45, 20, label = "mean = 125.06"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 185.18"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")
```
![M2_s2](/Plots/M2_s2.png "M2_s2")

The expected difference comes to 3.30 with a standard deviation of 0.46 and a wide range of uncertainty. The 95% credible interval is [2.48,4.29], so we are 95% certain that the true value of the difference between the two species lies within the range, given the data

We also can get the proportion of the time that pike has a higher mean than pikeperch:

`prop.table(table(fish_inter[, 21] > fish_inter[, 29]))`

```
 FALSE   TRUE
0.2405 0.7595
```

This means that the posterior probability that pike is better than pikeperch is 76%. Any pair of fishes within the sample of fishes can be compared in this manner.

- The difference between the pikeperch and wels averages is:



### Analyse posterior

#### Plot posterior

- Including random effects from *fi_fishid*

`library(bayesplot)`

`mcmc_areas(as.matrix(m2), prob_outer = .999)`

![M2_s3](/Plots/M2_s3.png "M2_s3")



**Diagnostics for Pareto smoothed importance sampling (PSIS)**

Print a diagnostic table summarizing the estimated Pareto shape parameters and **PSIS** effective sample sizes, find the indexes of observations for which the estimated Pareto shape parameter *k* is larger than some threshold value, or plot observation indexes vs. diagnostic estimates. The Details section below provides a brief overview of the diagnostics, but we recommend consulting **Vehtari, Gelman, and Gabry (2017a, 2017b)** for full details.

The reliability and approximate convergence rate of the PSIS-based estimates can be assessed using the estimates for the shape parameter k of the generalized Pareto distribution:

If k<0.5 then the distribution of raw importance ratios has finite variance and the central limit theorem holds. However, as k approaches 0.5 the RMSE of plain importance sampling (IS) increases significantly while PSIS has lower RMSE.

If 0.5≤k<1 then the variance of the raw importance ratios is infinite, but the mean exists. TIS and PSIS estimates have finite variance by accepting some bias. The convergence of the estimate is slower with increasing k. If k is between 0.5 and approximately 0.7 then we observe practically useful convergence rates and Monte Carlo error estimates with PSIS (the bias of TIS increases faster than the bias of PSIS). If k>0.7 we observe impractical convergence rates and unreliable Monte Carlo error estimates.

If k≥1 then neither the variance nor the mean of the raw importance ratios exists. The convergence rate is close to zero and bias can be large with practical sample sizes.

If the estimated tail shape parameter k exceeds 0.5, the user should be warned, although in practice we have observed good performance for values of k up to 0.7. (Note: If k is greater than 0.5 then WAIC is also likely to fail, but WAIC lacks its own diagnostic.) When using PSIS in the context of approximate LOO-CV, then even if the PSIS estimate has a finite variance the user should consider sampling directly from p(θs|y−i) for any problematic observations i, use k-fold cross-validation, or use a more robust model. Importance sampling is likely to work less well if the marginal posterior p(θs|y) and LOO posterior p(θs|y−i) are much different, which is more likely to happen with a non-robust model and highly influential observation

**Effective sample size and error estimates**

In the case that we obtain the samples from the proposal distribution via MCMC the loo package also computes estimates for the Monte Carlo error and the effective sample size for importance sampling, which are more accurate for PSIS than for IS and TIS (see Vehtari et al (2017b) for details). However, the PSIS effective sample size estimate will be over-optimistic when the estimate of k is greater than 0.7.

**References**

**Vehtari, A., Gelman, A., and Gabry, J. (2017a).** *Practical Bayesian model evaluation using leave-one-out cross-validation and WAIC. Statistics and Computing. 27(5), 1413--1432. doi:10.1007/s11222-016-9696-4 (journal version, preprint arXiv:1507.04544)*

**Vehtari, A., Gelman, A., and Gabry, J. (2017b).** *Pareto smoothed importance sampling. preprint arXiv:1507.02646*

### Cross-validation checking

We can use Pareto-smoothed importance sampling LOO CV as model checking tool (2017b)

- First, create a loo object

- Returns an object of class *pareto_k_table*, which is a matrix with columns "Count", "Proportion", and "Min. n_eff", and has its own print method

`library(loo)`

`plot(loo, diagnostic = c("k", "n_eff"), label_points = FALSE, main = "PSIS diagnostic plot")`

`loo`

```
Computed from 4000 by 8846 log-likelihood matrix

         Estimate    SE
elpd_loo -71952.3 136.1
p_loo        48.4   2.0
looic    143904.7 272.1
------
Monte Carlo SE of elpd_loo is 0.1.

All Pareto k estimates are good (k < 0.5).
See help('pareto-k-diagnostic') for details.
```
`plot(loo, diagnostic = c("k", "n_eff"), label_points = FALSE, main = "PSIS diagnostic plot")`

![M2_sloo](/Plots/M2_sloo.png "M2_sloo")

There are no highly influential observations, which indicates that the model is correct (i.e. there is no misspecification, Vehtari, Gelman and Gabry, 2017).


### Check what would CV say about relevance of covariates

We form 3 models by dropping each of the covariates out

### Posterior predictive checking

In addition to LOO CV, posterior predictive checks can often detect problems and also provide more information about the reason. Here we test the proportion of 1's predicted by the model and compare them to the observed number of 1's.

`prop_zero <- function(y) mean(y == 0)`

`(prop_zero_test1 <- pp_check(m2, plotfun = "stat", stat = "prop_zero"))`

![M2_s4](/Plots/M2_s4.png "M2_s4")


`library(loo)`

`pareto_k_table(loo)`   

```
All Pareto k estimates are good (k < 0.5)
```
- Returns an integer vector indicating which observations have Pareto k estimates above threshold

`pareto_k_ids(loo, threshold = 0.5)`

- Returns a vector of the estimated Pareto k parameters

`pareto_k_values(loo)`

- Returns a vector of the estimated PSIS effective sample sizes

`psis_n_eff_values(loo)`

- Returns the Monte Carlo standard error (MCSE) estimate for PSIS-LOO. MCSE will be NA if any Pareto k values are above threshold

`mcse_loo(loo, threshold = 0.7)`

```
[1] 0.110971542634
```

### Analyse posterior

#### Plot posterior

- Including random effects from *fi_fishid* 

`mcmc_areas(as.matrix(m2), prob_outer = .999)`

![M2_s5](/Plots/M2_s5.png "M2_s5")








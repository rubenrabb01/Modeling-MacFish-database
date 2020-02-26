## Bayesian mixed-effects model fitted with stan 

*2-level Longitudinal conditional growth model*

- Random intercept model + dropping random slope / Random Intercept with One Fixed Level-1 Factor (Non-Random Slope) 

`m2<-stan_lmer(formula= ranged2d ~ 1 + season*fi_species +(1 | fi_fishid), data = mean.ranged2d, seed=350)`

### Results for m2 fit

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

Auxiliary parameter(s):
      Median MAD_SD
sigma 13.67   0.10 

Error terms:
 Groups    Name        Std.Dev.
 fi_fishid (Intercept)  6.93   
 Residual              13.67   
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
 formula:      sqrt(ranged2d + 1) ~ 1 + season * fi_species + (1 | fi_fishid)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
                                           mean   sd    10%   50%   90%
(Intercept)                               26.6    2.2  23.7  26.5  29.4
seasonspring_I                            -1.8    0.8  -2.9  -1.8  -0.7
seasonspring_II                           14.6    1.7  12.4  14.6  16.9
seasonsummer                              -3.3    0.7  -4.2  -3.3  -2.4
seasonwinter                               3.7    0.8   2.7   3.7   4.6
fi_speciespikeperch                       -7.6    3.4 -11.9  -7.7  -3.2
fi_specieswels                             0.2    3.0  -3.5   0.2   3.9
seasonspring_I:fi_speciespikeperch         7.6    1.3   5.9   7.6   9.2
seasonspring_II:fi_speciespikeperch      -13.7    2.5 -16.9 -13.6 -10.5
seasonsummer:fi_speciespikeperch          13.0    1.1  11.6  13.0  14.4
seasonwinter:fi_speciespikeperch          -3.5    1.2  -5.0  -3.5  -2.0
seasonspring_I:fi_specieswels              6.8    1.1   5.5   6.8   8.1
seasonspring_II:fi_specieswels           -10.2    2.2 -13.0 -10.2  -7.5
seasonsummer:fi_specieswels                4.3    0.9   3.2   4.3   5.4
seasonwinter:fi_specieswels               -2.9    1.0  -4.2  -2.9  -1.7
b[(Intercept) fi_fishid:T449202_1]        -1.0    2.7  -4.5  -0.9   2.2
b[(Intercept) fi_fishid:T449203_1]         6.1    2.3   3.2   6.1   9.1
b[(Intercept) fi_fishid:T449204_1]        -2.1    2.5  -5.2  -2.1   1.1
b[(Intercept) fi_fishid:T449207_1]        -8.2    2.3 -11.2  -8.2  -5.3
b[(Intercept) fi_fishid:T449208_1]       -10.0    2.7 -13.5  -9.9  -6.9
b[(Intercept) fi_fishid:T449209_1]        -0.5    2.3  -3.4  -0.5   2.5
b[(Intercept) fi_fishid:T449213_1]        10.4    2.7   7.0  10.5  13.6
b[(Intercept) fi_fishid:T449215_1]         6.4    2.7   2.9   6.5   9.8
b[(Intercept) fi_fishid:T449268_1]         6.7    1.9   4.2   6.7   9.1
b[(Intercept) fi_fishid:T449269_1]         4.8    2.0   2.2   4.8   7.3
b[(Intercept) fi_fishid:T449270_2]         5.5    2.4   2.5   5.5   8.5
b[(Intercept) fi_fishid:T449271_1]         0.3    2.3  -2.6   0.3   3.3
b[(Intercept) fi_fishid:T449272_1]        11.3    2.3   8.3  11.3  14.3
b[(Intercept) fi_fishid:T449274_1]         7.5    1.9   5.0   7.5   9.9
b[(Intercept) fi_fishid:T449275_1]         6.1    1.9   3.6   6.1   8.6
b[(Intercept) fi_fishid:T449276_1]         1.8    1.9  -0.7   1.9   4.2
b[(Intercept) fi_fishid:T449277_1]         0.5    2.3  -2.4   0.6   3.5
b[(Intercept) fi_fishid:T449278_1]        -9.8    2.0 -12.3  -9.8  -7.3
b[(Intercept) fi_fishid:T449280_1]        -2.4    2.0  -5.0  -2.5   0.1
b[(Intercept) fi_fishid:T449282_1]       -15.0    2.0 -17.7 -15.0 -12.5
b[(Intercept) fi_fishid:T449284_1]        -5.7    1.9  -8.2  -5.7  -3.3
b[(Intercept) fi_fishid:T449285_1]        -6.0    2.4  -9.1  -6.0  -2.9
b[(Intercept) fi_fishid:T449286_1]        -1.5    2.0  -4.0  -1.5   1.0
b[(Intercept) fi_fishid:T449287_1]        -7.0    2.3 -10.0  -7.0  -4.0
b[(Intercept) fi_fishid:T449288_1]         3.5    2.0   0.9   3.5   6.0
b[(Intercept) fi_fishid:T449310_1]         1.1    2.7  -2.3   1.2   4.3
b[(Intercept) fi_fishid:T449313_1]        -0.8    2.6  -4.2  -0.6   2.5
b[(Intercept) fi_fishid:T449314_1]        -1.3    2.0  -3.9  -1.3   1.1
b[(Intercept) fi_fishid:T449317_1]        -6.1    2.7  -9.7  -6.0  -2.7
b[(Intercept) fi_fishid:T449318_1]         1.3    2.0  -1.2   1.4   3.9
b[(Intercept) fi_fishid:T449319_1]         4.3    1.9   1.8   4.3   6.7
sigma                                     13.7    0.1  13.5  13.7  13.8
Sigma[fi_fishid:(Intercept),(Intercept)]  48.1   14.2  32.5  45.5  67.0

Fit Diagnostics:
           mean   sd   10%   50%   90%
mean_PPD 26.9    0.2 26.7  26.9  27.2 

The mean_ppd is the sample average posterior predictive distribution of the outcome variable (for details see help('summary.stanreg')).

MCMC diagnostics
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

### Check priors used 

`prior_summary(object = m2)`

```{r, echo=FALSE, eval=TRUE}

Priors for model 'm2' 
------
Intercept (after predictors centered)
  Specified prior:
    ~ normal(location = 0, scale = 10)
  Adjusted prior:
    ~ normal(location = 0, scale = 155)

Coefficients
  Specified prior:
    ~ normal(location = [0,0,0,...], scale = [2.5,2.5,2.5,...])
  Adjusted prior:
    ~ normal(location = [0,0,0,...], scale = [38.65,38.65,38.65,...])

Auxiliary (sigma)
  Specified prior:
    ~ exponential(rate = 1)
  Adjusted prior:
    ~ exponential(rate = 0.065)

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
 formula:      sqrt(ranged2d + 1) ~ 1 + season * fi_species + (1 | fi_fishid)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
                                           mean   sd    2.5%   97.5%
(Intercept)                              26.57   2.25 22.14  31.07  
sigma                                    13.67   0.10 13.47  13.87  
Sigma[fi_fishid:(Intercept),(Intercept)] 48.08  14.24 28.00  82.63  

MCMC diagnostics
                                         mcse Rhat n_eff
(Intercept)                              0.09 1.01  663 
sigma                                    0.00 1.00 3728 
Sigma[fi_fishid:(Intercept),(Intercept)] 0.45 1.00 1010 

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

```b[(Intercept) fi_fishid:T449202_1] b[(Intercept) fi_fishid:T449203_1] b[(Intercept) fi_fishid:T449204_1] b[(Intercept) fi_fishid:T449207_1] 
                          25.57162                           32.69192                           24.48862                           18.34202 
b[(Intercept) fi_fishid:T449208_1] b[(Intercept) fi_fishid:T449209_1] b[(Intercept) fi_fishid:T449213_1] b[(Intercept) fi_fishid:T449215_1] 
                          16.52584                           26.12149                           36.98002                           32.99841 
b[(Intercept) fi_fishid:T449268_1] b[(Intercept) fi_fishid:T449269_1] b[(Intercept) fi_fishid:T449270_2] b[(Intercept) fi_fishid:T449271_1] 
                          33.23976                           31.35621                           32.03577                           26.88533 
b[(Intercept) fi_fishid:T449272_1] b[(Intercept) fi_fishid:T449274_1] b[(Intercept) fi_fishid:T449275_1] b[(Intercept) fi_fishid:T449276_1] 
                          37.86413                           34.02502                           32.69888                           28.40233 
b[(Intercept) fi_fishid:T449277_1] b[(Intercept) fi_fishid:T449278_1] b[(Intercept) fi_fishid:T449280_1] b[(Intercept) fi_fishid:T449282_1] 
                          27.11228                           16.78636                           24.12406                           11.53634 
b[(Intercept) fi_fishid:T449284_1] b[(Intercept) fi_fishid:T449285_1] b[(Intercept) fi_fishid:T449286_1] b[(Intercept) fi_fishid:T449287_1] 
                          20.88890                           20.58268                           25.07346                           19.55353 
b[(Intercept) fi_fishid:T449288_1] b[(Intercept) fi_fishid:T449310_1] b[(Intercept) fi_fishid:T449313_1] b[(Intercept) fi_fishid:T449314_1] 
                          30.04009                           27.68933                           25.80439                           25.24224 
b[(Intercept) fi_fishid:T449317_1] b[(Intercept) fi_fishid:T449318_1] b[(Intercept) fi_fishid:T449319_1] 
                          20.43668                           27.89507                           30.86861 
```       
      
- Posterior SD of each alpha  

`a_sd <- apply(X = fish_inter, MARGIN = 2, FUN = sd)`

`a_sd`

```
b[(Intercept) fi_fishid:T449202_1] b[(Intercept) fi_fishid:T449203_1] b[(Intercept) fi_fishid:T449204_1] b[(Intercept) fi_fishid:T449207_1] 
                         3.3949620                          0.8856418                          1.3731967                          0.8760182 
b[(Intercept) fi_fishid:T449208_1] b[(Intercept) fi_fishid:T449209_1] b[(Intercept) fi_fishid:T449213_1] b[(Intercept) fi_fishid:T449215_1] 
                         3.4089368                          0.8513227                          3.3955890                          3.4656475 
b[(Intercept) fi_fishid:T449268_1] b[(Intercept) fi_fishid:T449269_1] b[(Intercept) fi_fishid:T449270_2] b[(Intercept) fi_fishid:T449271_1] 
                         3.0498827                          3.0584610                          1.0028200                          0.9144566 
b[(Intercept) fi_fishid:T449272_1] b[(Intercept) fi_fishid:T449274_1] b[(Intercept) fi_fishid:T449275_1] b[(Intercept) fi_fishid:T449276_1] 
                         0.9101332                          3.0340077                          3.0453675                          3.0371532 
b[(Intercept) fi_fishid:T449277_1] b[(Intercept) fi_fishid:T449278_1] b[(Intercept) fi_fishid:T449280_1] b[(Intercept) fi_fishid:T449282_1] 
                         0.8751876                          3.0330799                          3.0493375                          3.0783485 
b[(Intercept) fi_fishid:T449284_1] b[(Intercept) fi_fishid:T449285_1] b[(Intercept) fi_fishid:T449286_1] b[(Intercept) fi_fishid:T449287_1] 
                         3.0435887                          1.1676847                          3.0526302                          0.9049456 
b[(Intercept) fi_fishid:T449288_1] b[(Intercept) fi_fishid:T449310_1] b[(Intercept) fi_fishid:T449313_1] b[(Intercept) fi_fishid:T449314_1] 
                         3.0841526                          3.4016266                          3.3867785                          3.0524458 
b[(Intercept) fi_fishid:T449317_1] b[(Intercept) fi_fishid:T449318_1] b[(Intercept) fi_fishid:T449319_1] 
                         3.4576413                          3.0742162                          3.0192146 

```

- Posterior median and 95% credible interval 

`a_quant <- apply(X = fish_inter, MARGIN = 2, FUN = quantile, probs = c(0.025, 0.50, 0.975))`
                 
`a_quant <- data.frame(t(a_quant))`

`a_quant`

```
                                       Q2.5      Q50    Q97.5
b[(Intercept) fi_fishid:T449202_1] 18.786492 25.64586 32.21240
b[(Intercept) fi_fishid:T449203_1] 30.968932 32.68488 34.37875
b[(Intercept) fi_fishid:T449204_1] 21.854413 24.48542 27.29991
b[(Intercept) fi_fishid:T449207_1] 16.569753 18.36722 20.07356
b[(Intercept) fi_fishid:T449208_1]  9.533023 16.58894 23.24038
b[(Intercept) fi_fishid:T449209_1] 24.440893 26.11920 27.79348
b[(Intercept) fi_fishid:T449213_1] 30.199911 37.02422 43.59982
b[(Intercept) fi_fishid:T449215_1] 26.111198 33.09104 39.78390
b[(Intercept) fi_fishid:T449268_1] 27.209116 33.24243 39.49058
b[(Intercept) fi_fishid:T449269_1] 25.379257 31.37731 37.40663
b[(Intercept) fi_fishid:T449270_2] 30.122649 32.01582 34.05571
b[(Intercept) fi_fishid:T449271_1] 25.082376 26.88198 28.69621
b[(Intercept) fi_fishid:T449272_1] 36.124887 37.85047 39.66779
b[(Intercept) fi_fishid:T449274_1] 28.052893 34.03260 40.31389
b[(Intercept) fi_fishid:T449275_1] 26.664900 32.67339 38.94626
b[(Intercept) fi_fishid:T449276_1] 22.360119 28.44338 34.49349
b[(Intercept) fi_fishid:T449277_1] 25.366254 27.11441 28.83569
b[(Intercept) fi_fishid:T449278_1] 10.783262 16.78165 22.72761
b[(Intercept) fi_fishid:T449280_1] 18.029573 24.10573 30.22800
b[(Intercept) fi_fishid:T449282_1]  5.426500 11.54105 17.80320
b[(Intercept) fi_fishid:T449284_1] 14.920524 20.86228 27.04462
b[(Intercept) fi_fishid:T449285_1] 18.233357 20.58904 22.91009
b[(Intercept) fi_fishid:T449286_1] 19.058784 25.09037 31.14333
b[(Intercept) fi_fishid:T449287_1] 17.809102 19.54287 21.29708
b[(Intercept) fi_fishid:T449288_1] 23.875974 30.09545 36.23062
b[(Intercept) fi_fishid:T449310_1] 20.972183 27.74927 34.38901
b[(Intercept) fi_fishid:T449313_1] 18.991607 25.82146 32.43882
b[(Intercept) fi_fishid:T449314_1] 19.159665 25.23451 31.37988
b[(Intercept) fi_fishid:T449317_1] 13.586034 20.48557 27.27263
b[(Intercept) fi_fishid:T449318_1] 21.795274 27.85288 34.07173
b[(Intercept) fi_fishid:T449319_1] 24.816963 30.86029 36.99222

```

`names(a_quant) <- c("Q2.5", "Q50", "Q97.5")`

- Combine summary statistics of posterior simulation draws

`a_df <- data.frame(a_mean, a_sd, a_quant)`

`round(head(a_df), 2)`

```
                                   a_mean a_sd  Q2.5   Q50 Q97.5 a_rank
b[(Intercept) fi_fishid:T449282_1]  11.54 3.08  5.43 11.54 17.80      1
b[(Intercept) fi_fishid:T449208_1]  16.53 3.41  9.53 16.59 23.24      2
b[(Intercept) fi_fishid:T449278_1]  16.79 3.03 10.78 16.78 22.73      3
b[(Intercept) fi_fishid:T449207_1]  18.34 0.88 16.57 18.37 20.07      4
b[(Intercept) fi_fishid:T449287_1]  19.55 0.90 17.81 19.54 21.30      5
b[(Intercept) fi_fishid:T449317_1]  20.44 3.46 13.59 20.49 27.27      6

```

### Plots

#### Caterpillar plot to show the fully Bayes estimates for the fi_fishid varying intercepts in rank order together with their 95% credible interval

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

  - The difference between the two fish averages (fish #21 and #29)

`fish_diff <- a_sims[, 21] - a_sims[, 29]`


- We can investigate the posterior distribution of the difference with descriptive statistics and a histogram as follows:

 - Investigate differences of two distributions

`mean <- mean(fish_diff)`
`sd <- sd(fish_diff)`
`quantile <- quantile(fish_diff, probs = c(0.025, 0.50, 0.975))`
`quantile <- data.frame(t(quantile))`
`names(quantile) <- c("Q2.5", "Q50", "Q97.5")`
`diff_df <- data.frame(mean, sd, quantile)`
`round(diff_df, 2)`

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
  geom_text(aes(0.45, 20, label = "mean = 0.45"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 3.39"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`
  
```

### Diagnostics for Pareto smoothed importance sampling (PSIS)

Print a diagnostic table summarizing the estimated Pareto shape parameters and PSIS effective sample sizes, find the indexes of observations for which the estimated Pareto shape parameter k is larger than some threshold value, or plot observation indexes vs. diagnostic estimates. The Details section below provides a brief overview of the diagnostics, but we recommend consulting Vehtari, Gelman, and Gabry (2017a, 2017b) for full details.

The reliability and approximate convergence rate of the PSIS-based estimates can be assessed using the estimates for the shape parameter k of the generalized Pareto distribution:

If k<0.5 then the distribution of raw importance ratios has finite variance and the central limit theorem holds. However, as k approaches 0.5 the RMSE of plain importance sampling (IS) increases significantly while PSIS has lower RMSE.

If 0.5≤k<1 then the variance of the raw importance ratios is infinite, but the mean exists. TIS and PSIS estimates have finite variance by accepting some bias. The convergence of the estimate is slower with increasing k. If k is between 0.5 and approximately 0.7 then we observe practically useful convergence rates and Monte Carlo error estimates with PSIS (the bias of TIS increases faster than the bias of PSIS). If k>0.7 we observe impractical convergence rates and unreliable Monte Carlo error estimates.

If k≥1 then neither the variance nor the mean of the raw importance ratios exists. The convergence rate is close to zero and bias can be large with practical sample sizes.

If the estimated tail shape parameter k exceeds 0.5, the user should be warned, although in practice we have observed good performance for values of k up to 0.7. (Note: If k is greater than 0.5 then WAIC is also likely to fail, but WAIC lacks its own diagnostic.) When using PSIS in the context of approximate LOO-CV, then even if the PSIS estimate has a finite variance the user should consider sampling directly from p(θs|y−i) for any problematic observations i, use k-fold cross-validation, or use a more robust model. Importance sampling is likely to work less well if the marginal posterior p(θs|y) and LOO posterior p(θs|y−i) are much different, which is more likely to happen with a non-robust model and highly influential observation


- First, create a loo object

`loo<-loo(m2, cores = getOption("mc.cores", 4))`


- Returns an object of class *pareto_k_table*, which is a matrix with columns "Count", "Proportion", and "Min. n_eff", and has its own print method

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
[1] 0.1016874
```

# S3 method for psis_loo

`plot(loo, diagnostic = c("k", "n_eff"), label_points = FALSE, main = "PSIS diagnostic plot")`

![M2_s2](/Plots/M2_s2.png "M2_s2")


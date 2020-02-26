### Bayesian mixed-effects model fitted with stan 

`m1<-stan_glm(formula= sqrt(ranged2d + 1) ~ 1 + season*fi_species, data = mean.ranged2d, seed=350)`

### Results for m1 fit

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
Chain 1:  Elapsed Time: 0.371 seconds (Warm-up)
Chain 1:                1.681 seconds (Sampling)
Chain 1:                2.052 seconds (Total)
Chain 1: 

SAMPLING FOR MODEL 'continuous' NOW (CHAIN 2).
Chain 2: 
Chain 2: Gradient evaluation took 0 seconds
Chain 2: 1000 transitions using 10 leapfrog steps per transition would take 0 seconds.
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
Chain 2:  Elapsed Time: 0.349 seconds (Warm-up)
Chain 2:                1.526 seconds (Sampling)
Chain 2:                1.875 seconds (Total)
Chain 2: 

SAMPLING FOR MODEL 'continuous' NOW (CHAIN 3).
Chain 3: 
Chain 3: Gradient evaluation took 0 seconds
Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 0 seconds.
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
Chain 3:  Elapsed Time: 0.344 seconds (Warm-up)
Chain 3:                1.61 seconds (Sampling)
Chain 3:                1.954 seconds (Total)
Chain 3: 

SAMPLING FOR MODEL 'continuous' NOW (CHAIN 4).
Chain 4: 
Chain 4: Gradient evaluation took 0 seconds
Chain 4: 1000 transitions using 10 leapfrog steps per transition would take 0 seconds.
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
Chain 4:  Elapsed Time: 0.404 seconds (Warm-up)
Chain 4:                1.65 seconds (Sampling)
Chain 4:                2.054 seconds (Total)
Chain 4: 

```

`print(m1, digits = 2)`

``` 
stan_glm
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + season * fi_species
 observations: 8846
 predictors:   15
------
                                    Median MAD_SD
(Intercept)                          27.24   0.55
seasonspring_I                       -2.74   0.90
seasonspring_II                      13.15   1.92
seasonsummer                         -4.33   0.75
seasonwinter                          3.97   0.82
fi_speciespikeperch                  -8.40   0.89
fi_specieswels                       -0.13   0.72
seasonspring_I:fi_speciespikeperch    8.33   1.39
seasonspring_II:fi_speciespikeperch -12.48   2.74
seasonsummer:fi_speciespikeperch     14.09   1.19
seasonwinter:fi_speciespikeperch     -3.73   1.31
seasonspring_I:fi_specieswels         7.40   1.15
seasonspring_II:fi_specieswels       -6.26   2.42
seasonsummer:fi_specieswels           5.14   0.96
seasonwinter:fi_specieswels          -2.17   1.05

Auxiliary parameter(s):
      Median MAD_SD
sigma 14.99   0.11 

------
* For help interpreting the printed output see ?print.stanreg
* For info on the priors used see ?prior_summary.stanreg
```

`summary(m1)`

```
Model Info:
 function:     stan_glm
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + season * fi_species
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 predictors:   15

Estimates:
                                      mean   sd    10%   50%   90%
(Intercept)                          27.2    0.6  26.5  27.2  28.0
seasonspring_I                       -2.7    0.9  -3.9  -2.7  -1.6
seasonspring_II                      13.1    1.9  10.7  13.2  15.6
seasonsummer                         -4.3    0.8  -5.3  -4.3  -3.3
seasonwinter                          4.0    0.8   2.9   4.0   5.0
fi_speciespikeperch                  -8.4    0.9  -9.5  -8.4  -7.3
fi_specieswels                       -0.1    0.7  -1.0  -0.1   0.8
seasonspring_I:fi_speciespikeperch    8.3    1.4   6.6   8.3  10.2
seasonspring_II:fi_speciespikeperch -12.5    2.8 -16.0 -12.5  -8.8
seasonsummer:fi_speciespikeperch     14.1    1.2  12.6  14.1  15.6
seasonwinter:fi_speciespikeperch     -3.7    1.3  -5.4  -3.7  -2.0
seasonspring_I:fi_specieswels         7.4    1.2   5.9   7.4   8.9
seasonspring_II:fi_specieswels       -6.3    2.5  -9.4  -6.3  -3.2
seasonsummer:fi_specieswels           5.1    1.0   3.8   5.1   6.4
seasonwinter:fi_specieswels          -2.2    1.1  -3.5  -2.2  -0.8
sigma                                15.0    0.1  14.8  15.0  15.1

Fit Diagnostics:
           mean   sd   10%   50%   90%
mean_PPD 26.9    0.2 26.6  26.9  27.2 

The mean_ppd is the sample average posterior predictive distribution of the outcome variable (for details see help('summary.stanreg')).

MCMC diagnostics
                                    mcse Rhat n_eff
(Intercept)                         0.0  1.0  1806 
seasonspring_I                      0.0  1.0  1995 
seasonspring_II                     0.0  1.0  2412 
seasonsummer                        0.0  1.0  1953 
seasonwinter                        0.0  1.0  2103 
fi_speciespikeperch                 0.0  1.0  1841 
fi_specieswels                      0.0  1.0  1797 
seasonspring_I:fi_speciespikeperch  0.0  1.0  2260 
seasonspring_II:fi_speciespikeperch 0.1  1.0  2483 
seasonsummer:fi_speciespikeperch    0.0  1.0  2149 
seasonwinter:fi_speciespikeperch    0.0  1.0  1920 
seasonspring_I:fi_specieswels       0.0  1.0  2117 
seasonspring_II:fi_specieswels      0.0  1.0  2558 
seasonsummer:fi_specieswels         0.0  1.0  1979 
seasonwinter:fi_specieswels         0.0  1.0  2202 
sigma                               0.0  1.0  4809 
mean_PPD                            0.0  1.0  4093 
log-posterior                       0.1  1.0  1773 

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).
```

`prior_summary(object = m1)`

```
Priors for model 'm1' 
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
------
See help('prior_summary.stanreg') for more details
```

`summary(m1, pars = c("(Intercept)", "sigma", "Sigma[fi_fishid:(Intercept),(Intercept)]"), probs = c(0.025, 0.975), digits = 2)`

```
Model Info:
 function:     stan_glm
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + season * fi_species
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 predictors:   15

Estimates:
              mean   sd    2.5%   97.5%
(Intercept) 27.24   0.56 26.13  28.35  
sigma       14.99   0.11 14.76  15.21  

MCMC diagnostics
            mcse Rhat n_eff
(Intercept) 0.01 1.00 1806 
sigma       0.00 1.00 4809 

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).
```

`sims <- as.matrix(m1)`

`dim(sims)`

``` 
[1] 4000   16

para_name <- colnames(sims)
para_name
[1] "(Intercept)"                         "seasonspring_I"                      "seasonspring_II"                     "seasonsummer"                       
[5] "seasonwinter"                        "fi_speciespikeperch"                 "fi_specieswels"                      "seasonspring_I:fi_speciespikeperch" 
[9] "seasonspring_II:fi_speciespikeperch" "seasonsummer:fi_speciespikeperch"    "seasonwinter:fi_speciespikeperch"    "seasonspring_I:fi_specieswels"      
[13] "seasonspring_II:fi_specieswels"      "seasonsummer:fi_specieswels"         "seasonwinter:fi_specieswels"         "sigma"           ```                

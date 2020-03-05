`m1b<-stan_lmer(formula= sqrt(ranged2d + 1) ~ 1 + season + (1 | fi_fishid), data = mean.ranged2d, seed=350)`

### Fitted Model 3 results

```
SAMPLING FOR MODEL 'continuous' NOW (CHAIN 1).
Chain 1:
Chain 1: Gradient evaluation took 0.001 seconds
Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 10 seconds.
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
Chain 1:  Elapsed Time: 46.753 seconds (Warm-up)
Chain 1:                35.788 seconds (Sampling)
Chain 1:                82.541 seconds (Total)
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
Chain 2:  Elapsed Time: 47.561 seconds (Warm-up)
Chain 2:                40.349 seconds (Sampling)
Chain 2:                87.91 seconds (Total)
Chain 2:

SAMPLING FOR MODEL 'continuous' NOW (CHAIN 3).
Chain 3:
Chain 3: Gradient evaluation took 0.001 seconds
Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 10 seconds.
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
Chain 3:  Elapsed Time: 40.583 seconds (Warm-up)
Chain 3:                40.342 seconds (Sampling)
Chain 3:                80.925 seconds (Total)
Chain 3:

SAMPLING FOR MODEL 'continuous' NOW (CHAIN 4).
Chain 4:
Chain 4: Gradient evaluation took 0.002 seconds
Chain 4: 1000 transitions using 10 leapfrog steps per transition would take 20 seconds.
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
Chain 4:  Elapsed Time: 55.284 seconds (Warm-up)
Chain 4:                43.243 seconds (Sampling)
Chain 4:                98.527 seconds (Total)
Chain 4:
Warning messages:
1: Bulk Effective Samples Size (ESS) is too low, indicating posterior means and medians may be unreliable.
Running the chains for more iterations may help. See
http://mc-stan.org/misc/warnings.html#bulk-ess
2: Tail Effective Samples Size (ESS) is too low, indicating posterior variances and tail quantiles may be unreliable.
Running the chains for more iterations may help. See
http://mc-stan.org/misc/warnings.html#tail-ess
```

### Quick Summary

`print(m1b, digits = 2)`

```
stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ season + (1 | fi_fishid)
 observations: 8846
------
                Median MAD_SD
(Intercept)     25.13   1.28
seasonspring_I   3.12   0.46
seasonspring_II  6.32   0.96
seasonsummer     1.63   0.40
seasonwinter     1.47   0.43

Auxiliary parameter(s):
      Median MAD_SD
sigma 13.91   0.10

Error terms:
 Groups    Name        Std.Dev.
 fi_fishid (Intercept)  7.16
 Residual              13.91
Num. levels: fi_fishid 31

------
* For help interpreting the printed output see ?print.stanreg
* For info on the priors used see ?prior_summary.stanreg
```

`summary(m1b)`

```
Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ season + (1 | fi_fishid)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
                                           mean   sd    10%   50%   90%
(Intercept)                               25.2    1.4  23.5  25.1  26.9
seasonspring_I                             3.1    0.5   2.5   3.1   3.7
seasonspring_II                            6.3    0.9   5.1   6.3   7.6
seasonsummer                               1.6    0.4   1.1   1.6   2.1
seasonwinter                               1.5    0.4   0.9   1.5   2.0
b[(Intercept) fi_fishid:1]                -2.5    1.5  -4.4  -2.5  -0.6
b[(Intercept) fi_fishid:2]                -7.0    1.7  -9.1  -6.9  -4.8
b[(Intercept) fi_fishid:3]                -5.3    1.5  -7.2  -5.3  -3.4
b[(Intercept) fi_fishid:4]                -7.6    1.5  -9.5  -7.6  -5.7
b[(Intercept) fi_fishid:5]                -7.9    1.6  -9.9  -7.9  -5.8
b[(Intercept) fi_fishid:6]                -4.1    1.6  -6.1  -4.1  -2.1
b[(Intercept) fi_fishid:7]                 5.8    1.7   3.7   5.9   8.0
b[(Intercept) fi_fishid:8]               -14.4    1.5 -16.3 -14.4 -12.5
b[(Intercept) fi_fishid:9]                 6.1    1.5   4.2   6.1   8.0
b[(Intercept) fi_fishid:10]                0.6    1.5  -1.3   0.6   2.6
b[(Intercept) fi_fishid:11]                0.0    1.5  -2.0   0.0   1.8
b[(Intercept) fi_fishid:12]               -5.5    1.8  -7.8  -5.5  -3.3
b[(Intercept) fi_fishid:13]                6.4    1.6   4.3   6.4   8.4
b[(Intercept) fi_fishid:14]               -8.4    1.7 -10.5  -8.4  -6.3
b[(Intercept) fi_fishid:15]               10.7    1.5   8.8  10.7  12.6
b[(Intercept) fi_fishid:16]                7.6    1.5   5.6   7.6   9.4
b[(Intercept) fi_fishid:17]                6.3    1.5   4.3   6.4   8.2
b[(Intercept) fi_fishid:18]               -0.5    1.6  -2.5  -0.5   1.5
b[(Intercept) fi_fishid:19]                3.3    1.5   1.4   3.3   5.1
b[(Intercept) fi_fishid:20]                3.0    1.5   1.0   3.0   4.9
b[(Intercept) fi_fishid:21]                5.2    1.6   3.3   5.2   7.3
b[(Intercept) fi_fishid:22]                5.8    1.5   3.9   5.8   7.7
b[(Intercept) fi_fishid:23]               -4.2    1.5  -6.1  -4.2  -2.3
b[(Intercept) fi_fishid:24]                0.2    1.5  -1.8   0.2   2.1
b[(Intercept) fi_fishid:25]               -8.2    1.5 -10.1  -8.1  -6.3
b[(Intercept) fi_fishid:26]                0.1    1.5  -1.7   0.1   2.0
b[(Intercept) fi_fishid:27]                6.7    1.5   4.8   6.7   8.7
b[(Intercept) fi_fishid:28]               -0.9    1.5  -2.8  -0.9   1.0
b[(Intercept) fi_fishid:29]              -13.2    1.6 -15.3 -13.2 -11.2
b[(Intercept) fi_fishid:30]                8.1    1.5   6.2   8.1  10.0
b[(Intercept) fi_fishid:31]                8.9    1.5   7.1   8.9  10.7
sigma                                     13.9    0.1  13.8  13.9  14.0
Sigma[fi_fishid:(Intercept),(Intercept)]  51.3   13.8  35.6  49.1  70.2

Fit Diagnostics:
           mean   sd   10%   50%   90%
mean_PPD 26.9    0.2 26.7  26.9  27.2

The mean_ppd is the sample average posterior predictive distribution of the outcome variable (for details see help('summary.stanreg')).

MCMC diagnostics
                                         mcse Rhat n_eff
(Intercept)                              0.1  1.0   252
seasonspring_I                           0.0  1.0  2011
seasonspring_II                          0.0  1.0  2571
seasonsummer                             0.0  1.0  2148
seasonwinter                             0.0  1.0  2478
b[(Intercept) fi_fishid:1]               0.1  1.0   302
b[(Intercept) fi_fishid:2]               0.1  1.0   409
b[(Intercept) fi_fishid:3]               0.1  1.0   318
b[(Intercept) fi_fishid:4]               0.1  1.0   320
b[(Intercept) fi_fishid:5]               0.1  1.0   329
b[(Intercept) fi_fishid:6]               0.1  1.0   319
b[(Intercept) fi_fishid:7]               0.1  1.0   399
b[(Intercept) fi_fishid:8]               0.1  1.0   303
b[(Intercept) fi_fishid:9]               0.1  1.0   308
b[(Intercept) fi_fishid:10]              0.1  1.0   313
b[(Intercept) fi_fishid:11]              0.1  1.0   302
b[(Intercept) fi_fishid:12]              0.1  1.0   416
b[(Intercept) fi_fishid:13]              0.1  1.0   360
b[(Intercept) fi_fishid:14]              0.1  1.0   377
b[(Intercept) fi_fishid:15]              0.1  1.0   324
b[(Intercept) fi_fishid:16]              0.1  1.0   311
b[(Intercept) fi_fishid:17]              0.1  1.0   335
b[(Intercept) fi_fishid:18]              0.1  1.0   311
b[(Intercept) fi_fishid:19]              0.1  1.0   307
b[(Intercept) fi_fishid:20]              0.1  1.0   328
b[(Intercept) fi_fishid:21]              0.1  1.0   329
b[(Intercept) fi_fishid:22]              0.1  1.0   314
b[(Intercept) fi_fishid:23]              0.1  1.0   304
b[(Intercept) fi_fishid:24]              0.1  1.0   318
b[(Intercept) fi_fishid:25]              0.1  1.0   300
b[(Intercept) fi_fishid:26]              0.1  1.0   312
b[(Intercept) fi_fishid:27]              0.1  1.0   316
b[(Intercept) fi_fishid:28]              0.1  1.0   324
b[(Intercept) fi_fishid:29]              0.1  1.0   342
b[(Intercept) fi_fishid:30]              0.1  1.0   313
b[(Intercept) fi_fishid:31]              0.1  1.0   318
sigma                                    0.0  1.0  3435
Sigma[fi_fishid:(Intercept),(Intercept)] 0.7  1.0   389
mean_PPD                                 0.0  1.0  3961
log-posterior                            0.3  1.0   494

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).
```

### Check priors used

`prior_summary(object = m1b)`

```{r, echo=FALSE, eval=TRUE}

Priors for model 'm1b'
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

`summary(m1b, pars = c("(Intercept)", "sigma", "Sigma[fi_species:(Intercept),(Intercept)]"), probs = c(0.025, 0.975), digits = 2)`

```

Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ season + (1 | fi_fishid)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
              mean   sd    2.5%   97.5%
(Intercept) 25.16   1.36 22.50  27.94
sigma       13.91   0.10 13.71  14.12

MCMC diagnostics
            mcse Rhat n_eff
(Intercept) 0.09 1.02  252
sigma       0.00 1.00 3435

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).
```

### Extract the posterior draws for all parameters

`sims <- as.matrix(m1b)`

`dim(sims)`

```
[1] 4000   38

```

`para_name <- colnames(sims)`

`para_name`

```
 [1] "(Intercept)"                              "seasonspring_I"
 [3] "seasonspring_II"                          "seasonsummer"
 [5] "seasonwinter"                             "b[(Intercept) fi_fishid:1]"
 [7] "b[(Intercept) fi_fishid:2]"               "b[(Intercept) fi_fishid:3]"
 [9] "b[(Intercept) fi_fishid:4]"               "b[(Intercept) fi_fishid:5]"
[11] "b[(Intercept) fi_fishid:6]"               "b[(Intercept) fi_fishid:7]"
[13] "b[(Intercept) fi_fishid:8]"               "b[(Intercept) fi_fishid:9]"
[15] "b[(Intercept) fi_fishid:10]"              "b[(Intercept) fi_fishid:11]"
[17] "b[(Intercept) fi_fishid:12]"              "b[(Intercept) fi_fishid:13]"
[19] "b[(Intercept) fi_fishid:14]"              "b[(Intercept) fi_fishid:15]"
[21] "b[(Intercept) fi_fishid:16]"              "b[(Intercept) fi_fishid:17]"
[23] "b[(Intercept) fi_fishid:18]"              "b[(Intercept) fi_fishid:19]"
[25] "b[(Intercept) fi_fishid:20]"              "b[(Intercept) fi_fishid:21]"
[27] "b[(Intercept) fi_fishid:22]"              "b[(Intercept) fi_fishid:23]"
[29] "b[(Intercept) fi_fishid:24]"              "b[(Intercept) fi_fishid:25]"
[31] "b[(Intercept) fi_fishid:26]"              "b[(Intercept) fi_fishid:27]"
[33] "b[(Intercept) fi_fishid:28]"              "b[(Intercept) fi_fishid:29]"
[35] "b[(Intercept) fi_fishid:30]"              "b[(Intercept) fi_fishid:31]"
[37] "sigma"                                    "Sigma[fi_fishid:(Intercept),(Intercept)]"
```

- Obtain fi_species-level varying intercept a_j

 - draws for overall mean

`mu_a_sims <- as.matrix(m1b, pars = "(Intercept)")`

 - draws for 3 fi_species-level error

`fish_err <- as.matrix(m1b,regex_pars = "b\\[\\(Intercept\\) fi_species\\:")`

 - draws for 3 species' varying intercepts

`fish_inter <- as.numeric(mu_a_sims) + fish_err`

- Obtain sigma_y and sigma_alpha^2

 -draws for sigma_y

`signma_y <- as.matrix(m1b, pars = "sigma")`

 - draws for sigma_alpha^2

`sigma_alpha <- as.matrix(m1b, pars = "Sigma[fi_species:(Intercept),(Intercept)]")`


### Obtaining means, SD, medians and 95% credible intervals of varying intercepts

In *fish_inter*, we have saved 4,000 posterior draws (from all 4 chains) for the varying intercepts αj of the 30 fishes. For example, the first column of the 4,000 by 30 matrix is a vector of 4,000 posterior simulation draws for the first fish's (*fi_species*:T449202_1) varying intercept α1. One quantitative way to summarize the posterior probability distribution of these 4,000 estimates for α1 is to examine their quantiles.

- Posterior mean of each alpha

`a_mean <- apply(X = fish_inter, MARGIN = 2, FUN = mean)`

`a_mean`

```
 b[(Intercept) fi_fishid:1]  b[(Intercept) fi_fishid:2]  b[(Intercept) fi_fishid:3]  b[(Intercept) fi_fishid:4]
                   22.65611                    18.19944                    19.88525                    17.55894
 b[(Intercept) fi_fishid:5]  b[(Intercept) fi_fishid:6]  b[(Intercept) fi_fishid:7]  b[(Intercept) fi_fishid:8]
                   17.28012                    21.05662                    30.99756                    10.75575
 b[(Intercept) fi_fishid:9] b[(Intercept) fi_fishid:10] b[(Intercept) fi_fishid:11] b[(Intercept) fi_fishid:12]
                   31.27791                    25.78777                    25.12406                    19.66371
b[(Intercept) fi_fishid:13] b[(Intercept) fi_fishid:14] b[(Intercept) fi_fishid:15] b[(Intercept) fi_fishid:16]
                   31.51873                    16.75917                    35.87383                    32.74001
b[(Intercept) fi_fishid:17] b[(Intercept) fi_fishid:18] b[(Intercept) fi_fishid:19] b[(Intercept) fi_fishid:20]
                   31.48980                    24.66353                    28.43429                    28.13015
b[(Intercept) fi_fishid:21] b[(Intercept) fi_fishid:22] b[(Intercept) fi_fishid:23] b[(Intercept) fi_fishid:24]
                   30.38893                    30.98828                    20.95587                    25.31297
b[(Intercept) fi_fishid:25] b[(Intercept) fi_fishid:26] b[(Intercept) fi_fishid:27] b[(Intercept) fi_fishid:28]
                   16.98690                    25.30700                    31.89658                    24.28129
b[(Intercept) fi_fishid:29] b[(Intercept) fi_fishid:30] b[(Intercept) fi_fishid:31]
                   11.92271                    33.27116                    34.05940
```

- Posterior SD of each alpha

`a_sd <- apply(X = fish_inter, MARGIN = 2, FUN = sd)`

`a_sd`

```
 b[(Intercept) fi_fishid:1]  b[(Intercept) fi_fishid:2]  b[(Intercept) fi_fishid:3]  b[(Intercept) fi_fishid:4]
                  0.8263131                   1.1394806                   0.7942932                   0.8005113
 b[(Intercept) fi_fishid:5]  b[(Intercept) fi_fishid:6]  b[(Intercept) fi_fishid:7]  b[(Intercept) fi_fishid:8]
                  0.8885192                   0.8458977                   1.1408305                   0.7903641
 b[(Intercept) fi_fishid:9] b[(Intercept) fi_fishid:10] b[(Intercept) fi_fishid:11] b[(Intercept) fi_fishid:12]
                  0.8015428                   0.8075408                   0.7976819                   1.2810792
b[(Intercept) fi_fishid:13] b[(Intercept) fi_fishid:14] b[(Intercept) fi_fishid:15] b[(Intercept) fi_fishid:16]
                  0.9731794                   1.0685387                   0.8626735                   0.7765670
b[(Intercept) fi_fishid:17] b[(Intercept) fi_fishid:18] b[(Intercept) fi_fishid:19] b[(Intercept) fi_fishid:20]
                  0.8239524                   0.8664141                   0.7767939                   0.8618274
b[(Intercept) fi_fishid:21] b[(Intercept) fi_fishid:22] b[(Intercept) fi_fishid:23] b[(Intercept) fi_fishid:24]
                  0.9153469                   0.8135210                   0.7841159                   0.8707819
b[(Intercept) fi_fishid:25] b[(Intercept) fi_fishid:26] b[(Intercept) fi_fishid:27] b[(Intercept) fi_fishid:28]
                  0.8246331                   0.8006935                   0.8369922                   0.8447457
b[(Intercept) fi_fishid:29] b[(Intercept) fi_fishid:30] b[(Intercept) fi_fishid:31]
                  1.0132123                   0.7893760                   0.8003434
```

- Posterior median and 95% credible interval

`a_quant <- apply(X = fish_inter, MARGIN = 2, FUN = quantile, probs = c(0.025, 0.50, 0.975))`

`a_quant <- data.frame(t(a_quant))`

`a_quant`

```
                                X2.5.     X50.   X97.5.
b[(Intercept) fi_fishid:1]  21.039233 22.66466 24.27110
b[(Intercept) fi_fishid:2]  15.907482 18.22637 20.33842
b[(Intercept) fi_fishid:3]  18.328191 19.86976 21.45812
b[(Intercept) fi_fishid:4]  15.999759 17.56155 19.12451
b[(Intercept) fi_fishid:5]  15.509073 17.27775 19.01179
b[(Intercept) fi_fishid:6]  19.439338 21.05059 22.71890
b[(Intercept) fi_fishid:7]  28.803231 31.00057 33.24051
b[(Intercept) fi_fishid:8]   9.192346 10.76231 12.28624
b[(Intercept) fi_fishid:9]  29.700500 31.29055 32.83017
b[(Intercept) fi_fishid:10] 24.228957 25.77952 27.37900
b[(Intercept) fi_fishid:11] 23.591380 25.11840 26.69825
b[(Intercept) fi_fishid:12] 17.075011 19.65923 22.23857
b[(Intercept) fi_fishid:13] 29.665896 31.50691 33.43216
b[(Intercept) fi_fishid:14] 14.672277 16.76525 18.85662
b[(Intercept) fi_fishid:15] 34.171084 35.87971 37.57096
b[(Intercept) fi_fishid:16] 31.259941 32.74592 34.20499
b[(Intercept) fi_fishid:17] 29.846664 31.50175 33.11319
b[(Intercept) fi_fishid:18] 22.993019 24.66778 26.34030
b[(Intercept) fi_fishid:19] 26.936516 28.42474 29.94146
b[(Intercept) fi_fishid:20] 26.403976 28.11346 29.78938
b[(Intercept) fi_fishid:21] 28.599080 30.39238 32.23709
b[(Intercept) fi_fishid:22] 29.386802 30.97695 32.58063
b[(Intercept) fi_fishid:23] 19.422230 20.96221 22.49107
b[(Intercept) fi_fishid:24] 23.657090 25.30873 27.01251
b[(Intercept) fi_fishid:25] 15.390873 16.97898 18.59500
b[(Intercept) fi_fishid:26] 23.778603 25.29774 26.86074
b[(Intercept) fi_fishid:27] 30.310147 31.88194 33.55023
b[(Intercept) fi_fishid:28] 22.640965 24.29641 25.93419
b[(Intercept) fi_fishid:29]  9.970949 11.91553 13.94426
b[(Intercept) fi_fishid:30] 31.726599 33.28491 34.80199
b[(Intercept) fi_fishid:31] 32.465772 34.06298 35.61404
```

`names(a_quant) <- c("X2.5.", "X50.", "X97.5.")`

- Combine summary statistics of posterior simulation draws

`a_df <- data.frame(a_mean, a_sd, a_quant)`

`round(head(a_df), 2)`

```
                           a_mean a_sd X2.5.  X50. X97.5.
b[(Intercept) fi_fishid:1]  22.66 0.83 21.04 22.66  24.27
b[(Intercept) fi_fishid:2]  18.20 1.14 15.91 18.23  20.34
b[(Intercept) fi_fishid:3]  19.89 0.79 18.33 19.87  21.46
b[(Intercept) fi_fishid:4]  17.56 0.80 16.00 17.56  19.12
b[(Intercept) fi_fishid:5]  17.28 0.89 15.51 17.28  19.01
b[(Intercept) fi_fishid:6]  21.06 0.85 19.44 21.05  22.72

```

### Plots

Caterpillar plot to show the fully Bayes estimates for the *fi_species* varying intercepts in rank order together with their 95% credible interval

- Sort dataframe containing an estimated alpha's mean and sd for every fi_species

`a_df <- a_df[order(a_df$a_mean), ]`

`a_df`
```
                              a_mean      a_sd     X2.5.     X50.   X97.5.
b[(Intercept) fi_fishid:8]  10.75575 0.7903641  9.192346 10.76231 12.28624
b[(Intercept) fi_fishid:29] 11.92271 1.0132123  9.970949 11.91553 13.94426
b[(Intercept) fi_fishid:14] 16.75917 1.0685387 14.672277 16.76525 18.85662
b[(Intercept) fi_fishid:25] 16.98690 0.8246331 15.390873 16.97898 18.59500
b[(Intercept) fi_fishid:5]  17.28012 0.8885192 15.509073 17.27775 19.01179
b[(Intercept) fi_fishid:4]  17.55894 0.8005113 15.999759 17.56155 19.12451
b[(Intercept) fi_fishid:2]  18.19944 1.1394806 15.907482 18.22637 20.33842
b[(Intercept) fi_fishid:12] 19.66371 1.2810792 17.075011 19.65923 22.23857
b[(Intercept) fi_fishid:3]  19.88525 0.7942932 18.328191 19.86976 21.45812
b[(Intercept) fi_fishid:23] 20.95587 0.7841159 19.422230 20.96221 22.49107
b[(Intercept) fi_fishid:6]  21.05662 0.8458977 19.439338 21.05059 22.71890
b[(Intercept) fi_fishid:1]  22.65611 0.8263131 21.039233 22.66466 24.27110
b[(Intercept) fi_fishid:28] 24.28129 0.8447457 22.640965 24.29641 25.93419
b[(Intercept) fi_fishid:18] 24.66353 0.8664141 22.993019 24.66778 26.34030
b[(Intercept) fi_fishid:11] 25.12406 0.7976819 23.591380 25.11840 26.69825
b[(Intercept) fi_fishid:26] 25.30700 0.8006935 23.778603 25.29774 26.86074
b[(Intercept) fi_fishid:24] 25.31297 0.8707819 23.657090 25.30873 27.01251
b[(Intercept) fi_fishid:10] 25.78777 0.8075408 24.228957 25.77952 27.37900
b[(Intercept) fi_fishid:20] 28.13015 0.8618274 26.403976 28.11346 29.78938
b[(Intercept) fi_fishid:19] 28.43429 0.7767939 26.936516 28.42474 29.94146
b[(Intercept) fi_fishid:21] 30.38893 0.9153469 28.599080 30.39238 32.23709
b[(Intercept) fi_fishid:22] 30.98828 0.8135210 29.386802 30.97695 32.58063
b[(Intercept) fi_fishid:7]  30.99756 1.1408305 28.803231 31.00057 33.24051
b[(Intercept) fi_fishid:9]  31.27791 0.8015428 29.700500 31.29055 32.83017
b[(Intercept) fi_fishid:17] 31.48980 0.8239524 29.846664 31.50175 33.11319
b[(Intercept) fi_fishid:13] 31.51873 0.9731794 29.665896 31.50691 33.43216
b[(Intercept) fi_fishid:27] 31.89658 0.8369922 30.310147 31.88194 33.55023
b[(Intercept) fi_fishid:16] 32.74001 0.7765670 31.259941 32.74592 34.20499
b[(Intercept) fi_fishid:30] 33.27116 0.7893760 31.726599 33.28491 34.80199
b[(Intercept) fi_fishid:31] 34.05940 0.8003434 32.465772 34.06298 35.61404
b[(Intercept) fi_fishid:15] 35.87383 0.8626735 34.171084 35.87971 37.57096
```

`a_df$a_rank <- c(1 : dim(a_df)[1])`    # a vector of fi_species rank

- Plot fish-level alphas's posterior mean and 95% credible interval

```
ggplot(data = a_df,
       aes(x = a_rank,
           y = a_mean)) +
  geom_pointrange(aes(ymin = X2.5.,
                      ymax = X97.5.),
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
![M1b_s1](/Plots/M1b_s1.png "M1b_s1")

### Differences between species averages

Here I'll explore the posterior distribution of the differences between each two species with descriptive statistics and histograms

- The difference between the fish 1 and fish 2 averages is:

`fish_diff1 <- fish_inter[, 1] - fish_inter[, 2]`

`mean <- mean(fish_diff1)`
`sd <- sd(fish_diff1)`
`quantile <- quantile(fish_diff1, probs = c(0.025, 0.50, 0.975))`
`quantile <- data.frame(t(quantile))`
`names(quantile) <- c("Q2.5", "Q50", "Q97.5")`
`diff_df1 <- data.frame(mean, sd, quantile)`
`round(diff_df1, 2)`

```
  mean   sd Q2.5  Q50 Q97.5
1 4.46 1.36 1.88 4.44  7.16

```
`ggplot(data = data.frame(fish_diff1),
       aes(x = fish_diff1)) +
  geom_histogram(color = "black",
                 fill = "gray",
                 binwidth = 0.75) +
  scale_x_continuous("Score diffence between fish1 and fish2",
                     breaks = seq(from = -20,
                                  to = 20,
                                  by = 10)) +
  geom_vline(xintercept = c(mean(fish_diff1),
                            quantile(fish_diff1,
                                     probs = c(0.025, 0.975))),
             colour = "red",
             linetype = "longdash") +
  geom_text(aes(0.45, 20, label = "mean = 4.46"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 1.36"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M1b_s2](/Plots/M1b_s2.png "M1b_s2")

The expected difference comes to 3.88 with a standard deviation of 1.34 and a wide range of uncertainty. The 95% credible interval is [1.21,6.5], so we are 95% certain that the true value of the difference between the two species lies within the range, given the data

We also can get the proportion of the time that pike has a higher mean than pikeperch:

`prop.table(table(fish_inter[, 1] > fish_inter[, 2]))`

```
  FALSE    TRUE
0.00075 0.99925
```

This means that the posterior probability that fish1 is better than fish2 is 99.9%. Any pair of fishes within the sample of fishes can be compared in this manner.

- The difference between the fish2 and fish3 averages is:

`fish_diff2 <- fish_inter[, 2] - fish_inter[, 3]`

`mean <- mean(fish_diff2)`
`sd <- sd(fish_diff2)`
`quantile <- quantile(fish_diff2, probs = c(0.025, 0.50, 0.975))`
`quantile <- data.frame(t(quantile))`
`names(quantile) <- c("Q2.5", "Q50", "Q97.5")`
`diff_df2 <- data.frame(mean, sd, quantile)`
`round(diff_df2, 2)`

```
   mean   sd  Q2.5   Q50 Q97.5
1 -1.69 1.36 -4.35 -1.66  1.01

```
`ggplot(data = data.frame(fish_diff2),
       aes(x = fish_diff2)) +
  geom_histogram(color = "black",
                 fill = "gray",
                 binwidth = 0.75) +
  scale_x_continuous("Score diffence between fish2 and fish3",
                     breaks = seq(from = -20,
                                  to = 20,
                                  by = 10)) +
  geom_vline(xintercept = c(mean(fish_diff2),
                            quantile(fish_diff2,
                                     probs = c(0.025, 0.975))),
             colour = "red",
             linetype = "longdash") +
  geom_text(aes(0.45, 20, label = "mean = -1.69"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 1.36"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M1b_s3](/Plots/M1b_s3.png "M1b_s3")

- The difference between the fish1 and wels fish3 is:

`fish_diff3 <- fish_inter[, 1] - fish_inter[, 3]`

`mean <- mean(fish_diff3)`
`sd <- sd(fish_diff3)`
`quantile <- quantile(fish_diff3, probs = c(0.025, 0.50, 0.975))`
`quantile <- data.frame(t(quantile))`
`names(quantile) <- c("Q2.5", "Q50", "Q97.5")`
`diff_df3 <- data.frame(mean, sd, quantile)`
`round(diff_df3, 2)`

```
  mean  sd Q2.5  Q50 Q97.5
1 2.77 1.1 0.62 2.78  4.89

```
`ggplot(data = data.frame(fish_diff3),
       aes(x = fish_diff3)) +
  geom_histogram(color = "black",
                 fill = "gray",
                 binwidth = 0.75) +
  scale_x_continuous("Score diffence between fish1 and fish3",
                     breaks = seq(from = -20,
                                  to = 20,
                                  by = 10)) +
  geom_vline(xintercept = c(mean(fish_diff3),
                            quantile(fish_diff3,
                                     probs = c(0.025, 0.975))),
             colour = "red",
             linetype = "longdash") +
  geom_text(aes(0.45, 20, label = "mean = 2.77"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 1.1"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M1b_s4](/Plots/M1b_s4.png "M1b_s4")


### Analyse posterior

#### Plot posterior

- Including random effects from *fi_species*

`library(bayesplot)`

`mcmc_areas(as.matrix(m1b), prob_outer = .999)`

![M1b_s5](/Plots/M1b_s5.png "M1b_s5")

- Excluding random effects from *fi_species*

`mcmc_areas(as.matrix(m1b), prob_outer = .999, pars = c("(Intercept)",  "b[(Intercept) fi_fishid:1]", "b[(Intercept) fi_fishid:2]","b[(Intercept) fi_fishid:3]",
"b[(Intercept) fi_fishid:4]","b[(Intercept) fi_fishid:5]", "b[(Intercept) fi_fishid:6]", "b[(Intercept) fi_fishid:7]" ,"b[(Intercept) fi_fishid:8]","b[(Intercept) fi_fishid:9]",
"b[(Intercept) fi_fishid:10]","b[(Intercept) fi_fishid:11]", "b[(Intercept) fi_fishid:12]", "b[(Intercept) fi_fishid:13]","b[(Intercept) fi_fishid:14]","b[(Intercept) fi_fishid:15]",
"b[(Intercept) fi_fishid:16]", "b[(Intercept) fi_fishid:17]","b[(Intercept) fi_fishid:18]","b[(Intercept) fi_fishid:19]","b[(Intercept) fi_fishid:20]","b[(Intercept) fi_fishid:21]",
"b[(Intercept) fi_fishid:22]", "b[(Intercept) fi_fishid:23]","b[(Intercept) fi_fishid:24]","b[(Intercept) fi_fishid:25]","b[(Intercept) fi_fishid:26]","b[(Intercept) fi_fishid:27]",
"b[(Intercept) fi_fishid:28]","b[(Intercept) fi_fishid:29]","b[(Intercept) fi_fishid:30]","b[(Intercept) fi_fishid:31]"))`

![M1b_s6](/Plots/M1b_s6.png "M1b_s6")

- *fi_fishid:26*,*fi_fishid:27*,*fi_fishid:28*,*fi_fishid:10*,*fi_fishid:19*,*fi_fishid:18* have both lot of probability mass on both sides of 0


#### Plot paired marginals

`mcmc_pairs(as.matrix(m1b),pars = c("(Intercept)",  "b[(Intercept) fi_fishid:1]", "b[(Intercept) fi_fishid:2]","b[(Intercept) fi_fishid:3]",
"b[(Intercept) fi_fishid:4]","b[(Intercept) fi_fishid:5]", "b[(Intercept) fi_fishid:6]", "b[(Intercept) fi_fishid:7]" ,"b[(Intercept) fi_fishid:8]","b[(Intercept) fi_fishid:9]",
"b[(Intercept) fi_fishid:10]","b[(Intercept) fi_fishid:11]", "b[(Intercept) fi_fishid:12]", "b[(Intercept) fi_fishid:13]","b[(Intercept) fi_fishid:14]","b[(Intercept) fi_fishid:15]",
"b[(Intercept) fi_fishid:16]", "b[(Intercept) fi_fishid:17]","b[(Intercept) fi_fishid:18]","b[(Intercept) fi_fishid:19]","b[(Intercept) fi_fishid:20]","b[(Intercept) fi_fishid:21]",
"b[(Intercept) fi_fishid:22]", "b[(Intercept) fi_fishid:23]","b[(Intercept) fi_fishid:24]","b[(Intercept) fi_fishid:25]","b[(Intercept) fi_fishid:26]","b[(Intercept) fi_fishid:27]",
"b[(Intercept) fi_fishid:28]","b[(Intercept) fi_fishid:29]","b[(Intercept) fi_fishid:30]","b[(Intercept) fi_fishid:31]"))`




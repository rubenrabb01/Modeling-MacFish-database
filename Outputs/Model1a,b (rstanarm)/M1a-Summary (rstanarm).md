# Model 1: Varying intercept model with no predictors (Variance components model)

Consider the simplest multilevel model for fishes i=1,...,n nested within fi_species j=1,...,J and for whom we have range distances as responses. We can write a two-level varying intercept model with no predictors using the usual two-stage formulation as

yij=αj+ϵij, where ϵij∼N(0,σ2y)
αj=μα+uj, where uj∼N(0,σ2α)

where yij is the range distance for the ith fish in the jth fi_species, αj is the varying intercept for the jth fi_species, and μα is the overall mean across fi_species. Alternatively, the model can be expressed in reduced form as
yij=μα+uj+ϵij.
. If we further assume that the fish-level errors ϵij are normally distributed with mean 0 and variance σ2y, and that the fi_species-level varying intercepts αj are normally distributed with mean μα and variance σ2α, then the model can be expressed as

yij∼N(αj,σ2y),
αj∼N(μα,σ2α),

This model can then be fit using lmer(). We specify an intercept (the predictor “1”) and allow it to vary by the level-2 identifier (fi_species). We also specify the REML = FALSE option to obtain maximum likelihood (ML) estimates as opposed to the default restricted maximum likelihood (REML) estimates.

`m1a<-stan_lmer(formula= sqrt(ranged2d + 1) ~ 1 +(1 | fi_fishid), data = mean.ranged2d, seed=350)`

```
SAMPLING FOR MODEL 'continuous' NOW (CHAIN 1).
Chain 1:
Chain 1: Gradient evaluation took 0.002 seconds
Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 20 seconds.
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
Chain 1:  Elapsed Time: 28.732 seconds (Warm-up)
Chain 1:                26.213 seconds (Sampling)
Chain 1:                54.945 seconds (Total)
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
Chain 2:  Elapsed Time: 30.276 seconds (Warm-up)
Chain 2:                24.634 seconds (Sampling)
Chain 2:                54.91 seconds (Total)
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
Chain 3:  Elapsed Time: 34.812 seconds (Warm-up)
Chain 3:                24.808 seconds (Sampling)
Chain 3:                59.62 seconds (Total)
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
Chain 4:  Elapsed Time: 28.86 seconds (Warm-up)
Chain 4:                21.665 seconds (Sampling)
Chain 4:                50.525 seconds (Total)
Chain 4:
Warning message:
Bulk Effective Samples Size (ESS) is too low, indicating posterior means and medians may be unreliable.
Running the chains for more iterations may help. See
http://mc-stan.org/misc/warnings.html#bulk-ess
```

### Quick Summary

`print(m1a, digits = 2)`

```
stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + (1 | fi_fishid)
 observations: 8846
------
            Median MAD_SD
(Intercept) 26.53   1.18

Auxiliary parameter(s):
      Median MAD_SD
sigma 13.96   0.10

Error terms:
 Groups    Name        Std.Dev.
 fi_fishid (Intercept)  7.12
 Residual              13.96
Num. levels: fi_fishid 31

------
* For help interpreting the printed output see ?print.stanreg
* For info on the priors used see ?prior_summary.stanreg
```

`summary(m1a)`

```
Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + (1 | fi_fishid)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
                                           mean   sd    10%   50%   90%
(Intercept)                               26.5    1.2  24.9  26.5  28.0
b[(Intercept) fi_fishid:1]                -2.5    1.4  -4.3  -2.5  -0.6
b[(Intercept) fi_fishid:2]                -6.4    1.6  -8.4  -6.4  -4.3
b[(Intercept) fi_fishid:3]                -4.9    1.4  -6.7  -4.9  -3.2
b[(Intercept) fi_fishid:4]                -7.3    1.4  -9.1  -7.3  -5.5
b[(Intercept) fi_fishid:5]                -7.8    1.5  -9.7  -7.9  -6.0
b[(Intercept) fi_fishid:6]                -4.1    1.5  -6.0  -4.1  -2.2
b[(Intercept) fi_fishid:7]                 6.5    1.6   4.4   6.4   8.5
b[(Intercept) fi_fishid:8]               -14.0    1.4 -15.8 -14.0 -12.2
b[(Intercept) fi_fishid:9]                 6.4    1.4   4.7   6.4   8.3
b[(Intercept) fi_fishid:10]                0.8    1.4  -1.0   0.8   2.6
b[(Intercept) fi_fishid:11]                0.3    1.4  -1.5   0.3   2.1
b[(Intercept) fi_fishid:12]               -4.7    1.7  -6.9  -4.7  -2.4
b[(Intercept) fi_fishid:13]                6.0    1.5   4.0   6.0   7.9
b[(Intercept) fi_fishid:14]               -8.1    1.6 -10.2  -8.1  -6.0
b[(Intercept) fi_fishid:15]               10.7    1.5   8.9  10.7  12.6
b[(Intercept) fi_fishid:16]                7.9    1.4   6.1   7.9   9.7
b[(Intercept) fi_fishid:17]                6.4    1.4   4.6   6.4   8.2
b[(Intercept) fi_fishid:18]               -0.5    1.5  -2.4  -0.5   1.4
b[(Intercept) fi_fishid:19]                3.6    1.4   1.9   3.6   5.4
b[(Intercept) fi_fishid:20]                3.0    1.4   1.2   3.0   4.8
b[(Intercept) fi_fishid:21]                5.3    1.5   3.3   5.2   7.2
b[(Intercept) fi_fishid:22]                5.9    1.4   4.1   5.9   7.7
b[(Intercept) fi_fishid:23]               -3.9    1.4  -5.7  -4.0  -2.1
b[(Intercept) fi_fishid:24]                0.2    1.5  -1.7   0.2   2.1
b[(Intercept) fi_fishid:25]               -8.1    1.5 -10.0  -8.2  -6.3
b[(Intercept) fi_fishid:26]                0.4    1.4  -1.4   0.4   2.3
b[(Intercept) fi_fishid:27]                7.1    1.4   5.3   7.1   9.0
b[(Intercept) fi_fishid:28]               -0.8    1.4  -2.7  -0.8   1.0
b[(Intercept) fi_fishid:29]              -13.0    1.5 -15.0 -13.1 -11.1
b[(Intercept) fi_fishid:30]                8.5    1.4   6.7   8.5  10.3
b[(Intercept) fi_fishid:31]                9.2    1.4   7.4   9.2  11.0
sigma                                     14.0    0.1  13.8  14.0  14.1
Sigma[fi_fishid:(Intercept),(Intercept)]  50.7   14.6  35.0  47.8  70.7

Fit Diagnostics:
           mean   sd   10%   50%   90%
mean_PPD 26.9    0.2 26.7  26.9  27.2

The mean_ppd is the sample average posterior predictive distribution of the outcome variable (for details see help('summary.stanreg')).

MCMC diagnostics
                                         mcse Rhat n_eff
(Intercept)                              0.1  1.0   275
b[(Intercept) fi_fishid:1]               0.1  1.0   357
b[(Intercept) fi_fishid:2]               0.1  1.0   511
b[(Intercept) fi_fishid:3]               0.1  1.0   368
b[(Intercept) fi_fishid:4]               0.1  1.0   375
b[(Intercept) fi_fishid:5]               0.1  1.0   403
b[(Intercept) fi_fishid:6]               0.1  1.0   408
b[(Intercept) fi_fishid:7]               0.1  1.0   491
b[(Intercept) fi_fishid:8]               0.1  1.0   352
b[(Intercept) fi_fishid:9]               0.1  1.0   374
b[(Intercept) fi_fishid:10]              0.1  1.0   362
b[(Intercept) fi_fishid:11]              0.1  1.0   383
b[(Intercept) fi_fishid:12]              0.1  1.0   536
b[(Intercept) fi_fishid:13]              0.1  1.0   414
b[(Intercept) fi_fishid:14]              0.1  1.0   511
b[(Intercept) fi_fishid:15]              0.1  1.0   371
b[(Intercept) fi_fishid:16]              0.1  1.0   375
b[(Intercept) fi_fishid:17]              0.1  1.0   384
b[(Intercept) fi_fishid:18]              0.1  1.0   409
b[(Intercept) fi_fishid:19]              0.1  1.0   367
b[(Intercept) fi_fishid:20]              0.1  1.0   368
b[(Intercept) fi_fishid:21]              0.1  1.0   392
b[(Intercept) fi_fishid:22]              0.1  1.0   380
b[(Intercept) fi_fishid:23]              0.1  1.0   362
b[(Intercept) fi_fishid:24]              0.1  1.0   369
b[(Intercept) fi_fishid:25]              0.1  1.0   390
b[(Intercept) fi_fishid:26]              0.1  1.0   391
b[(Intercept) fi_fishid:27]              0.1  1.0   383
b[(Intercept) fi_fishid:28]              0.1  1.0   373
b[(Intercept) fi_fishid:29]              0.1  1.0   472
b[(Intercept) fi_fishid:30]              0.1  1.0   377
b[(Intercept) fi_fishid:31]              0.1  1.0   344
sigma                                    0.0  1.0  3179
Sigma[fi_fishid:(Intercept),(Intercept)] 0.9  1.0   282
mean_PPD                                 0.0  1.0  3955
log-posterior                            0.3  1.0   381

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).

```

### Check priors used

`prior_summary(object = m1a)`

```{r, echo=FALSE, eval=TRUE}

Priors for model 'm1a'
------
Intercept (after predictors centered)
  Specified prior:
    ~ normal(location = 0, scale = 10)
  Adjusted prior:
    ~ normal(location = 0, scale = 155)

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

`summary(m1a, pars = c("(Intercept)", "sigma", "Sigma[fi_species:(Intercept),(Intercept)]"), probs = c(0.025, 0.975), digits = 2)`

```
Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + (1 | fi_fishid)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
              mean   sd    2.5%   97.5%
(Intercept) 26.50   1.22 24.04  28.92
sigma       13.96   0.10 13.76  14.17

MCMC diagnostics
            mcse Rhat n_eff
(Intercept) 0.07 1.00  275
sigma       0.00 1.00 3179

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).

```

### Extract the posterior draws for all parameters

`sims <- as.matrix(m1a)`

`dim(sims)`

```
[1] 4000   34

```

`para_name <- colnames(sims)`

`para_name`

```
  [1] "(Intercept)"                              "b[(Intercept) fi_fishid:1]"
 [3] "b[(Intercept) fi_fishid:2]"               "b[(Intercept) fi_fishid:3]"
 [5] "b[(Intercept) fi_fishid:4]"               "b[(Intercept) fi_fishid:5]"
 [7] "b[(Intercept) fi_fishid:6]"               "b[(Intercept) fi_fishid:7]"
 [9] "b[(Intercept) fi_fishid:8]"               "b[(Intercept) fi_fishid:9]"
[11] "b[(Intercept) fi_fishid:10]"              "b[(Intercept) fi_fishid:11]"
[13] "b[(Intercept) fi_fishid:12]"              "b[(Intercept) fi_fishid:13]"
[15] "b[(Intercept) fi_fishid:14]"              "b[(Intercept) fi_fishid:15]"
[17] "b[(Intercept) fi_fishid:16]"              "b[(Intercept) fi_fishid:17]"
[19] "b[(Intercept) fi_fishid:18]"              "b[(Intercept) fi_fishid:19]"
[21] "b[(Intercept) fi_fishid:20]"              "b[(Intercept) fi_fishid:21]"
[23] "b[(Intercept) fi_fishid:22]"              "b[(Intercept) fi_fishid:23]"
[25] "b[(Intercept) fi_fishid:24]"              "b[(Intercept) fi_fishid:25]"
[27] "b[(Intercept) fi_fishid:26]"              "b[(Intercept) fi_fishid:27]"
[29] "b[(Intercept) fi_fishid:28]"              "b[(Intercept) fi_fishid:29]"
[31] "b[(Intercept) fi_fishid:30]"              "b[(Intercept) fi_fishid:31]"
[33] "sigma"                                    "Sigma[fi_fishid:(Intercept),(Intercept)]"
```

- Obtain fi_species-level varying intercept a_j

 - draws for overall mean

`mu_a_sims <- as.matrix(m1a, pars = "(Intercept)")`

 - draws for 3 fi_species-level error

`fish_err <- as.matrix(m1a,regex_pars = "b\\[\\(Intercept\\) fi_fishid\\:")`

 - draws for 3 species' varying intercepts

`fish_inter <- as.numeric(mu_a_sims) + fish_err`

- Obtain sigma_y and sigma_alpha^2

 -draws for sigma_y

`signma_y <- as.matrix(m1a, pars = "sigma")`

 - draws for sigma_alpha^2

`sigma_alpha <- as.matrix(m1a, pars = "Sigma[fi_fishid:(Intercept),(Intercept)]")`


### Obtaining means, SD, medians and 95% credible intervals of varying intercepts

In *fish_inter*, we have saved 4,000 posterior draws (from all 4 chains) for the varying intercepts αj of the 30 fishes. For example, the first column of the 4,000 by 30 matrix is a vector of 4,000 posterior simulation draws for the first fish's (*fi_species*:T449202_1) varying intercept α1. One quantitative way to summarize the posterior probability distribution of these 4,000 estimates for α1 is to examine their quantiles.

- Posterior mean of each alpha

`a_mean <- apply(X = fish_inter, MARGIN = 2, FUN = mean)`

`a_mean'

```
 b[(Intercept) fi_fishid:1]  b[(Intercept) fi_fishid:2]  b[(Intercept) fi_fishid:3]  b[(Intercept) fi_fishid:4]
                   24.01236                    20.12778                    21.55987                    19.20594
 b[(Intercept) fi_fishid:5]  b[(Intercept) fi_fishid:6]  b[(Intercept) fi_fishid:7]  b[(Intercept) fi_fishid:8]
                   18.66063                    22.41447                    32.96246                    12.48559
 b[(Intercept) fi_fishid:9] b[(Intercept) fi_fishid:10] b[(Intercept) fi_fishid:11] b[(Intercept) fi_fishid:12]
                   32.94433                    27.28610                    26.80723                    21.84494
b[(Intercept) fi_fishid:13] b[(Intercept) fi_fishid:14] b[(Intercept) fi_fishid:15] b[(Intercept) fi_fishid:16]
                   32.46018                    18.40715                    37.23900                    34.37760
b[(Intercept) fi_fishid:17] b[(Intercept) fi_fishid:18] b[(Intercept) fi_fishid:19] b[(Intercept) fi_fishid:20]
                   32.88719                    26.02241                    30.13217                    29.50361
b[(Intercept) fi_fishid:21] b[(Intercept) fi_fishid:22] b[(Intercept) fi_fishid:23] b[(Intercept) fi_fishid:24]
                   31.75561                    32.43806                    22.57154                    26.70189
b[(Intercept) fi_fishid:25] b[(Intercept) fi_fishid:26] b[(Intercept) fi_fishid:27] b[(Intercept) fi_fishid:28]
                   18.35705                    26.95068                    33.61570                    25.67090
b[(Intercept) fi_fishid:29] b[(Intercept) fi_fishid:30] b[(Intercept) fi_fishid:31]
                   13.45441                    34.96372                    35.70593
```

- Posterior SD of each alpha

`a_sd <- apply(X = fish_inter, MARGIN = 2, FUN = sd)`

`a_sd`

```
 b[(Intercept) fi_fishid:1]  b[(Intercept) fi_fishid:2]  b[(Intercept) fi_fishid:3]  b[(Intercept) fi_fishid:4]
                  0.7702548                   1.0973781                   0.7398227                   0.7533626
 b[(Intercept) fi_fishid:5]  b[(Intercept) fi_fishid:6]  b[(Intercept) fi_fishid:7]  b[(Intercept) fi_fishid:8]
                  0.8366840                   0.8216294                   1.1238006                   0.7418592
 b[(Intercept) fi_fishid:9] b[(Intercept) fi_fishid:10] b[(Intercept) fi_fishid:11] b[(Intercept) fi_fishid:12]
                  0.7557578                   0.7787025                   0.7415034                   1.2610458
b[(Intercept) fi_fishid:13] b[(Intercept) fi_fishid:14] b[(Intercept) fi_fishid:15] b[(Intercept) fi_fishid:16]
                  0.9279770                   1.1019489                   0.8278314                   0.7392616
b[(Intercept) fi_fishid:17] b[(Intercept) fi_fishid:18] b[(Intercept) fi_fishid:19] b[(Intercept) fi_fishid:20]
                  0.7834571                   0.8364101                   0.7665548                   0.8027104
b[(Intercept) fi_fishid:21] b[(Intercept) fi_fishid:22] b[(Intercept) fi_fishid:23] b[(Intercept) fi_fishid:24]
                  0.8750333                   0.7488008                   0.7476838                   0.8279524
b[(Intercept) fi_fishid:25] b[(Intercept) fi_fishid:26] b[(Intercept) fi_fishid:27] b[(Intercept) fi_fishid:28]
                  0.8119233                   0.7709505                   0.7863929                   0.7937091
b[(Intercept) fi_fishid:29] b[(Intercept) fi_fishid:30] b[(Intercept) fi_fishid:31]
                  0.9657903                   0.7579784                   0.7357061
```

- Posterior median and 95% credible interval

`a_quant <- apply(X = fish_inter, MARGIN = 2, FUN = quantile, probs = c(0.025, 0.50, 0.975))`

`a_quant <- data.frame(t(a_quant))`

`a_quant`

```
                               X2.5.     X50.   X97.5.
b[(Intercept) fi_fishid:1]  22.51460 24.01776 25.53278
b[(Intercept) fi_fishid:2]  17.95868 20.12460 22.25219
b[(Intercept) fi_fishid:3]  20.09419 21.56511 23.00824
b[(Intercept) fi_fishid:4]  17.75515 19.20292 20.67839
b[(Intercept) fi_fishid:5]  17.01343 18.66592 20.28811
b[(Intercept) fi_fishid:6]  20.79487 22.42695 23.98022
b[(Intercept) fi_fishid:7]  30.74492 32.96372 35.16173
b[(Intercept) fi_fishid:8]  11.03092 12.48582 13.97652
b[(Intercept) fi_fishid:9]  31.47855 32.94323 34.42971
b[(Intercept) fi_fishid:10] 25.71317 27.28247 28.77218
b[(Intercept) fi_fishid:11] 25.36648 26.80550 28.32130
b[(Intercept) fi_fishid:12] 19.43049 21.83993 24.28742
b[(Intercept) fi_fishid:13] 30.67914 32.45436 34.28063
b[(Intercept) fi_fishid:14] 16.24116 18.40614 20.59536
b[(Intercept) fi_fishid:15] 35.58431 37.22210 38.85242
b[(Intercept) fi_fishid:16] 32.91652 34.38061 35.84113
b[(Intercept) fi_fishid:17] 31.34371 32.88731 34.45670
b[(Intercept) fi_fishid:18] 24.36632 26.03564 27.62113
b[(Intercept) fi_fishid:19] 28.63892 30.13026 31.62417
b[(Intercept) fi_fishid:20] 27.95177 29.51934 31.04375
b[(Intercept) fi_fishid:21] 30.05583 31.76451 33.48390
b[(Intercept) fi_fishid:22] 31.02468 32.42762 33.91655
b[(Intercept) fi_fishid:23] 21.09442 22.57991 23.99934
b[(Intercept) fi_fishid:24] 25.07508 26.71527 28.34854
b[(Intercept) fi_fishid:25] 16.77116 18.36173 19.91120
b[(Intercept) fi_fishid:26] 25.46448 26.96274 28.43337
b[(Intercept) fi_fishid:27] 32.07852 33.60849 35.12117
b[(Intercept) fi_fishid:28] 24.06829 25.67885 27.21599
b[(Intercept) fi_fishid:29] 11.54150 13.45211 15.33787
b[(Intercept) fi_fishid:30] 33.47160 34.95923 36.42457
b[(Intercept) fi_fishid:31] 34.27616 35.70234 37.12118

```

`names(a_quant) <- c("X2.5.", "X50.", "X97.5.")`

- Combine summary statistics of posterior simulation draws

`a_df <- data.frame(a_mean, a_sd, a_quant)`

`round(head(a_df), 2)`

```
                           a_mean a_sd X2.5.  X50. X97.5.
b[(Intercept) fi_fishid:1]  24.01 0.77 22.51 24.02  25.53
b[(Intercept) fi_fishid:2]  20.13 1.10 17.96 20.12  22.25
b[(Intercept) fi_fishid:3]  21.56 0.74 20.09 21.57  23.01
b[(Intercept) fi_fishid:4]  19.21 0.75 17.76 19.20  20.68
b[(Intercept) fi_fishid:5]  18.66 0.84 17.01 18.67  20.29
b[(Intercept) fi_fishid:6]  22.41 0.82 20.79 22.43  23.98

```

### Plots

Caterpillar plot to show the fully Bayes estimates for the *fi_species* varying intercepts in rank order together with their 95% credible interval

- Sort dataframe containing an estimated alpha's mean and sd for every fi_species

`a_df <- a_df[order(a_df$a_mean), ]`

`a_df`
```
                              a_mean      a_sd    X2.5.     X50.   X97.5.
b[(Intercept) fi_fishid:8]  12.48559 0.7418592 11.03092 12.48582 13.97652
b[(Intercept) fi_fishid:29] 13.45441 0.9657903 11.54150 13.45211 15.33787
b[(Intercept) fi_fishid:25] 18.35705 0.8119233 16.77116 18.36173 19.91120
b[(Intercept) fi_fishid:14] 18.40715 1.1019489 16.24116 18.40614 20.59536
b[(Intercept) fi_fishid:5]  18.66063 0.8366840 17.01343 18.66592 20.28811
b[(Intercept) fi_fishid:4]  19.20594 0.7533626 17.75515 19.20292 20.67839
b[(Intercept) fi_fishid:2]  20.12778 1.0973781 17.95868 20.12460 22.25219
b[(Intercept) fi_fishid:3]  21.55987 0.7398227 20.09419 21.56511 23.00824
b[(Intercept) fi_fishid:12] 21.84494 1.2610458 19.43049 21.83993 24.28742
b[(Intercept) fi_fishid:6]  22.41447 0.8216294 20.79487 22.42695 23.98022
b[(Intercept) fi_fishid:23] 22.57154 0.7476838 21.09442 22.57991 23.99934
b[(Intercept) fi_fishid:1]  24.01236 0.7702548 22.51460 24.01776 25.53278
b[(Intercept) fi_fishid:28] 25.67090 0.7937091 24.06829 25.67885 27.21599
b[(Intercept) fi_fishid:18] 26.02241 0.8364101 24.36632 26.03564 27.62113
b[(Intercept) fi_fishid:24] 26.70189 0.8279524 25.07508 26.71527 28.34854
b[(Intercept) fi_fishid:11] 26.80723 0.7415034 25.36648 26.80550 28.32130
b[(Intercept) fi_fishid:26] 26.95068 0.7709505 25.46448 26.96274 28.43337
b[(Intercept) fi_fishid:10] 27.28610 0.7787025 25.71317 27.28247 28.77218
b[(Intercept) fi_fishid:20] 29.50361 0.8027104 27.95177 29.51934 31.04375
b[(Intercept) fi_fishid:19] 30.13217 0.7665548 28.63892 30.13026 31.62417
b[(Intercept) fi_fishid:21] 31.75561 0.8750333 30.05583 31.76451 33.48390
b[(Intercept) fi_fishid:22] 32.43806 0.7488008 31.02468 32.42762 33.91655
b[(Intercept) fi_fishid:13] 32.46018 0.9279770 30.67914 32.45436 34.28063
b[(Intercept) fi_fishid:17] 32.88719 0.7834571 31.34371 32.88731 34.45670
b[(Intercept) fi_fishid:9]  32.94433 0.7557578 31.47855 32.94323 34.42971
b[(Intercept) fi_fishid:7]  32.96246 1.1238006 30.74492 32.96372 35.16173
b[(Intercept) fi_fishid:27] 33.61570 0.7863929 32.07852 33.60849 35.12117
b[(Intercept) fi_fishid:16] 34.37760 0.7392616 32.91652 34.38061 35.84113
b[(Intercept) fi_fishid:30] 34.96372 0.7579784 33.47160 34.95923 36.42457
b[(Intercept) fi_fishid:31] 35.70593 0.7357061 34.27616 35.70234 37.12118
b[(Intercept) fi_fishid:15] 37.23900 0.8278314 35.58431 37.22210 38.85242
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
![M1a_s1](/Plots/M1a_s1.png "M1a_s1")

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
1 3.88 1.34 1.21 3.88   6.5

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
  geom_text(aes(0.45, 20, label = "mean = 3.88"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 1.34"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M1a_s2](/Plots/M1a_s2.png "M1a_s2")

The expected difference comes to 3.88 with a standard deviation of 1.34 and a wide range of uncertainty. The 95% credible interval is [1.21,6.5], so we are 95% certain that the true value of the difference between the two species lies within the range, given the data

We also can get the proportion of the time that pike has a higher mean than pikeperch:

`prop.table(table(fish_inter[, 1] > fish_inter[, 2]))`

```
  FALSE    TRUE
0.00325 0.99675
```

This means that the posterior probability that fish1 is better than fish2 is 99.7%. Any pair of fishes within the sample of fishes can be compared in this manner.

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
1 -1.43 1.32 -3.94 -1.44  1.13

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
  geom_text(aes(0.45, 20, label = "mean = -1.43"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 1.32"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M1a_s3](/Plots/M1a_s3.png "M1a_s3")

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
  mean   sd Q2.5  Q50 Q97.5
1 2.45 1.07 0.35 2.45  4.57

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
  geom_text(aes(0.45, 20, label = "mean = 2.45"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 1.07"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M1a_s4](/Plots/M1a_s4.png "M1a_s4")


### Analyse posterior

#### Plot posterior

- Including random effects from *fi_species*

`library(bayesplot)`

`mcmc_areas(as.matrix(m1a), prob_outer = .999)`

![M1a_s5](/Plots/M1a_s5.png "M1a_s5")

- Excluding random effects from *fi_species*

`mcmc_areas(as.matrix(m1a), prob_outer = .999, pars = c("(Intercept)",  "b[(Intercept) fi_fishid:1]", "b[(Intercept) fi_fishid:2]","b[(Intercept) fi_fishid:3]",
"b[(Intercept) fi_fishid:4]","b[(Intercept) fi_fishid:5]", "b[(Intercept) fi_fishid:6]", "b[(Intercept) fi_fishid:7]" ,"b[(Intercept) fi_fishid:8]","b[(Intercept) fi_fishid:9]",
"b[(Intercept) fi_fishid:10]","b[(Intercept) fi_fishid:11]", "b[(Intercept) fi_fishid:12]", "b[(Intercept) fi_fishid:13]","b[(Intercept) fi_fishid:14]","b[(Intercept) fi_fishid:15]",
"b[(Intercept) fi_fishid:16]", "b[(Intercept) fi_fishid:17]","b[(Intercept) fi_fishid:18]","b[(Intercept) fi_fishid:19]","b[(Intercept) fi_fishid:20]","b[(Intercept) fi_fishid:21]",
"b[(Intercept) fi_fishid:22]", "b[(Intercept) fi_fishid:23]","b[(Intercept) fi_fishid:24]","b[(Intercept) fi_fishid:25]","b[(Intercept) fi_fishid:26]","b[(Intercept) fi_fishid:27]",
"b[(Intercept) fi_fishid:28]","b[(Intercept) fi_fishid:29]","b[(Intercept) fi_fishid:30]","b[(Intercept) fi_fishid:31]"))`

![M1a_s6](/Plots/M1a_s6.png "M1a_s6")

- *fi_fishid:26*,*fi_fishid:27*,*fi_fishid:28*,*fi_fishid:10*,*fi_fishid:19*,*fi_fishid:18* have both lot of probability mass on both sides of 0


#### Plot paired marginals

`mcmc_pairs(as.matrix(m1a),pars = c("(Intercept)",  "b[(Intercept) fi_fishid:1]", "b[(Intercept) fi_fishid:2]","b[(Intercept) fi_fishid:3]",
"b[(Intercept) fi_fishid:4]","b[(Intercept) fi_fishid:5]", "b[(Intercept) fi_fishid:6]", "b[(Intercept) fi_fishid:7]" ,"b[(Intercept) fi_fishid:8]","b[(Intercept) fi_fishid:9]",
"b[(Intercept) fi_fishid:10]","b[(Intercept) fi_fishid:11]", "b[(Intercept) fi_fishid:12]", "b[(Intercept) fi_fishid:13]","b[(Intercept) fi_fishid:14]","b[(Intercept) fi_fishid:15]",
"b[(Intercept) fi_fishid:16]", "b[(Intercept) fi_fishid:17]","b[(Intercept) fi_fishid:18]","b[(Intercept) fi_fishid:19]","b[(Intercept) fi_fishid:20]","b[(Intercept) fi_fishid:21]",
"b[(Intercept) fi_fishid:22]", "b[(Intercept) fi_fishid:23]","b[(Intercept) fi_fishid:24]","b[(Intercept) fi_fishid:25]","b[(Intercept) fi_fishid:26]","b[(Intercept) fi_fishid:27]",
"b[(Intercept) fi_fishid:28]","b[(Intercept) fi_fishid:29]","b[(Intercept) fi_fishid:30]","b[(Intercept) fi_fishid:31]"))`

![M1a_s7](/Plots/M1a_s7.png "M1a_s7")


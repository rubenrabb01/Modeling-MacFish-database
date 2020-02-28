## Re-fit model 3 using different Bayesian input parameters:

- Increase the value of parameter `adapt_delta` to 0.95 (default is 0.8) as I had the warning messages:
```
1: There were 49 divergent transitions after warmup. Increasing adapt_delta above 0.95 may help. See
http://mc-stan.org/misc/warnings.html#divergent-transitions-after-warmup 
2: Examine the pairs() plot to diagnose sampling problems
```
`m31 <- stan_lmer(formula=sqrt(ranged2d + 1) ~ 1 + season*fi_species + (1 | fi_species), data = mean.ranged2d, seed=350, control = list(max_treedepth=20,adapt_delta = 0.95))`

### Model 3 results

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
Chain 1:  Elapsed Time: 1197.23 seconds (Warm-up)
Chain 1:                761.127 seconds (Sampling)
Chain 1:                1958.36 seconds (Total)
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
Chain 2:  Elapsed Time: 1066.36 seconds (Warm-up)
Chain 2:                756.505 seconds (Sampling)
Chain 2:                1822.86 seconds (Total)
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
Chain 3:  Elapsed Time: 1172.38 seconds (Warm-up)
Chain 3:                924.527 seconds (Sampling)
Chain 3:                2096.91 seconds (Total)
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
Chain 4:  Elapsed Time: 1067.13 seconds (Warm-up)
Chain 4:                716.206 seconds (Sampling)
Chain 4:                1783.33 seconds (Total)
Chain 4:
Warning messages:
1: There were 49 divergent transitions after warmup. Increasing adapt_delta above 0.95 may help. See
http://mc-stan.org/misc/warnings.html#divergent-transitions-after-warmup
2: Examine the pairs() plot to diagnose sampling problems

```

### Quick Summary

`print(m31, digits = 2)`

```
stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + season * fi_species + (1 | fi_species)
 observations: 8846
------
                                    Median MAD_SD
(Intercept)                          27.21   5.59
seasonspring_I                       -2.75   0.89
seasonspring_II                      13.15   1.89
seasonsummer                         -4.34   0.79
seasonwinter                          3.96   0.86
fi_speciespikeperch                  -8.18   7.72
fi_specieswels                       -0.09   7.57
seasonspring_I:fi_speciespikeperch    8.38   1.38
seasonspring_II:fi_speciespikeperch -12.50   2.75
seasonsummer:fi_speciespikeperch     14.10   1.17
seasonwinter:fi_speciespikeperch     -3.74   1.33
seasonspring_I:fi_specieswels         7.40   1.15
seasonspring_II:fi_specieswels       -6.31   2.41
seasonsummer:fi_specieswels           5.17   0.95
seasonwinter:fi_specieswels          -2.14   1.12

Auxiliary parameter(s):
      Median MAD_SD
sigma 14.98   0.11

Error terms:
 Groups     Name        Std.Dev.
 fi_species (Intercept) 12.6
 Residual               15.0
Num. levels: fi_species 3

------
* For help interpreting the printed output see ?print.stanreg
* For info on the priors used see ?prior_summary.stanreg
```

`summary(m31)`

```
Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + season * fi_species + (1 | fi_species)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_species (3)

Estimates:
                                            mean   sd    10%   50%   90%
(Intercept)                                26.9    9.7  16.2  27.2  37.5
seasonspring_I                             -2.7    0.9  -3.9  -2.7  -1.6
seasonspring_II                            13.2    1.9  10.8  13.2  15.6
seasonsummer                               -4.3    0.8  -5.3  -4.3  -3.4
seasonwinter                                4.0    0.8   2.9   4.0   5.0
fi_speciespikeperch                        -7.4   13.1 -20.9  -8.2   6.6
fi_specieswels                              0.4   13.1 -14.2  -0.1  15.0
seasonspring_I:fi_speciespikeperch          8.4    1.4   6.6   8.4  10.2
seasonspring_II:fi_speciespikeperch       -12.5    2.7 -16.1 -12.5  -9.0
seasonsummer:fi_speciespikeperch           14.1    1.2  12.6  14.1  15.6
seasonwinter:fi_speciespikeperch           -3.7    1.3  -5.4  -3.7  -2.1
seasonspring_I:fi_specieswels               7.4    1.1   5.9   7.4   8.8
seasonspring_II:fi_specieswels             -6.3    2.4  -9.3  -6.3  -3.3
seasonsummer:fi_specieswels                 5.1    1.0   3.9   5.2   6.4
seasonwinter:fi_specieswels                -2.1    1.1  -3.5  -2.1  -0.8
b[(Intercept) fi_species:pike]              0.3    9.7 -10.2   0.0  11.1
b[(Intercept) fi_species:pikeperch]        -0.7   10.3 -12.0   0.0   9.7
b[(Intercept) fi_species:wels]             -0.2    9.5 -10.9   0.0  10.0
sigma                                      15.0    0.1  14.8  15.0  15.1
Sigma[fi_species:(Intercept),(Intercept)] 159.3  273.2   1.4  51.0 453.9

Fit Diagnostics:
           mean   sd   10%   50%   90%
mean_PPD 26.9    0.2 26.6  26.9  27.2

The mean_ppd is the sample average posterior predictive distribution of the outcome variable (for details see help('summary.stanreg')).

MCMC diagnostics
                                          mcse Rhat n_eff
(Intercept)                               0.3  1.0  1427
seasonspring_I                            0.0  1.0  1819
seasonspring_II                           0.0  1.0  2254
seasonsummer                              0.0  1.0  1859
seasonwinter                              0.0  1.0  2003
fi_speciespikeperch                       0.3  1.0  1509
fi_specieswels                            0.4  1.0  1373
seasonspring_I:fi_speciespikeperch        0.0  1.0  2142
seasonspring_II:fi_speciespikeperch       0.1  1.0  2577
seasonsummer:fi_speciespikeperch          0.0  1.0  2139
seasonwinter:fi_speciespikeperch          0.0  1.0  2191
seasonspring_I:fi_specieswels             0.0  1.0  1931
seasonspring_II:fi_specieswels            0.0  1.0  2384
seasonsummer:fi_specieswels               0.0  1.0  2121
seasonwinter:fi_specieswels               0.0  1.0  2113
b[(Intercept) fi_species:pike]            0.3  1.0  1418
b[(Intercept) fi_species:pikeperch]       0.2  1.0  1968
b[(Intercept) fi_species:wels]            0.3  1.0  1346
sigma                                     0.0  1.0  5027
Sigma[fi_species:(Intercept),(Intercept)] 7.1  1.0  1493
mean_PPD                                  0.0  1.0  4018
log-posterior                             0.1  1.0  1428

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).

```

### Check priors used

`prior_summary(object = m31)`

```{r, echo=FALSE, eval=TRUE}

Priors for model 'm31'
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

`summary(m31, pars = c("(Intercept)", "sigma", "Sigma[fi_species:(Intercept),(Intercept)]"), probs = c(0.025, 0.975), digits = 2)`

```
Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + season * fi_species + (1 | fi_species)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_species (3)

Estimates:
                                            mean   sd     2.5%   97.5%
(Intercept)                                26.95   9.74   3.70  47.32
sigma                                      14.98   0.12  14.76  15.21
Sigma[fi_species:(Intercept),(Intercept)] 159.26 273.24   0.12 959.41

MCMC diagnostics
                                          mcse Rhat n_eff
(Intercept)                               0.26 1.00 1427
sigma                                     0.00 1.00 5027
Sigma[fi_species:(Intercept),(Intercept)] 7.07 1.00 1493

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).

```

### Extract the posterior draws for all parameters

`sims <- as.matrix(m31)`

`dim(sims)`

```
[1] 4000   20

```

`para_name <- colnames(sims)`

`para_name`

```
 [1] "(Intercept)"                               "seasonspring_I"                            "seasonspring_II"
 [4] "seasonsummer"                              "seasonwinter"                              "fi_speciespikeperch"
 [7] "fi_specieswels"                            "seasonspring_I:fi_speciespikeperch"        "seasonspring_II:fi_speciespikeperch"
[10] "seasonsummer:fi_speciespikeperch"          "seasonwinter:fi_speciespikeperch"          "seasonspring_I:fi_specieswels"
[13] "seasonspring_II:fi_specieswels"            "seasonsummer:fi_specieswels"               "seasonwinter:fi_specieswels"
[16] "b[(Intercept) fi_species:pike]"            "b[(Intercept) fi_species:pikeperch]"       "b[(Intercept) fi_species:wels]"
[19] "sigma"                                     "Sigma[fi_species:(Intercept),(Intercept)]"

```

- Obtain fi_species-level varying intercept a_j

 - draws for overall mean

`mu_a_sims <- as.matrix(m31, pars = "(Intercept)")`

 - draws for 3 fi_species-level error

`fish_err <- as.matrix(m31,regex_pars = "b\\[\\(Intercept\\) fi_species\\:")`

 - draws for 3 species' varying intercepts

`fish_inter <- as.numeric(mu_a_sims) + fish_err`

- Obtain sigma_y and sigma_alpha^2

 -draws for sigma_y

`signma_y <- as.matrix(m31, pars = "sigma")`

 - draws for sigma_alpha^2

`sigma_alpha <- as.matrix(m31, pars = "Sigma[fi_species:(Intercept),(Intercept)]")`


### Obtaining means, SD, medians and 95% credible intervals of varying intercepts

In *fish_inter*, we have saved 4,000 posterior draws (from all 4 chains) for the varying intercepts αj of the 30 fishes. For example, the first column of the 4,000 by 30 matrix is a vector of 4,000 posterior simulation draws for the first fish's (*fi_species*:T449202_1) varying intercept α1. One quantitative way to summarize the posterior probability distribution of these 4,000 estimates for α1 is to examine their quantiles.

- Posterior mean of each alpha

`a_mean <- apply(X = fish_inter, MARGIN = 2, FUN = mean)`

`a_mean'

```
b[(Intercept) fi_species:pike] b[(Intercept) fi_species:pikeperch]      b[(Intercept) fi_species:wels]
                     27.25171                            26.27423                            26.76328
```

- Posterior SD of each alpha

`a_sd <- apply(X = fish_inter, MARGIN = 2, FUN = sd)`

`a_sd`

```
     b[(Intercept) fi_species:pike] b[(Intercept) fi_species:pikeperch]      b[(Intercept) fi_species:wels]
                          0.5558908                          13.0366786                          13.0231399

```

- Posterior median and 95% credible interval

`a_quant <- apply(X = fish_inter, MARGIN = 2, FUN = quantile, probs = c(0.025, 0.50, 0.975))`

`a_quant <- data.frame(t(a_quant))`

`a_quant`

```
                                        X2.5.     X50.   X97.5.
b[(Intercept) fi_species:pike]      26.161654 27.25910 28.29623
b[(Intercept) fi_species:pikeperch] -4.321019 27.01307 53.04407
b[(Intercept) fi_species:wels]      -3.339740 27.22325 54.86273

```

`names(a_quant) <- c("Q2.5", "Q50", "Q97.5")`

- Combine summary statistics of posterior simulation draws

`a_df <- data.frame(a_mean, a_sd, a_quant)`

`round(head(a_df), 2)`

```
                                    a_mean  a_sd  Q2.5   Q50 Q97.5
b[(Intercept) fi_species:pike]       27.25  0.56 26.16 27.26 28.30
b[(Intercept) fi_species:pikeperch]  26.27 13.04 -4.32 27.01 53.04
b[(Intercept) fi_species:wels]       26.76 13.02 -3.34 27.22 54.86

```

### Plots

Caterpillar plot to show the fully Bayes estimates for the *fi_species* varying intercepts in rank order together with their 95% credible interval

- Sort dataframe containing an estimated alpha's mean and sd for every fi_species

`a_df <- a_df[order(a_df$a_mean), ]`

`a_df`
```
                                      a_mean       a_sd      Q2.5      Q50    Q97.5
b[(Intercept) fi_species:pikeperch] 26.27423 13.0366786 -4.321019 27.01307 53.04407
b[(Intercept) fi_species:wels]      26.76328 13.0231399 -3.339740 27.22325 54.86273
b[(Intercept) fi_species:pike]      27.25171  0.5558908 26.161654 27.25910 28.29623
```

`a_df$a_rank <- c(1 : dim(a_df)[1])`    # a vector of fi_species rank

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
![M31_s1](/Plots/M31_s1.png "M31_s1")

### Differences between species averages

Here I'll explore the posterior distribution of the differences between each two species with descriptive statistics and histograms

- The difference between the pike and pikeperch averages is:

`fish_diff1 <- fish_inter[, 1] - fish_inter[, 2]`

`mean <- mean(fish_diff1)`
`sd <- sd(fish_diff1)`
`quantile <- quantile(fish_diff1, probs = c(0.025, 0.50, 0.975))`
`quantile <- data.frame(t(quantile))`
`names(quantile) <- c("Q2.5", "Q50", "Q97.5")`
`diff_df1 <- data.frame(mean, sd, quantile)`
`round(diff_df1, 2)`

```
  mean    sd   Q2.5  Q50 Q97.5
1 0.98 13.03 -25.72 0.15  31.2

```
`ggplot(data = data.frame(fish_diff1),
       aes(x = fish_diff1)) +
  geom_histogram(color = "black",
                 fill = "gray",
                 binwidth = 0.75) +
  scale_x_continuous("Score diffence between pike and pikeperch",
                     breaks = seq(from = -20,
                                  to = 20,
                                  by = 10)) +
  geom_vline(xintercept = c(mean(fish_diff1),
                            quantile(fish_diff1,
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

![M31_s2](/Plots/M31_s2.png "M31_s2")

The expected difference comes to 0.45 with a standard deviation of 3.39 and a wide range of uncertainty. The 95% credible interval is [-25.72,31.2], so we are 95% certain that the true value of the difference between the two species lies within the range, given the data

We also can get the proportion of the time that pike has a higher mean than pikeperch:

`prop.table(table(fish_inter[, 1] > fish_inter[, 2]))`

```
  FALSE    TRUE
0.47975 0.52025
```

This means that the posterior probability that pike is better than pikeperch is 52%. Any pair of species within the sample of species can be compared in this manner.

- The difference between the pikeperch and wels averages is:

`fish_diff2 <- fish_inter[, 2] - fish_inter[, 3]`

`mean <- mean(fish_diff2)`
`sd <- sd(fish_diff2)`
`quantile <- quantile(fish_diff2, probs = c(0.025, 0.50, 0.975))`
`quantile <- data.frame(t(quantile))`
`names(quantile) <- c("Q2.5", "Q50", "Q97.5")`
`diff_df2 <- data.frame(mean, sd, quantile)`
`round(diff_df2, 2)`

```
   mean    sd   Q2.5   Q50 Q97.5
1 -0.49 14.42 -34.97 -0.08 30.33

```
`ggplot(data = data.frame(fish_diff2),
       aes(x = fish_diff2)) +
  geom_histogram(color = "black",
                 fill = "gray",
                 binwidth = 0.75) +
  scale_x_continuous("Score diffence between pikeperch and wels",
                     breaks = seq(from = -20,
                                  to = 20,
                                  by = 10)) +
  geom_vline(xintercept = c(mean(fish_diff2),
                            quantile(fish_diff2,
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

![M31_s3](/Plots/M31_s3.png "M31_s3")

- The difference between the pike and wels averages is:

`fish_diff3 <- fish_inter[, 1] - fish_inter[, 3]`

`mean <- mean(fish_diff3)`
`sd <- sd(fish_diff3)`
`quantile <- quantile(fish_diff3, probs = c(0.025, 0.50, 0.975))`
`quantile <- data.frame(t(quantile))`
`names(quantile) <- c("Q2.5", "Q50", "Q97.5")`
`diff_df3 <- data.frame(mean, sd, quantile)`
`round(diff_df3, 2)`

```
  mean sd   Q2.5  Q50 Q97.5
1 0.49 13 -27.49 0.01 30.24

```
`ggplot(data = data.frame(fish_diff3),
       aes(x = fish_diff3)) +
  geom_histogram(color = "black",
                 fill = "gray",
                 binwidth = 0.75) +
  scale_x_continuous("Score diffence between pike and wels",
                     breaks = seq(from = -20,
                                  to = 20,
                                  by = 10)) +
  geom_vline(xintercept = c(mean(fish_diff3),
                            quantile(fish_diff3,
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

![M31_s4](/Plots/M31_s4.png "M31_s4")


### Cross-validation checking

We can use Pareto-smoothed importance sampling LOO CV as model checking tool (2017b)

`library(loo)`

`loo<-loo(m2, cores = getOption("mc.cores", 4))`

`loo`

```
Computed from 4000 by 8846 log-likelihood matrix

         Estimate    SE
elpd_loo -35704.5  74.3
p_loo        44.5   0.9
looic     71409.0 148.6
------
Monte Carlo SE of elpd_loo is 0.1.

All Pareto k estimates are good (k < 0.5).
See help('pareto-k-diagnostic') for details.
```

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

- First, create a *loo* object

- Returns an object of class *pareto_k_table*, which is a matrix with columns "Count", "Proportion", and "Min. n_eff", and has its own print method

`plot(loo, diagnostic = c("k", "n_eff"), label_points = FALSE, main = "PSIS diagnostic plot")`

![M31_s5](/Plots/M31_s5.png "M31_s5")

There are no highly influential observations, which indicates that the model is correct (i.e. there is no misspecification, Vehtari, Gelman and Gabry, 2017).


### Check what would CV say about relevance of covariates

We form 3 models by dropping each of the covariates out

## NOT HERE!!!!!

### Posterior predictive checking

In addition to LOO CV, posterior predictive checks can often detect problems and also provide more information about the reason. Here we test the proportion of 1's predicted by the model and compare them to the observed number of 1's.

`prop_zero <- function(y) mean(y == 1)`

`(prop_zero_test1 <- pp_check(m2, plotfun = "stat", stat = "prop_zero"))`

![M31_s6](/Plots/M31_s6.png "M31_s6")


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

### Analyse posterior

#### Plot posterior

- Including random effects from *fi_species*

`library(bayesplot)`

`mcmc_areas(as.matrix(m2), prob_outer = .999)`

![M31_s7](/Plots/M31_s7.png "M31_s7")

- Excluding random effects from *fi_species*

`mcmc_areas(as.matrix(m2), prob_outer = .999, pars = c("(Intercept)","seasonwinter:fi_specieswels","seasonspring_II:fi_specieswels","seasonspring_I:fi_specieswels",
"seasonwinter:fi_speciespikeperch","seasonsummer:fi_speciespikeperch","seasonspring_II:fi_speciespikeperch",
"seasonspring_I:fi_speciespikeperch","fi_specieswels","fi_speciespikeperch","seasonwinter","seasonsummer",
"seasonspring_II","seasonspring_I"))`

![M31_s8](/Plots/M31_s8.png "M31_s8")

- *seasonspring_I:fi_specieswels*,*seasonsummer:fi_speciespikeperch*, *seasonspring_I:fi_speciespikeperch*, *seasonspring_II:fi_speciespikeperch* are marginal     significantly away from zero
- *fi_specieswels* and *fi_speciespikeperch* have both lot of probability mass on both sides of 0
- *seasonspring_I*, *seasonwinter:fi_speciespikeperch*  and *seasonspring_II:fi_specieswels* are much closer to 0


#### Plot paired marginals

`mcmc_pairs(as.matrix(m31),pars = c("(Intercept)","seasonwinter:fi_specieswels","seasonspring_II:fi_specieswels","seasonspring_I:fi_specieswels",
"seasonwinter:fi_speciespikeperch","seasonsummer:fi_speciespikeperch","seasonspring_II:fi_speciespikeperch",
"seasonspring_I:fi_speciespikeperch","fi_specieswels","fi_speciespikeperch","seasonwinter","seasonsummer",
"seasonspring_II","seasonspring_I"))`

![M31_s9](/Plots/M31_s9.png "M31_s9")

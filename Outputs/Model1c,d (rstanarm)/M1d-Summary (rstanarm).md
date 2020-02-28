
`m1d<-stan_lmer(formula= sqrt(ranged2d + 1) ~ 1 + season + (1 | fi_species), data = mean.ranged2d, seed=350,control = list(max_treedepth=20,adapt_delta = 0.95))`

### Model 1d results

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
Chain 1:  Elapsed Time: 201.085 seconds (Warm-up)
Chain 1:                183.362 seconds (Sampling)
Chain 1:                384.447 seconds (Total)
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
Chain 2:  Elapsed Time: 211.233 seconds (Warm-up)
Chain 2:                177.795 seconds (Sampling)
Chain 2:                389.028 seconds (Total)
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
Chain 3:  Elapsed Time: 176.224 seconds (Warm-up)
Chain 3:                182.523 seconds (Sampling)
Chain 3:                358.747 seconds (Total)
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
Chain 4:  Elapsed Time: 179.575 seconds (Warm-up)
Chain 4:                184.051 seconds (Sampling)
Chain 4:                363.626 seconds (Total)
Chain 4:
Warning messages:
1: There were 15 divergent transitions after warmup. Increasing adapt_delta above 0.95 may help. See
http://mc-stan.org/misc/warnings.html#divergent-transitions-after-warmup
2: Examine the pairs() plot to diagnose sampling problems

```

### Quick Summary

`print(m1d, digits = 2)`

```
stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + season + (1 | fi_species)
 observations: 8846
------
                Median MAD_SD
(Intercept)     24.62   2.57
seasonspring_I   2.61   0.50
seasonspring_II  6.93   1.01
seasonsummer     1.23   0.42
seasonwinter     2.08   0.45

Auxiliary parameter(s):
      Median MAD_SD
sigma 15.25   0.12

Error terms:
 Groups     Name        Std.Dev.
 fi_species (Intercept)  7.18
 Residual               15.25
Num. levels: fi_species 3

------
* For help interpreting the printed output see ?print.stanreg
* For info on the priors used see ?prior_summary.stanreg
```

`summary(m1d)`

```
Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + season + (1 | fi_species)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_species (3)

Estimates:
                                            mean   sd    10%   50%   90%
(Intercept)                                24.6    4.0  20.6  24.6  28.5
seasonspring_I                              2.6    0.5   2.0   2.6   3.2
seasonspring_II                             6.9    1.0   5.6   6.9   8.2
seasonsummer                                1.2    0.4   0.7   1.2   1.8
seasonwinter                                2.1    0.5   1.5   2.1   2.7
b[(Intercept) fi_species:pike]              0.6    4.0  -3.3   0.5   4.5
b[(Intercept) fi_species:pikeperch]        -2.9    4.0  -6.9  -2.9   1.1
b[(Intercept) fi_species:wels]              2.7    4.0  -1.2   2.6   6.7
sigma                                      15.3    0.1  15.1  15.3  15.4
Sigma[fi_species:(Intercept),(Intercept)]  51.5   96.8   4.8  19.1 124.3

Fit Diagnostics:
           mean   sd   10%   50%   90%
mean_PPD 26.9    0.2 26.6  26.9  27.2

The mean_ppd is the sample average posterior predictive distribution of the outcome variable (for details see help('summary.stanreg')).

MCMC diagnostics
                                          mcse Rhat n_eff
(Intercept)                               0.1  1.0   861
seasonspring_I                            0.0  1.0  2815
seasonspring_II                           0.0  1.0  3167
seasonsummer                              0.0  1.0  2794
seasonwinter                              0.0  1.0  2883
b[(Intercept) fi_species:pike]            0.1  1.0   864
b[(Intercept) fi_species:pikeperch]       0.1  1.0   863
b[(Intercept) fi_species:wels]            0.1  1.0   861
sigma                                     0.0  1.0  4014
Sigma[fi_species:(Intercept),(Intercept)] 2.7  1.0  1328
mean_PPD                                  0.0  1.0  3601
log-posterior                             0.1  1.0  1278

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).

```

### Check priors used

`prior_summary(object = m1d)`

```{r, echo=FALSE, eval=TRUE}

Priors for model 'm1d'
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

`summary(m1d, pars = c("(Intercept)", "sigma", "Sigma[fi_species:(Intercept),(Intercept)]"), probs = c(0.025, 0.975), digits = 2)`

```
Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + season + (1 | fi_species)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_species (3)

Estimates:
                                            mean   sd     2.5%   97.5%
(Intercept)                                24.59   3.97  15.41  32.81
sigma                                      15.25   0.12  15.02  15.48
Sigma[fi_species:(Intercept),(Intercept)]  51.48  96.75   2.74 328.59

MCMC diagnostics
                                          mcse Rhat n_eff
(Intercept)                               0.14 1.00  861
sigma                                     0.00 1.00 4014
Sigma[fi_species:(Intercept),(Intercept)] 2.65 1.00 1328

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).
```

### Extract the posterior draws for all parameters

`sims <- as.matrix(m1d)`

`dim(sims)`

```
[1] 4000   10

```
`para_name <- colnames(sims)`

`para_name`

```
[1] "(Intercept)"                               "seasonspring_I"
 [3] "seasonspring_II"                           "seasonsummer"
 [5] "seasonwinter"                              "b[(Intercept) fi_species:pike]"
 [7] "b[(Intercept) fi_species:pikeperch]"       "b[(Intercept) fi_species:wels]"
 [9] "sigma"                                     "Sigma[fi_species:(Intercept),(Intercept)]"
```

- Obtain fi_species-level varying intercept a_j

 - draws for overall mean

`mu_a_sims <- as.matrix(m1d, pars = "(Intercept)")`

 - draws for 3 fi_species-level error

`fish_err <- as.matrix(m1d,regex_pars = "b\\[\\(Intercept\\) fi_species\\:")`

 - draws for 3 species' varying intercepts

`fish_inter <- as.numeric(mu_a_sims) + fish_err`

- Obtain sigma_y and sigma_alpha^2

 -draws for sigma_y

`signma_y <- as.matrix(m1d, pars = "sigma")`

 - draws for sigma_alpha^2

`sigma_alpha <- as.matrix(m1d, pars = "Sigma[fi_species:(Intercept),(Intercept)]")`


### Obtaining means, SD, medians and 95% credible intervals of varying intercepts

In *fish_inter*, we have saved 4,000 posterior draws (from all 4 chains) for the varying intercepts αj of the 30 fishes. For example, the first column of the 4,000 by 30 matrix is a vector of 4,000 posterior simulation draws for the first fish's (*fi_species*:T449202_1) varying intercept α1. One quantitative way to summarize the posterior probability distribution of these 4,000 estimates for α1 is to examine their quantiles.

- Posterior mean of each alpha

`a_mean <- apply(X = fish_inter, MARGIN = 2, FUN = mean)`

`a_mean'

```
   b[(Intercept) fi_species:pike] b[(Intercept) fi_species:pikeperch]      b[(Intercept) fi_species:wels]
                           25.15574                            21.67753                            27.28541
```

- Posterior SD of each alpha

`a_sd <- apply(X = fish_inter, MARGIN = 2, FUN = sd)`

`a_sd`

```
   b[(Intercept) fi_species:pike] b[(Intercept) fi_species:pikeperch]      b[(Intercept) fi_species:wels]
                          0.3844493                           0.4452545                           0.3469269

```

- Posterior median and 95% credible interval

`a_quant <- apply(X = fish_inter, MARGIN = 2, FUN = quantile, probs = c(0.025, 0.50, 0.975))`

`a_quant <- data.frame(t(a_quant))`

`a_quant`

```
                                       X2.5.     X50.   X97.5.
b[(Intercept) fi_species:pike]      24.40116 25.15856 25.89552
b[(Intercept) fi_species:pikeperch] 20.80365 21.67576 22.53533
b[(Intercept) fi_species:wels]      26.59435 27.29038 27.95141
```

`names(a_quant) <- c("X2.5.", "X50.", "X97.5.")`

- Combine summary statistics of posterior simulation draws

`a_df <- data.frame(a_mean, a_sd, a_quant)`

`round(head(a_df), 2)`

```
                                    a_mean a_sd X2.5.  X50. X97.5.
b[(Intercept) fi_species:pike]       25.16 0.38 24.40 25.16  25.90
b[(Intercept) fi_species:pikeperch]  21.68 0.45 20.80 21.68  22.54
b[(Intercept) fi_species:wels]       27.29 0.35 26.59 27.29  27.95

```

### Plots

Caterpillar plot to show the fully Bayes estimates for the *fi_species* varying intercepts in rank order together with their 95% credible interval

- Sort dataframe containing an estimated alpha's mean and sd for every fi_species

`a_df <- a_df[order(a_df$a_mean), ]`

`a_df`

```
                                      a_mean      a_sd    X2.5.     X50.   X97.5.
b[(Intercept) fi_species:pikeperch] 21.67753 0.4452545 20.80365 21.67576 22.53533
b[(Intercept) fi_species:pike]      25.15574 0.3844493 24.40116 25.15856 25.89552
b[(Intercept) fi_species:wels]      27.28541 0.3469269 26.59435 27.29038 27.95141

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
![M1d_s1](/Plots/M1d_s1.png "M1d_s1")

### Differences between species averages

Here I'll explore the posterior distribution of the differences between each two species with descriptive statistics and histograms

- - The difference between the pike and pikeperch averages is:

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
1 3.39 0.46 2.48 3.39  4.29

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
  geom_text(aes(0.45, 20, label = "mean = 3.39"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 0.46"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M1d_s2](/Plots/M1d_s2.png "M1d_s2")

The expected difference comes to 3.30 with a standard deviation of 0.46 and a wide range of uncertainty. The 95% credible interval is [2.48,4.29], so we are 95% certain that the true value of the difference between the two species lies within the range, given the data

We also can get the proportion of the time that pike has a higher mean than pikeperch:

`prop.table(table(fish_inter[, 1] > fish_inter[, 2]))`

```
TRUE
   1
```

This means that the posterior probability that pike is better than pikeperch is 100%. Any pair of fishes within the sample of fishes can be compared in this manner.

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
   mean   sd  Q2.5   Q50 Q97.5
1 -5.61 0.43 -6.49 -5.61 -4.82
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
  geom_text(aes(0.45, 20, label = "mean = -5.61"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 0.43"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M1d_s3](/Plots/M1d_s3.png "M1d_s3")

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
  mean   sd  Q2.5   Q50 Q97.5
1 -2.13 0.37 -2.82 -2.13  -1.4
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
  geom_text(aes(0.45, 20, label = "mean = -2.13"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 0.37"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M1d_s4](/Plots/M1d_s4.png "M1d_s4")

### Cross-validation checking

We can use Pareto-smoothed importance sampling LOO CV as model checking tool (2017b)

`library(loo)`

`loo<-loo(m1d)`

`loo`

```
Computed from 4000 by 8846 log-likelihood matrix

         Estimate    SE
elpd_loo -36660.1  65.5
p_loo         8.2   0.2
looic     73320.3 131.1
------
Monte Carlo SE of elpd_loo is 0.0.

All Pareto k estimates are good (k < 0.5).
See help('pareto-k-diagnostic') for details.
```
`plot(loo, diagnostic = c("k", "n_eff"), label_points = FALSE, main = "PSIS diagnostic plot")`

![M1d_sloo](/Plots/M1d_sloo.png "M1d_sloo")

### Analyse posterior

#### Plot posterior

- Including random effects from *fi_species*

`library(bayesplot)`

`mcmc_areas(as.matrix(m1d), prob_outer = .999)`

![M1d_s5](/Plots/M1d_s5.png "M1d_s5")

- Excluding random effects from *fi_species*

`mcmc_areas(as.matrix(m1d), prob_outer = .999, pars = c("(Intercept)","b[(Intercept) fi_species:pike]", "b[(Intercept) fi_species:pikeperch]", "b[(Intercept) fi_species:wels]"))`

![M1d_s6](/Plots/M1d_s6.png "M1d_s6")

#### Plot paired marginals

`mcmc_pairs(as.matrix(m1d), pars = c("(Intercept)","b[(Intercept) fi_species:pike]", "b[(Intercept) fi_species:pikeperch]", "b[(Intercept) fi_species:wels]"))`

![M1d_s7](/Plots/M1d_s7.png "M1d_s7")
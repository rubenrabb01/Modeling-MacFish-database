`m6<-stan_lmer(formula=sqrt(ranged2d + 1) ~ 1 + fi_species +(1 + season|fi_fishid), data = mean.ranged2d, seed=350)`

### Model 1c results

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
Chain 1:  Elapsed Time: 114.942 seconds (Warm-up)
Chain 1:                103.462 seconds (Sampling)
Chain 1:                218.404 seconds (Total)
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
Chain 2:  Elapsed Time: 149.51 seconds (Warm-up)
Chain 2:                116.521 seconds (Sampling)
Chain 2:                266.031 seconds (Total)
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
Chain 3:  Elapsed Time: 127.362 seconds (Warm-up)
Chain 3:                114.854 seconds (Sampling)
Chain 3:                242.216 seconds (Total)
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
Chain 4:  Elapsed Time: 175.44 seconds (Warm-up)
Chain 4:                102.1 seconds (Sampling)
Chain 4:                277.54 seconds (Total)
Chain 4:
Warning messages:
1: There were 14 divergent transitions after warmup. Increasing adapt_delta above 0.95 may help. See
http://mc-stan.org/misc/warnings.html#divergent-transitions-after-warmup
2: Examine the pairs() plot to diagnose sampling problems

```

### Quick Summary

`print(m1c, digits = 2)`

```
stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + (1 | fi_species)
 observations: 8846
------
            Median MAD_SD
(Intercept) 26.29   2.19

Auxiliary parameter(s):
      Median MAD_SD
sigma 15.31   0.11

Error terms:
 Groups     Name        Std.Dev.
 fi_species (Intercept)  7.17
 Residual               15.31
Num. levels: fi_species 3

------
* For help interpreting the printed output see ?print.stanreg
* For info on the priors used see ?prior_summary.stanreg
```

`summary(m1c)`

```
Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + (1 | fi_species)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_species (3)

Estimates:
                                            mean   sd    10%   50%   90%
(Intercept)                                26.2    3.5  22.7  26.3  29.9
b[(Intercept) fi_species:pike]              0.4    3.5  -3.3   0.3   4.0
b[(Intercept) fi_species:pikeperch]        -3.0    3.5  -6.8  -3.0   0.6
b[(Intercept) fi_species:wels]              2.5    3.5  -1.1   2.5   6.1
sigma                                      15.3    0.1  15.2  15.3  15.5
Sigma[fi_species:(Intercept),(Intercept)]  51.4  109.7   4.3  16.6 119.2

Fit Diagnostics:
           mean   sd   10%   50%   90%
mean_PPD 26.9    0.2 26.6  26.9  27.2

The mean_ppd is the sample average posterior predictive distribution of the outcome variable (for details see help('summary.stanreg')).

MCMC diagnostics
                                          mcse Rhat n_eff
(Intercept)                               0.1  1.0   844
b[(Intercept) fi_species:pike]            0.1  1.0   849
b[(Intercept) fi_species:pikeperch]       0.1  1.0   856
b[(Intercept) fi_species:wels]            0.1  1.0   846
sigma                                     0.0  1.0  1947
Sigma[fi_species:(Intercept),(Intercept)] 6.0  1.0   335
mean_PPD                                  0.0  1.0  3138
log-posterior                             0.1  1.0   959

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).

```

### Check priors used

`prior_summary(object = m1c)`

```{r, echo=FALSE, eval=TRUE}

Priors for model 'm1c'
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

`summary(m1c, pars = c("(Intercept)", "sigma", "Sigma[fi_species:(Intercept),(Intercept)]"), probs = c(0.025, 0.975), digits = 2)`

```
Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + (1 | fi_species)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_species (3)

Estimates:
                                            mean   sd     2.5%   97.5%
(Intercept)                                26.24   3.53  18.33  33.43
sigma                                      15.31   0.12  15.08  15.55
Sigma[fi_species:(Intercept),(Intercept)]  51.37 109.70   2.45 365.83

MCMC diagnostics
                                          mcse Rhat n_eff
(Intercept)                               0.12 1.00  844
sigma                                     0.00 1.00 1947
Sigma[fi_species:(Intercept),(Intercept)] 6.00 1.01  335

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).
```

### Extract the posterior draws for all parameters

`sims <- as.matrix(m1c)`

`dim(sims)`

```
[1] 4000   6

```

`para_name <- colnames(sims)`

`para_name`

```
[1] "(Intercept)"                               "b[(Intercept) fi_species:pike]"
[3] "b[(Intercept) fi_species:pikeperch]"       "b[(Intercept) fi_species:wels]"
[5] "sigma"                                     "Sigma[fi_species:(Intercept),(Intercept)]"
```

- Obtain fi_species-level varying intercept a_j

 - draws for overall mean

`mu_a_sims <- as.matrix(m1c, pars = "(Intercept)")`

 - draws for 3 fi_species-level error

`fish_err <- as.matrix(m1c,regex_pars = "b\\[\\(Intercept\\) fi_species\\:")`

 - draws for 3 species' varying intercepts

`fish_inter <- as.numeric(mu_a_sims) + fish_err`

- Obtain sigma_y and sigma_alpha^2

 -draws for sigma_y

`signma_y <- as.matrix(m1c, pars = "sigma")`

 - draws for sigma_alpha^2

`sigma_alpha <- as.matrix(m1c, pars = "Sigma[fi_species:(Intercept),(Intercept)]")`


### Obtaining means, SD, medians and 95% credible intervals of varying intercepts

In *fish_inter*, we have saved 4,000 posterior draws (from all 4 chains) for the varying intercepts αj of the 30 fishes. For example, the first column of the 4,000 by 30 matrix is a vector of 4,000 posterior simulation draws for the first fish's (*fi_species*:T449202_1) varying intercept α1. One quantitative way to summarize the posterior probability distribution of these 4,000 estimates for α1 is to examine their quantiles.

- Posterior mean of each alpha

`a_mean <- apply(X = fish_inter, MARGIN = 2, FUN = mean)`

`a_mean'

```
     b[(Intercept) fi_species:pike] b[(Intercept) fi_species:pikeperch]      b[(Intercept) fi_species:wels]
                           26.62619                            23.23149                            28.77130
```

- Posterior SD of each alpha

`a_sd <- apply(X = fish_inter, MARGIN = 2, FUN = sd)`

`a_sd`

```
     b[(Intercept) fi_species:pike] b[(Intercept) fi_species:pikeperch]      b[(Intercept) fi_species:wels]
                          0.3000263                           0.3553515                           0.2430700
```

- Posterior median and 95% credible interval

`a_quant <- apply(X = fish_inter, MARGIN = 2, FUN = quantile, probs = c(0.025, 0.50, 0.975))`

`a_quant <- data.frame(t(a_quant))`

`a_quant`

```
                                       X2.5.     X50.   X97.5.
b[(Intercept) fi_species:pike]      26.05569 26.62843 27.22255
b[(Intercept) fi_species:pikeperch] 22.56412 23.23092 23.91960
b[(Intercept) fi_species:wels]      28.30660 28.77097 29.25222
```

`names(a_quant) <- c("X2.5.", "X50.", "X97.5.")`

- Combine summary statistics of posterior simulation draws

`a_df <- data.frame(a_mean, a_sd, a_quant)`

`round(head(a_df), 2)`

```
                                    a_mean a_sd X2.5.  X50. X97.5.
b[(Intercept) fi_species:pike]       26.63 0.30 26.06 26.63  27.22
b[(Intercept) fi_species:pikeperch]  23.23 0.36 22.56 23.23  23.92
b[(Intercept) fi_species:wels]       28.77 0.24 28.31 28.77  29.25

```

### Plots

Caterpillar plot to show the fully Bayes estimates for the *fi_species* varying intercepts in rank order together with their 95% credible interval

- Sort dataframe containing an estimated alpha's mean and sd for every fi_species

`a_df <- a_df[order(a_df$a_mean), ]`

`a_df`
```
                          a_mean      a_sd    X2.5.     X50.   X97.5.
b[(Intercept) fi_species:pikeperch] 23.23149 0.3553515 22.56412 23.23092 23.91960
b[(Intercept) fi_species:pike]      26.62619 0.3000263 26.05569 26.62843 27.22255
b[(Intercept) fi_species:wels]      28.77130 0.2430700 28.30660 28.77097 29.25222
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
![M1c_s1](/Plots/M1c_s1.png "M1c_s1")

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

![M1c_s2](/Plots/M1c_s2.png "M1c_s2")

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
1 -5.54 0.43 -6.39 -5.53 -4.71
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
  geom_text(aes(0.45, 20, label = "mean = -5.54"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 0.43"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M1c_s3](/Plots/M1c_s3.png "M1c_s3")

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
1 -2.15 0.39 -2.89 -2.15 -1.38
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
  geom_text(aes(0.45, 20, label = "mean = -2.15"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 0.39"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M1c_s4](/Plots/M1c_s4.png "M1c_s4")

### Cross-validation checking

We can use Pareto-smoothed importance sampling LOO CV as model checking tool (2017b)

`library(loo)`

`loo<-loo(m1c)`

`loo`

```
Computed from 4000 by 8846 log-likelihood matrix

         Estimate    SE
elpd_loo -35890.2  72.5
p_loo        31.6   0.6
looic     71780.5 144.9
------
Monte Carlo SE of elpd_loo is 0.1.

All Pareto k estimates are good (k < 0.5).
See help('pareto-k-diagnostic') for details.
```
`plot(loo, diagnostic = c("k", "n_eff"), label_points = FALSE, main = "PSIS diagnostic plot")`

![M1c_sloo](/Plots/M1c_sloo.png "M1c_sloo")


### Analyse posterior

#### Plot posterior

- Including random effects from *fi_species*

`library(bayesplot)`

`mcmc_areas(as.matrix(m1c), prob_outer = .999)`

![M1c_s5](/Plots/M1c_s5.png "M1c_s5")

- Excluding random effects from *fi_species*

`mcmc_areas(as.matrix(m1c), prob_outer = .999, pars = c("(Intercept)","b[(Intercept) fi_species:pike]", "b[(Intercept) fi_species:pikeperch]", "b[(Intercept) fi_species:wels]"))`

![M1c_s6](/Plots/M1c_s6.png "M1c_s6")

- *fi_species:26*,*fi_species:27*,*fi_species:28*,*fi_species:10*,*fi_species:19*,*fi_species:18* have both lot of probability mass on both sides of 0


#### Plot paired marginals

`mcmc_pairs(as.matrix(m1c), pars = c("(Intercept)","b[(Intercept) fi_species:pike]", "b[(Intercept) fi_species:pikeperch]", "b[(Intercept) fi_species:wels]"))`

![M1c_s7](/Plots/M1c_s7.png "M1c_s7")

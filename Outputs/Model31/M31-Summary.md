`m31<-brm(sqrt(ranged2d + 1) ~ 1 + season*fi_species +(1|fi_species), data = mean.ranged2d, family="gaussian", prior = prior, warmup = 1000, iter = 2000, cores = 4, chains = 4, control = list(adapt_delta = 0.95))`

`summary(m31)`
```
 Family: gaussian 
  Links: mu = identity; sigma = identity 
Formula: sqrt(ranged2d + 1) ~ 1 + season * fi_species + (1 | fi_species) 
   Data: mean.ranged2d (Number of observations: 8846) 
Samples: 4 chains, each with iter = 2000; warmup = 1000; thin = 1;
         total post-warmup samples = 4000

Group-Level Effects: 
~fi_species (Number of levels: 3) 
              Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
sd(Intercept)     7.18      6.14     0.20    23.31 1.00      956     1140

Population-Level Effects: 
                                    Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
Intercept                              23.36      6.35     6.41    32.15 1.01     1270     1100
seasonspring_I                         -2.27      0.90    -4.04    -0.57 1.00     2046     2704
seasonspring_II                        11.13      1.95     7.46    15.11 1.00     1975     2367
seasonsummer                           -4.06      0.79    -5.61    -2.53 1.00     1707     2302
seasonwinter                            3.59      0.81     2.02     5.22 1.00     2073     2365
fi_speciespikeperch                    -3.09      4.78   -12.20     5.33 1.00     1459     2135
fi_specieswels                          0.39      4.48    -8.03     8.90 1.00     2099     1131
seasonspring_I:fi_speciespikeperch      7.81      1.39     5.00    10.52 1.00     2088     2773
seasonspring_II:fi_speciespikeperch    -9.77      2.92   -15.56    -4.03 1.00     2178     2748
seasonsummer:fi_speciespikeperch       13.78      1.21    11.47    16.21 1.00     1875     2203
seasonwinter:fi_speciespikeperch       -3.12      1.31    -5.68    -0.59 1.00     2337     2168
seasonspring_I:fi_specieswels           6.84      1.13     4.65     9.05 1.00     2322     2793
seasonspring_II:fi_specieswels         -3.67      2.38    -8.51     0.61 1.00     2107     2417
seasonsummer:fi_specieswels             4.80      1.01     2.86     6.78 1.00     1779     2206
seasonwinter:fi_specieswels            -1.70      1.01    -3.75     0.19 1.00     2147     2100

Family Specific Parameters: 
      Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
sigma    14.99      0.11    14.77    15.21 1.00     4588     2783

Samples were drawn using sampling(NUTS). For each parameter, Eff.Sample 
is a crude measure of effective sample size, and Rhat is the potential 
scale reduction factor on split chains (at convergence, Rhat = 1).
Warning message:
There were 42 divergent transitions after warmup. Increasing adapt_delta above 0.95 may help.
See http://mc-stan.org/misc/warnings.html#divergent-transitions-after-warmup
```

`loo(m31, cores = getOption("mc.cores", 1))`

```
Computed from 4000 by 8846 log-likelihood matrix
         Estimate    SE
elpd_loo -36508.9  66.4
p_loo        16.6   0.5
looic     73017.8 132.8
------
Monte Carlo SE of elpd_loo is 0.1.

All Pareto k estimates are good (k < 0.5).
See help('pareto-k-diagnostic') for details.
```

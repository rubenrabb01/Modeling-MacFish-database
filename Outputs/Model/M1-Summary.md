### Bayesian mixed-effects model fitted with brms

`m1<-brm(sqrt(ranged2d + 1) ~ 1 + season*fi_species, 
          data = mean.ranged2d, family="gaussian", prior = prior, warmup = 1000, iter = 2000, cores = 4, chains = 4)`

`summary(m1)`

```
Family: gaussian 
  Links: mu = identity; sigma = identity 
Formula: sqrt(ranged2d + 1) ~ 1 + season * fi_species 
   Data: mean.ranged2d (Number of observations: 8846) 
Samples: 4 chains, each with iter = 2000; warmup = 1000; thin = 1;
         total post-warmup samples = 4000

Population-Level Effects: 
                                    Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
Intercept                              27.18      0.54    26.11    28.24 1.00     1930     2575
seasonspring_I                         -2.24      0.90    -4.05    -0.52 1.00     2132     2791
seasonspring_II                        11.17      1.99     7.40    15.16 1.00     2435     2723
seasonsummer                           -4.06      0.77    -5.55    -2.54 1.00     2218     2610
seasonwinter                            3.62      0.83     2.04     5.28 1.00     2610     3060
fi_speciespikeperch                    -8.35      0.91   -10.11    -6.54 1.00     2072     2611
fi_specieswels                         -0.05      0.67    -1.36     1.29 1.00     1886     2485
seasonspring_I:fi_speciespikeperch      7.75      1.44     4.92    10.45 1.00     2360     2885
seasonspring_II:fi_speciespikeperch    -9.77      3.02   -15.67    -3.78 1.00     2720     2881
seasonsummer:fi_speciespikeperch       13.78      1.22    11.40    16.20 1.00     2091     2311
seasonwinter:fi_speciespikeperch       -3.17      1.34    -5.89    -0.67 1.00     2539     3101
seasonspring_I:fi_specieswels           6.82      1.13     4.62     9.03 1.00     2157     2573
seasonspring_II:fi_specieswels         -3.72      2.40    -8.61     0.55 1.00     2458     2835
seasonsummer:fi_specieswels             4.79      0.96     2.84     6.65 1.00     2189     2563
seasonwinter:fi_specieswels            -1.74      1.02    -3.76     0.12 1.00     2474     2696

Family Specific Parameters: 
      Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
sigma    14.99      0.11    14.77    15.21 1.00     6069     2894

Samples were drawn using sampling(NUTS). For each parameter, Eff.Sample 
is a crude measure of effective sample size, and Rhat is the potential 
scale reduction factor on split chains (at convergence, Rhat = 1).
```

`loo(m1, cores = getOption("mc.cores", 1))`

```
Computed from 4000 by 8846 log-likelihood matrix

         Estimate    SE
elpd_loo -36508.6  66.4
p_loo        16.3   0.5
looic     73017.2 132.8
------
Monte Carlo SE of elpd_loo is 0.1.

All Pareto k estimates are good (k < 0.5).
See help('pareto-k-diagnostic') for details.
````

`plot(m1)`

![M1_1](/Plots/M3_1.png "M1_1")
![M1_2](/Plots/M3_2.png "M1_2")
![M1_3](/Plots/M3_3.png "M1_3")
![M1_4](/Plots/M3_4.png "M1_4")

`stanplot(m1, pars = c("b_Intercept", "b_seasonwinter")) + xlim(c(0, 9))`

![M1_5](/Plots/M3_5.png "M1_5")

`plot(loo(m1, cores = getOption("mc.cores", 1)))`

![M1_6](/Plots/M3_6.png "M1_6")

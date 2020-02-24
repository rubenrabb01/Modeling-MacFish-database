
### Bayesian mixed-effects model fitted with brms  

`m3<-brm(sqrt(ranged2d + 1) ~ 1 + season*fi_species +(1|fi_species), 
        data = mean.ranged2d, family="gaussian", prior = prior, warmup = 1000, iter = 2000, cores = 4, chains = 4)`

`summary(m3)`

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
sd(Intercept)     6.30      5.40     0.25    19.35 1.02      363      786

Population-Level Effects: 
                                    Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
Intercept                              24.52      4.91    12.77    32.46 1.03     1068      257
seasonspring_I                         -2.24      0.94    -4.03    -0.40 1.01      540      130
seasonspring_II                        11.11      1.93     7.51    15.01 1.00     1473     2040
seasonsummer                           -4.09      0.80    -5.67    -2.50 1.00     1335     1590
seasonwinter                            3.63      0.82     2.01     5.26 1.00     1156     1915
fi_speciespikeperch                    -4.03      5.83   -21.44     4.72 1.01      394      136
fi_specieswels                          0.48      3.72    -7.59     8.71 1.00     1568     1001
seasonspring_I:fi_speciespikeperch      7.73      1.45     5.04    10.58 1.01      753      463
seasonspring_II:fi_speciespikeperch    -9.61      2.94   -15.44    -3.74 1.00     1650     2353
seasonsummer:fi_speciespikeperch       13.79      1.23    11.45    16.25 1.00     1486     2207
seasonwinter:fi_speciespikeperch       -3.19      1.31    -5.76    -0.64 1.00     1567     2477
seasonspring_I:fi_specieswels           6.82      1.20     4.55     9.10 1.01      684      316
seasonspring_II:fi_specieswels         -3.64      2.33    -8.64     0.41 1.00     1522     2153
seasonsummer:fi_specieswels             4.84      1.01     2.78     6.85 1.00     1462     2019
seasonwinter:fi_specieswels            -1.74      1.00    -3.77     0.10 1.00     1088     2383

Family Specific Parameters: 
      Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
sigma    14.98      0.11    14.76    15.21 1.00     3162     2766

Samples were drawn using sampling(NUTS). For each parameter, Eff.Sample 
is a crude measure of effective sample size, and Rhat is the potential 
scale reduction factor on split chains (at convergence, Rhat = 1).
Warning message:
There were 203 divergent transitions after warmup. Increasing adapt_delta above 0.8 may help.
See http://mc-stan.org/misc/warnings.html#divergent-transitions-after-warmup
```

`loo(m3, cores = getOption("mc.cores", 1))`

```
Computed from 4000 by 8846 log-likelihood matrix

         Estimate    SE
elpd_loo -36509.0  66.4
p_loo        16.7   0.5
looic     73018.1 132.7
------
Monte Carlo SE of elpd_loo is 0.1.

All Pareto k estimates are good (k < 0.5).
See help('pareto-k-diagnostic') for details.
````

plot(m3)

![M3_1](/Plots/M3_1.png "M3_1")
![M3_2](/Plots/M3_2.png "M3_2")
![M3_3](/Plots/M3_3.png "M3_3")
![M3_4](/Plots/M3_4.png "M3_4")
![M3_5](/Plots/M3_5.png "M3_5")
![M3_6](/Plots/M3_6.png "M3_6")


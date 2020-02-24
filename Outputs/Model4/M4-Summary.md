### Bayesian mixed-effects model fitted with brms 

`m4 <- brm(sqrt(ranged2d + 1) ~ 1 + season*fi_species +(1 | fi_fishid)+(1|fi_species), 
       data = mean.ranged2d, family="gaussian", prior = prior, warmup = 1000, iter = 4000, 
       cores = 4, chains = 4 ,control = list(max_treedepth=30,adapt_delta = 0.98))`                                       

### change max_treedepth, iterations and adapt_delta

`summary(m4)`

```
 Family: gaussian 
  Links: mu = identity; sigma = identity 
Formula: sqrt(ranged2d + 1) ~ 1 + season * fi_species + (1 | fi_fishid) + (1 | fi_species) 
   Data: mean.ranged2d (Number of observations: 8846) 
Samples: 4 chains, each with iter = 4000; warmup = 1000; thin = 1;
         total post-warmup samples = 12000

Group-Level Effects: 
~fi_fishid (Number of levels: 31) 
              Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
sd(Intercept)     6.93      0.97     5.34     9.17 1.00     3030     4701

~fi_species (Number of levels: 3) 
              Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
sd(Intercept)     7.28      7.42     0.24    26.13 1.00     1023      144

Population-Level Effects: 
                                    Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
Intercept                              22.28      7.52    -1.39    32.30 1.01      588      150
seasonspring_I                         -1.37      0.82    -3.04     0.18 1.00     2371     7857
seasonspring_II                        13.21      1.84     9.54    16.80 1.00     6908     7089
seasonsummer                           -2.99      0.70    -4.39    -1.63 1.00     5017     8038
seasonwinter                            3.32      0.76     1.84     4.84 1.00     6734     8360
fi_speciespikeperch                    -1.69      6.56   -12.59    10.06 1.01      552      129
fi_specieswels                          0.51      4.02    -7.43     9.02 1.00     7040     4662
seasonspring_I:fi_speciespikeperch      6.99      1.28     4.46     9.51 1.00     6781     7955
seasonspring_II:fi_speciespikeperch   -11.73      2.71   -17.03    -6.42 1.00     3220     7357
seasonsummer:fi_speciespikeperch       12.61      1.11    10.39    14.75 1.00     7394     7305
seasonwinter:fi_speciespikeperch       -2.96      1.21    -5.39    -0.67 1.00     7863     7678
seasonspring_I:fi_specieswels           6.35      1.04     4.34     8.41 1.00     7042     7923
seasonspring_II:fi_specieswels         -8.41      2.37   -12.92    -3.74 1.00     6465     7424
seasonsummer:fi_specieswels             4.01      0.90     2.27     5.78 1.00     5068     8037
seasonwinter:fi_specieswels            -2.45      0.95    -4.34    -0.64 1.00     6951     8286

Family Specific Parameters: 
      Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
sigma    13.67      0.10    13.47    13.87 1.00    10148     7141

Samples were drawn using sampling(NUTS). For each parameter, Eff.Sample 
is a crude measure of effective sample size, and Rhat is the potential 
scale reduction factor on split chains (at convergence, Rhat = 1).
Warning message:
There were 207 divergent transitions after warmup. Increasing adapt_delta above 0.98 may help.
```

`loo(m4, cores = getOption("mc.cores", 1))`

```
Computed from 12000 by 8846 log-likelihood matrix

         Estimate    SE
elpd_loo -35706.0  74.2
p_loo        44.9   0.9
looic     71411.9 148.5
------
Monte Carlo SE of elpd_loo is 0.1.
```



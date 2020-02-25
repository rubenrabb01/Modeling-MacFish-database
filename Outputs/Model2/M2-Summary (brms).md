### Bayesian mixed-effects model fitted with brms 

`m2<-brm(sqrt(ranged2d + 1) ~ 1 + season*fi_species +(1 | fi_fishid), data = mean.ranged2d, family="gaussian", prior = prior, warmup = 1000, iter = 2000, cores = 4, chains = 4)`



### Results for m2 fit

`summary(m2)`

```
Family: gaussian 
  Links: mu = identity; sigma = identity 
Formula: sqrt(ranged2d + 1) ~ 1 + season * fi_species + (1 | fi_fishid) 
   Data: mean.ranged2d (Number of observations: 8846) 
Samples: 4 chains, each with iter = 2000; warmup = 1000; thin = 1;
         total post-warmup samples = 4000

Group-Level Effects: 
~fi_fishid (Number of levels: 31) 
              Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
sd(Intercept)     6.88      0.98     5.28     9.08 1.00      982     1456

Population-Level Effects: 
                                    Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
Intercept                              25.61      1.84    21.95    29.23 1.00     1222     1552
seasonspring_I                         -1.37      0.82    -2.97     0.20 1.00     2811     3119
seasonspring_II                        13.25      1.89     9.54    16.91 1.00     3101     2789
seasonsummer                           -2.99      0.72    -4.42    -1.60 1.00     2744     2897
seasonwinter                            3.34      0.78     1.86     4.88 1.00     2713     2717
fi_speciespikeperch                    -4.80      3.18   -11.44     0.67 1.00     1208     1617
fi_specieswels                          0.77      1.96    -2.99     4.93 1.00     1710     1917
seasonspring_I:fi_speciespikeperch      6.90      1.26     4.47     9.33 1.00     3243     3224
seasonspring_II:fi_speciespikeperch   -11.83      2.75   -17.14    -6.35 1.00     3211     2870
seasonsummer:fi_speciespikeperch       12.51      1.12    10.34    14.66 1.00     3063     2768
seasonwinter:fi_speciespikeperch       -3.06      1.20    -5.46    -0.73 1.00     2979     2996
seasonspring_I:fi_specieswels           6.32      1.05     4.29     8.39 1.00     3023     2974
seasonspring_II:fi_specieswels         -8.50      2.42   -13.12    -3.65 1.00     3053     2707
seasonsummer:fi_specieswels             3.98      0.91     2.23     5.75 1.00     2691     2923
seasonwinter:fi_specieswels            -2.49      0.98    -4.45    -0.69 1.00     2822     2776

Family Specific Parameters: 
      Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
sigma    13.67      0.10    13.47    13.87 1.00     4815     2434

Samples were drawn using sampling(NUTS). For each parameter, Eff.Sample 
is a crude measure of effective sample size, and Rhat is the potential 
scale reduction factor on split chains (at convergence, Rhat = 1).
```


`loo(m2, cores = getOption("mc.cores", 1))`

```
Output of model 'm2':
Computed from 4000 by 8846 log-likelihood matrix

         Estimate    SE
elpd_loo -35706.0  74.2
p_loo        45.0   0.9
looic     71412.0 148.5
------
Monte Carlo SE of elpd_loo is 0.1.

All Pareto k estimates are good (k < 0.5).
See help('pareto-k-diagnostic') for details.
```

`plot(m2)`

![M2_1](/Plots/M2_1.png "M2_1")
![M2_2](/Plots/M2_2.png "M2_2")
![M2_3](/Plots/M2_3.png "M2_3")

`stanplot(m2, pars = c("b_Intercept", "b_seasonwinter")) + xlim(c(0, 9))`

![M2_4](/Plots/M2_4.png "M2_4")

`plot(loo(m2, cores = getOption("mc.cores", 1)))`

![M2_5](/Plots/M2_5.png "M2_5")

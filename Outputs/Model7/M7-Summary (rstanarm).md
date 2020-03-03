

`m7<-stan_lmer(formula=sqrt(ranged2d + 1) ~ 1 + r3_condition + season +  (1 + fi_species|fi_fishid), data = mean.ranged2d, seed=350)`

### Model 1c results

```
SAMPLING FOR MODEL 'continuous' NOW (CHAIN 1).
Chain 1:
Chain 1: Gradient evaluation took 0.001528 seconds
Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 15.28 seconds.
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
Chain 1:  Elapsed Time: 646.234 seconds (Warm-up)
Chain 1:                308.963 seconds (Sampling)
Chain 1:                955.197 seconds (Total)
Chain 1:

SAMPLING FOR MODEL 'continuous' NOW (CHAIN 2).
Chain 2:
Chain 2: Gradient evaluation took 0.00123 seconds
Chain 2: 1000 transitions using 10 leapfrog steps per transition would take 12.3 seconds.
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
Chain 2:  Elapsed Time: 751.157 seconds (Warm-up)
Chain 2:                585.824 seconds (Sampling)
Chain 2:                1336.98 seconds (Total)
Chain 2:

SAMPLING FOR MODEL 'continuous' NOW (CHAIN 3).
Chain 3:
Chain 3: Gradient evaluation took 0.00123 seconds
Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 12.3 seconds.
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
Chain 3:  Elapsed Time: 844.333 seconds (Warm-up)
Chain 3:                1185.52 seconds (Sampling)
Chain 3:                2029.85 seconds (Total)
Chain 3:

SAMPLING FOR MODEL 'continuous' NOW (CHAIN 4).
Chain 4:
Chain 4: Gradient evaluation took 0.001236 seconds
Chain 4: 1000 transitions using 10 leapfrog steps per transition would take 12.36 seconds.
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
Chain 4:  Elapsed Time: 798.128 seconds (Warm-up)
Chain 4:                1253.96 seconds (Sampling)
Chain 4:                2052.09 seconds (Total)
Chain 4:
```

### Quick Summary

`print(m7, digits = 2)`

```
stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + r3_condition + season + (1 + fi_species |
	   fi_fishid)
 observations: 8846
------
                Median MAD_SD
(Intercept)     25.03   1.32
r3_condition    -0.69   1.42
seasonspring_I   3.12   0.46
seasonspring_II  6.29   0.93
seasonsummer     1.63   0.41
seasonwinter     1.46   0.43

Auxiliary parameter(s):
      Median MAD_SD
sigma 13.91   0.10

Error terms:
 Groups    Name                Std.Dev. Corr
 fi_fishid (Intercept)          6.62
           fi_speciespikeperch  5.32    -0.06
           fi_specieswels       5.48    -0.15  0.06
 Residual                      13.91
Num. levels: fi_fishid 31

------
* For help interpreting the printed output see ?print.stanreg
* For info on the priors used see ?prior_summary.stanreg
```

`summary(m7)`

```
Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + r3_condition + season + (1 + fi_species |
	   fi_fishid)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
                                                           mean   sd    10%   50%   90%
(Intercept)                                               25.0    1.3  23.4  25.0  26.7
r3_condition                                              -0.7    1.5  -2.6  -0.7   1.1
seasonspring_I                                             3.1    0.5   2.5   3.1   3.7
seasonspring_II                                            6.3    0.9   5.1   6.3   7.5
seasonsummer                                               1.6    0.4   1.1   1.6   2.2
seasonwinter                                               1.5    0.4   0.9   1.5   2.0
b[(Intercept) fi_fishid:T449202_1]                        -3.1    4.1  -7.7  -3.3   1.7
b[fi_speciespikeperch fi_fishid:T449202_1]                -1.4    3.9  -6.2  -1.0   2.7
b[fi_specieswels fi_fishid:T449202_1]                      0.2    5.2  -5.7   0.1   6.1
b[(Intercept) fi_fishid:T449203_1]                         6.5    1.7   4.4   6.4   8.6
b[fi_speciespikeperch fi_fishid:T449203_1]                -0.2    5.4  -6.6   0.2   5.5
b[fi_specieswels fi_fishid:T449203_1]                     -0.6    5.3  -7.2  -0.2   5.5
b[(Intercept) fi_fishid:T449204_1]                        -5.3    1.7  -7.5  -5.3  -3.1
b[fi_speciespikeperch fi_fishid:T449204_1]                 0.1    5.2  -5.4  -0.2   5.7
b[fi_specieswels fi_fishid:T449204_1]                      0.4    5.4  -5.7   0.0   6.7
b[(Intercept) fi_fishid:T449207_1]                        -7.0    1.8  -9.3  -7.0  -4.8
b[fi_speciespikeperch fi_fishid:T449207_1]                 0.2    5.5  -5.9  -0.1   6.5
b[fi_specieswels fi_fishid:T449207_1]                      0.8    5.6  -5.4   0.4   7.7
b[(Intercept) fi_fishid:T449208_1]                        -9.5    5.5 -15.5 -10.1  -2.3
b[fi_speciespikeperch fi_fishid:T449208_1]                -4.5    5.5 -11.5  -3.8   1.3
b[fi_specieswels fi_fishid:T449208_1]                      0.4    6.3  -7.2   0.3   8.3
b[(Intercept) fi_fishid:T449209_1]                         0.1    1.5  -1.8   0.1   2.0
b[fi_speciespikeperch fi_fishid:T449209_1]                -0.1    4.6  -5.3   0.0   5.3
b[fi_specieswels fi_fishid:T449209_1]                     -0.1    4.7  -5.6   0.0   5.4
b[(Intercept) fi_fishid:T449213_1]                         4.5    4.3  -1.1   4.9   9.3
b[fi_speciespikeperch fi_fishid:T449213_1]                 2.1    4.2  -2.4   1.5   7.4
b[fi_specieswels fi_fishid:T449213_1]                     -0.2    5.3  -6.5   0.0   5.8
b[(Intercept) fi_fishid:T449215_1]                         4.3    4.4  -1.2   4.6   9.2
b[fi_speciespikeperch fi_fishid:T449215_1]                 2.2    4.3  -2.2   1.6   7.4
b[fi_specieswels fi_fishid:T449215_1]                      0.0    5.3  -6.1   0.0   6.0
b[(Intercept) fi_fishid:T449268_1]                         5.6    4.7  -0.2   5.9  11.0
b[fi_speciespikeperch fi_fishid:T449268_1]                 0.0    5.5  -6.2   0.1   5.9
b[fi_specieswels fi_fishid:T449268_1]                      3.3    4.7  -2.0   2.8   9.2
b[(Intercept) fi_fishid:T449269_1]                         3.7    4.6  -2.0   3.9   9.0
b[fi_speciespikeperch fi_fishid:T449269_1]                 0.1    5.0  -5.6   0.1   5.7
b[fi_specieswels fi_fishid:T449269_1]                      2.0    4.4  -2.8   1.7   7.5
b[(Intercept) fi_fishid:T449270_2]                         6.3    1.6   4.3   6.3   8.4
b[fi_speciespikeperch fi_fishid:T449270_2]                -0.2    5.3  -6.2   0.2   5.3
b[fi_specieswels fi_fishid:T449270_2]                     -0.4    5.4  -7.0   0.0   5.5
b[(Intercept) fi_fishid:T449271_1]                        -0.7    1.7  -2.8  -0.7   1.4
b[fi_speciespikeperch fi_fishid:T449271_1]                -0.1    4.7  -5.1   0.0   4.9
b[fi_specieswels fi_fishid:T449271_1]                      0.0    4.8  -5.4   0.0   5.7
b[(Intercept) fi_fishid:T449272_1]                        10.8    1.5   8.8  10.7  12.8
b[fi_speciespikeperch fi_fishid:T449272_1]                -0.3    6.7  -8.1   0.1   6.8
b[fi_specieswels fi_fishid:T449272_1]                     -1.0    6.4  -9.0  -0.4   6.1
b[(Intercept) fi_fishid:T449274_1]                         7.3    6.1  -0.2   7.5  14.5
b[fi_speciespikeperch fi_fishid:T449274_1]                -0.1    6.3  -7.2   0.1   6.4
b[fi_specieswels fi_fishid:T449274_1]                      4.3    5.9  -2.0   3.7  11.7
b[(Intercept) fi_fishid:T449275_1]                         4.8    4.6  -0.9   5.0  10.2
b[fi_speciespikeperch fi_fishid:T449275_1]                 0.1    5.2  -6.0   0.1   5.9
b[fi_specieswels fi_fishid:T449275_1]                      2.4    4.5  -2.6   2.0   8.1
b[(Intercept) fi_fishid:T449276_1]                         1.9    4.4  -3.4   1.9   6.9
b[fi_speciespikeperch fi_fishid:T449276_1]                 0.1    4.8  -5.2   0.1   5.2
b[fi_specieswels fi_fishid:T449276_1]                      0.7    4.1  -4.0   0.7   5.6
b[(Intercept) fi_fishid:T449277_1]                         0.8    1.5  -1.2   0.8   2.7
b[fi_speciespikeperch fi_fishid:T449277_1]                -0.1    4.8  -5.2   0.0   4.9
b[fi_specieswels fi_fishid:T449277_1]                     -0.1    5.2  -6.0   0.0   5.4
b[(Intercept) fi_fishid:T449278_1]                        -5.5    4.9 -11.0  -5.8   0.5
b[fi_speciespikeperch fi_fishid:T449278_1]                 0.1    5.8  -6.2  -0.1   6.8
b[fi_specieswels fi_fishid:T449278_1]                     -3.0    4.8  -9.1  -2.5   2.2
b[(Intercept) fi_fishid:T449280_1]                        -0.6    4.2  -5.4  -0.5   4.3
b[fi_speciespikeperch fi_fishid:T449280_1]                 0.1    4.5  -4.8   0.1   4.8
b[fi_specieswels fi_fishid:T449280_1]                     -0.2    4.0  -5.0  -0.2   4.4
b[(Intercept) fi_fishid:T449282_1]                        -8.3    5.8 -14.9  -8.7  -1.0
b[fi_speciespikeperch fi_fishid:T449282_1]                 0.0    6.7  -7.0  -0.2   7.7
b[fi_specieswels fi_fishid:T449282_1]                     -4.6    5.6 -11.9  -3.9   1.4
b[(Intercept) fi_fishid:T449284_1]                        -3.1    4.5  -8.5  -3.3   2.5
b[fi_speciespikeperch fi_fishid:T449284_1]                -0.1    5.0  -5.4  -0.1   5.5
b[fi_specieswels fi_fishid:T449284_1]                     -1.7    4.4  -7.3  -1.3   3.3
b[(Intercept) fi_fishid:T449285_1]                        -8.2    1.7 -10.3  -8.2  -6.0
b[fi_speciespikeperch fi_fishid:T449285_1]                 0.2    5.6  -5.7  -0.2   6.8
b[fi_specieswels fi_fishid:T449285_1]                      0.8    5.8  -5.6   0.3   7.9
b[(Intercept) fi_fishid:T449286_1]                        -0.2    4.2  -5.1  -0.2   4.9
b[fi_speciespikeperch fi_fishid:T449286_1]                 0.1    4.4  -4.7   0.0   4.9
b[fi_specieswels fi_fishid:T449286_1]                     -0.1    4.1  -5.0  -0.1   4.7
b[(Intercept) fi_fishid:T449287_1]                        -7.7    1.5  -9.6  -7.7  -5.7
b[fi_speciespikeperch fi_fishid:T449287_1]                 0.2    5.8  -6.2  -0.1   6.9
b[fi_specieswels fi_fishid:T449287_1]                      0.7    5.9  -5.7   0.3   8.2
b[(Intercept) fi_fishid:T449288_1]                         2.9    4.6  -2.6   3.3   8.2
b[fi_speciespikeperch fi_fishid:T449288_1]                 0.1    5.2  -5.8   0.1   5.9
b[fi_specieswels fi_fishid:T449288_1]                      1.8    4.5  -3.2   1.3   7.3
b[(Intercept) fi_fishid:T449310_1]                        -1.1    3.9  -5.5  -1.2   3.6
b[fi_speciespikeperch fi_fishid:T449310_1]                -0.5    3.7  -4.9  -0.4   3.5
b[fi_specieswels fi_fishid:T449310_1]                      0.1    4.9  -5.6   0.0   5.7
b[(Intercept) fi_fishid:T449313_1]                        -2.3    4.0  -6.9  -2.5   2.4
b[fi_speciespikeperch fi_fishid:T449313_1]                -1.1    3.8  -5.7  -0.7   3.0
b[fi_specieswels fi_fishid:T449313_1]                      0.1    5.1  -5.7   0.1   6.0
b[(Intercept) fi_fishid:T449314_1]                        -0.4    4.1  -5.2  -0.4   4.5
b[fi_speciespikeperch fi_fishid:T449314_1]                 0.1    4.5  -4.7   0.0   5.2
b[fi_specieswels fi_fishid:T449314_1]                     -0.1    3.9  -4.7   0.0   4.5
b[(Intercept) fi_fishid:T449317_1]                        -4.1    4.3  -9.0  -4.3   1.1
b[fi_speciespikeperch fi_fishid:T449317_1]                -2.0    4.2  -7.3  -1.5   2.3
b[fi_specieswels fi_fishid:T449317_1]                      0.3    5.3  -5.6   0.1   6.5
b[(Intercept) fi_fishid:T449318_1]                         1.6    4.2  -3.7   1.7   6.5
b[fi_speciespikeperch fi_fishid:T449318_1]                 0.1    4.8  -5.3   0.1   5.2
b[fi_specieswels fi_fishid:T449318_1]                      1.0    4.1  -3.8   0.8   6.1
b[(Intercept) fi_fishid:T449319_1]                         3.4    4.4  -2.0   3.6   8.5
b[fi_speciespikeperch fi_fishid:T449319_1]                -0.1    5.1  -6.0   0.1   5.4
b[fi_specieswels fi_fishid:T449319_1]                      1.9    4.3  -2.9   1.6   7.3
sigma                                                     13.9    0.1  13.8  13.9  14.0
Sigma[fi_fishid:(Intercept),(Intercept)]                  43.8   17.1  25.8  40.4  66.1
Sigma[fi_fishid:fi_speciespikeperch,(Intercept)]          -2.2   16.9 -21.9   0.0  14.4
Sigma[fi_fishid:fi_specieswels,(Intercept)]               -5.4   16.5 -25.4  -2.5  10.8
Sigma[fi_fishid:fi_speciespikeperch,fi_speciespikeperch]  28.3   34.1   2.2  16.4  70.0
Sigma[fi_fishid:fi_specieswels,fi_speciespikeperch]        1.8   22.4 -17.0   0.1  21.4
Sigma[fi_fishid:fi_specieswels,fi_specieswels]            30.0   29.5   4.9  20.7  67.1

Fit Diagnostics:
           mean   sd   10%   50%   90%
mean_PPD 26.9    0.2 26.7  26.9  27.2

The mean_ppd is the sample average posterior predictive distribution of the outcome variable (for details see help('summary.stanreg')).

MCMC diagnostics
                                                         mcse Rhat n_eff
(Intercept)                                              0.0  1.0  1019
r3_condition                                             0.0  1.0  2515
seasonspring_I                                           0.0  1.0  4460
seasonspring_II                                          0.0  1.0  7233
seasonsummer                                             0.0  1.0  4740
seasonwinter                                             0.0  1.0  5522
b[(Intercept) fi_fishid:T449202_1]                       0.1  1.0  2362
b[fi_speciespikeperch fi_fishid:T449202_1]               0.1  1.0  2735
b[fi_specieswels fi_fishid:T449202_1]                    0.1  1.0  3390
b[(Intercept) fi_fishid:T449203_1]                       0.0  1.0  1399
b[fi_speciespikeperch fi_fishid:T449203_1]               0.1  1.0  3483
b[fi_specieswels fi_fishid:T449203_1]                    0.1  1.0  2595
b[(Intercept) fi_fishid:T449204_1]                       0.0  1.0  1591
b[fi_speciespikeperch fi_fishid:T449204_1]               0.1  1.0  3919
b[fi_specieswels fi_fishid:T449204_1]                    0.1  1.0  2552
b[(Intercept) fi_fishid:T449207_1]                       0.0  1.0  1451
b[fi_speciespikeperch fi_fishid:T449207_1]               0.1  1.0  2570
b[fi_specieswels fi_fishid:T449207_1]                    0.1  1.0  2161
b[(Intercept) fi_fishid:T449208_1]                       0.2  1.0   834
b[fi_speciespikeperch fi_fishid:T449208_1]               0.2  1.0   884
b[fi_specieswels fi_fishid:T449208_1]                    0.2  1.0  1319
b[(Intercept) fi_fishid:T449209_1]                       0.0  1.0  1044
b[fi_speciespikeperch fi_fishid:T449209_1]               0.1  1.0  5073
b[fi_specieswels fi_fishid:T449209_1]                    0.1  1.0  5526
b[(Intercept) fi_fishid:T449213_1]                       0.1  1.0  1625
b[fi_speciespikeperch fi_fishid:T449213_1]               0.1  1.0  1587
b[fi_specieswels fi_fishid:T449213_1]                    0.1  1.0  2547
b[(Intercept) fi_fishid:T449215_1]                       0.1  1.0  1749
b[fi_speciespikeperch fi_fishid:T449215_1]               0.1  1.0  1749
b[fi_specieswels fi_fishid:T449215_1]                    0.1  1.0  2745
b[(Intercept) fi_fishid:T449268_1]                       0.1  1.0  1545
b[fi_speciespikeperch fi_fishid:T449268_1]               0.1  1.0  1823
b[fi_specieswels fi_fishid:T449268_1]                    0.1  1.0  1558
b[(Intercept) fi_fishid:T449269_1]                       0.1  1.0  2248
b[fi_speciespikeperch fi_fishid:T449269_1]               0.1  1.0  2197
b[fi_specieswels fi_fishid:T449269_1]                    0.1  1.0  2074
b[(Intercept) fi_fishid:T449270_2]                       0.0  1.0  1343
b[fi_speciespikeperch fi_fishid:T449270_2]               0.1  1.0  3534
b[fi_specieswels fi_fishid:T449270_2]                    0.1  1.0  2362
b[(Intercept) fi_fishid:T449271_1]                       0.0  1.0  1297
b[fi_speciespikeperch fi_fishid:T449271_1]               0.1  1.0  5831
b[fi_specieswels fi_fishid:T449271_1]                    0.1  1.0  5180
b[(Intercept) fi_fishid:T449272_1]                       0.0  1.0  1348
b[fi_speciespikeperch fi_fishid:T449272_1]               0.1  1.0  2059
b[fi_specieswels fi_fishid:T449272_1]                    0.2  1.0  1603
b[(Intercept) fi_fishid:T449274_1]                       0.2  1.0  1618
b[fi_speciespikeperch fi_fishid:T449274_1]               0.2  1.0  1539
b[fi_specieswels fi_fishid:T449274_1]                    0.1  1.0  1566
b[(Intercept) fi_fishid:T449275_1]                       0.1  1.0  1782
b[fi_speciespikeperch fi_fishid:T449275_1]               0.1  1.0  2147
b[fi_specieswels fi_fishid:T449275_1]                    0.1  1.0  1442
b[(Intercept) fi_fishid:T449276_1]                       0.1  1.0  3054
b[fi_speciespikeperch fi_fishid:T449276_1]               0.1  1.0  3914
b[fi_specieswels fi_fishid:T449276_1]                    0.1  1.0  3318
b[(Intercept) fi_fishid:T449277_1]                       0.0  1.0  1240
b[fi_speciespikeperch fi_fishid:T449277_1]               0.1  1.0  5698
b[fi_specieswels fi_fishid:T449277_1]                    0.1  1.0  4881
b[(Intercept) fi_fishid:T449278_1]                       0.1  1.0  1573
b[fi_speciespikeperch fi_fishid:T449278_1]               0.1  1.0  1753
b[fi_specieswels fi_fishid:T449278_1]                    0.1  1.0  1575
b[(Intercept) fi_fishid:T449280_1]                       0.1  1.0  3027
b[fi_speciespikeperch fi_fishid:T449280_1]               0.1  1.0  4760
b[fi_specieswels fi_fishid:T449280_1]                    0.1  1.0  3573
b[(Intercept) fi_fishid:T449282_1]                       0.2  1.0  1096
b[fi_speciespikeperch fi_fishid:T449282_1]               0.2  1.0  1100
b[fi_specieswels fi_fishid:T449282_1]                    0.2  1.0  1104
b[(Intercept) fi_fishid:T449284_1]                       0.1  1.0  2131
b[fi_speciespikeperch fi_fishid:T449284_1]               0.1  1.0  2253
b[fi_specieswels fi_fishid:T449284_1]                    0.1  1.0  2788
b[(Intercept) fi_fishid:T449285_1]                       0.0  1.0  1464
b[fi_speciespikeperch fi_fishid:T449285_1]               0.1  1.0  3089
b[fi_specieswels fi_fishid:T449285_1]                    0.1  1.0  1812
b[(Intercept) fi_fishid:T449286_1]                       0.1  1.0  2669
b[fi_speciespikeperch fi_fishid:T449286_1]               0.1  1.0  4033
b[fi_specieswels fi_fishid:T449286_1]                    0.1  1.0  3079
b[(Intercept) fi_fishid:T449287_1]                       0.0  1.0  1182
b[fi_speciespikeperch fi_fishid:T449287_1]               0.1  1.0  2132
b[fi_specieswels fi_fishid:T449287_1]                    0.1  1.0  1948
b[(Intercept) fi_fishid:T449288_1]                       0.1  1.0  2430
b[fi_speciespikeperch fi_fishid:T449288_1]               0.1  1.0  2441
b[fi_specieswels fi_fishid:T449288_1]                    0.1  1.0  2555
b[(Intercept) fi_fishid:T449310_1]                       0.1  1.0  2998
b[fi_speciespikeperch fi_fishid:T449310_1]               0.1  1.0  3340
b[fi_specieswels fi_fishid:T449310_1]                    0.1  1.0  4294
b[(Intercept) fi_fishid:T449313_1]                       0.1  1.0  2687
b[fi_speciespikeperch fi_fishid:T449313_1]               0.1  1.0  2456
b[fi_specieswels fi_fishid:T449313_1]                    0.1  1.0  3886
b[(Intercept) fi_fishid:T449314_1]                       0.1  1.0  3141
b[fi_speciespikeperch fi_fishid:T449314_1]               0.1  1.0  3922
b[fi_specieswels fi_fishid:T449314_1]                    0.1  1.0  3804
b[(Intercept) fi_fishid:T449317_1]                       0.1  1.0  2068
b[fi_speciespikeperch fi_fishid:T449317_1]               0.1  1.0  1761
b[fi_specieswels fi_fishid:T449317_1]                    0.1  1.0  2753
b[(Intercept) fi_fishid:T449318_1]                       0.1  1.0  3233
b[fi_speciespikeperch fi_fishid:T449318_1]               0.1  1.0  3385
b[fi_specieswels fi_fishid:T449318_1]                    0.1  1.0  3506
b[(Intercept) fi_fishid:T449319_1]                       0.1  1.0  2442
b[fi_speciespikeperch fi_fishid:T449319_1]               0.1  1.0  2918
b[fi_specieswels fi_fishid:T449319_1]                    0.1  1.0  2395
sigma                                                    0.0  1.0  7881
Sigma[fi_fishid:(Intercept),(Intercept)]                 0.4  1.0  1479
Sigma[fi_fishid:fi_speciespikeperch,(Intercept)]         0.5  1.0  1137
Sigma[fi_fishid:fi_specieswels,(Intercept)]              0.6  1.0   750
Sigma[fi_fishid:fi_speciespikeperch,fi_speciespikeperch] 1.4  1.0   579
Sigma[fi_fishid:fi_specieswels,fi_speciespikeperch]      0.9  1.0   684
Sigma[fi_fishid:fi_specieswels,fi_specieswels]           1.2  1.0   578
mean_PPD                                                 0.0  1.0  3764
log-posterior                                            0.2  1.0  1296

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1)
```

### Check priors used

`prior_summary(object = m7)`

```{r, echo=FALSE, eval=TRUE}

Priors for model 'm7'
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
    ~ normal(location = [0,0,0,...], scale = [38.64,38.65,38.65,...])

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

`summary(m7, pars = c("(Intercept)","b[(Intercept) fi_fishid:]", "sigma", "Sigma[fi_fishid:(Intercept),(Intercept)]"), probs = c(0.025, 0.975), digits = 2)`

```
Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + r3_condition + season + (1 + fi_species |
	   fi_fishid)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
                                           mean   sd    2.5%   97.5%
(Intercept)                              25.03   1.33 22.43  27.69
sigma                                    13.91   0.10 13.70  14.11
Sigma[fi_fishid:(Intercept),(Intercept)] 43.80  17.07 20.48  85.82

MCMC diagnostics
                                         mcse Rhat n_eff
(Intercept)                              0.04 1.01 1019
sigma                                    0.00 1.00 7881
Sigma[fi_fishid:(Intercept),(Intercept)] 0.44 1.00 1479

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).
```

### Extract the posterior draws for all parameters

`sims <- as.matrix(m7)`

`dim(sims)`

```
[1] 4000   106

```

`para_name <- colnames(sims)`

`para_name`

```
[1] "(Intercept)"                                              "r3_condition"
  [3] "seasonspring_I"                                           "seasonspring_II"
  [5] "seasonsummer"                                             "seasonwinter"
  [7] "b[(Intercept) fi_fishid:T449202_1]"                       "b[fi_speciespikeperch fi_fishid:T449202_1]"
  [9] "b[fi_specieswels fi_fishid:T449202_1]"                    "b[(Intercept) fi_fishid:T449203_1]"
 [11] "b[fi_speciespikeperch fi_fishid:T449203_1]"               "b[fi_specieswels fi_fishid:T449203_1]"
 [13] "b[(Intercept) fi_fishid:T449204_1]"                       "b[fi_speciespikeperch fi_fishid:T449204_1]"
 [15] "b[fi_specieswels fi_fishid:T449204_1]"                    "b[(Intercept) fi_fishid:T449207_1]"
 [17] "b[fi_speciespikeperch fi_fishid:T449207_1]"               "b[fi_specieswels fi_fishid:T449207_1]"
 [19] "b[(Intercept) fi_fishid:T449208_1]"                       "b[fi_speciespikeperch fi_fishid:T449208_1]"
 [21] "b[fi_specieswels fi_fishid:T449208_1]"                    "b[(Intercept) fi_fishid:T449209_1]"
 [23] "b[fi_speciespikeperch fi_fishid:T449209_1]"               "b[fi_specieswels fi_fishid:T449209_1]"
 [25] "b[(Intercept) fi_fishid:T449213_1]"                       "b[fi_speciespikeperch fi_fishid:T449213_1]"
 [27] "b[fi_specieswels fi_fishid:T449213_1]"                    "b[(Intercept) fi_fishid:T449215_1]"
 [29] "b[fi_speciespikeperch fi_fishid:T449215_1]"               "b[fi_specieswels fi_fishid:T449215_1]"
 [31] "b[(Intercept) fi_fishid:T449268_1]"                       "b[fi_speciespikeperch fi_fishid:T449268_1]"
 [33] "b[fi_specieswels fi_fishid:T449268_1]"                    "b[(Intercept) fi_fishid:T449269_1]"
 [35] "b[fi_speciespikeperch fi_fishid:T449269_1]"               "b[fi_specieswels fi_fishid:T449269_1]"
 [37] "b[(Intercept) fi_fishid:T449270_2]"                       "b[fi_speciespikeperch fi_fishid:T449270_2]"
 [39] "b[fi_specieswels fi_fishid:T449270_2]"                    "b[(Intercept) fi_fishid:T449271_1]"
 [41] "b[fi_speciespikeperch fi_fishid:T449271_1]"               "b[fi_specieswels fi_fishid:T449271_1]"
 [43] "b[(Intercept) fi_fishid:T449272_1]"                       "b[fi_speciespikeperch fi_fishid:T449272_1]"
 [45] "b[fi_specieswels fi_fishid:T449272_1]"                    "b[(Intercept) fi_fishid:T449274_1]"
 [47] "b[fi_speciespikeperch fi_fishid:T449274_1]"               "b[fi_specieswels fi_fishid:T449274_1]"
 [49] "b[(Intercept) fi_fishid:T449275_1]"                       "b[fi_speciespikeperch fi_fishid:T449275_1]"
 [51] "b[fi_specieswels fi_fishid:T449275_1]"                    "b[(Intercept) fi_fishid:T449276_1]"
 [53] "b[fi_speciespikeperch fi_fishid:T449276_1]"               "b[fi_specieswels fi_fishid:T449276_1]"
 [55] "b[(Intercept) fi_fishid:T449277_1]"                       "b[fi_speciespikeperch fi_fishid:T449277_1]"
 [57] "b[fi_specieswels fi_fishid:T449277_1]"                    "b[(Intercept) fi_fishid:T449278_1]"
 [59] "b[fi_speciespikeperch fi_fishid:T449278_1]"               "b[fi_specieswels fi_fishid:T449278_1]"
 [61] "b[(Intercept) fi_fishid:T449280_1]"                       "b[fi_speciespikeperch fi_fishid:T449280_1]"
 [63] "b[fi_specieswels fi_fishid:T449280_1]"                    "b[(Intercept) fi_fishid:T449282_1]"
 [65] "b[fi_speciespikeperch fi_fishid:T449282_1]"               "b[fi_specieswels fi_fishid:T449282_1]"
 [67] "b[(Intercept) fi_fishid:T449284_1]"                       "b[fi_speciespikeperch fi_fishid:T449284_1]"
 [69] "b[fi_specieswels fi_fishid:T449284_1]"                    "b[(Intercept) fi_fishid:T449285_1]"
 [71] "b[fi_speciespikeperch fi_fishid:T449285_1]"               "b[fi_specieswels fi_fishid:T449285_1]"
 [73] "b[(Intercept) fi_fishid:T449286_1]"                       "b[fi_speciespikeperch fi_fishid:T449286_1]"
 [75] "b[fi_specieswels fi_fishid:T449286_1]"                    "b[(Intercept) fi_fishid:T449287_1]"
 [77] "b[fi_speciespikeperch fi_fishid:T449287_1]"               "b[fi_specieswels fi_fishid:T449287_1]"
 [79] "b[(Intercept) fi_fishid:T449288_1]"                       "b[fi_speciespikeperch fi_fishid:T449288_1]"
 [81] "b[fi_specieswels fi_fishid:T449288_1]"                    "b[(Intercept) fi_fishid:T449310_1]"
 [83] "b[fi_speciespikeperch fi_fishid:T449310_1]"               "b[fi_specieswels fi_fishid:T449310_1]"
 [85] "b[(Intercept) fi_fishid:T449313_1]"                       "b[fi_speciespikeperch fi_fishid:T449313_1]"
 [87] "b[fi_specieswels fi_fishid:T449313_1]"                    "b[(Intercept) fi_fishid:T449314_1]"
 [89] "b[fi_speciespikeperch fi_fishid:T449314_1]"               "b[fi_specieswels fi_fishid:T449314_1]"
 [91] "b[(Intercept) fi_fishid:T449317_1]"                       "b[fi_speciespikeperch fi_fishid:T449317_1]"
 [93] "b[fi_specieswels fi_fishid:T449317_1]"                    "b[(Intercept) fi_fishid:T449318_1]"
 [95] "b[fi_speciespikeperch fi_fishid:T449318_1]"               "b[fi_specieswels fi_fishid:T449318_1]"
 [97] "b[(Intercept) fi_fishid:T449319_1]"                       "b[fi_speciespikeperch fi_fishid:T449319_1]"
 [99] "b[fi_specieswels fi_fishid:T449319_1]"                    "sigma"
[101] "Sigma[fi_fishid:(Intercept),(Intercept)]"                 "Sigma[fi_fishid:fi_speciespikeperch,(Intercept)]"
[103] "Sigma[fi_fishid:fi_specieswels,(Intercept)]"              "Sigma[fi_fishid:fi_speciespikeperch,fi_speciespikeperch]"
[105] "Sigma[fi_fishid:fi_specieswels,fi_speciespikeperch]"      "Sigma[fi_fishid:fi_specieswels,fi_specieswels]"
```

- Obtain fi_species-level varying intercept a_j

 - draws for overall mean

`mu_a_sims <- as.matrix(m7, pars = "(Intercept)")`

 - draws for 3 fi_species-level error

`fish_err <- as.matrix(m7,regex_pars = "b\\[\\(Intercept\\) fi_fishid\\:")`

 - draws for 3 species' varying intercepts

`fish_inter <- as.numeric(mu_a_sims) + fish_err`

- Obtain sigma_y and sigma_alpha^2

 -draws for sigma_y

`signma_y <- as.matrix(m7, pars = "sigma")`

 - draws for sigma_alpha^2

`sigma_alpha <- as.matrix(m7, pars = "Sigma[fi_fishid:(Intercept),(Intercept)]")`


### Obtaining means, SD, medians and 95% credible intervals of varying intercepts

In *fish_inter*, we have saved 4,000 posterior draws (from all 4 chains) for the varying intercepts αj of the 30 fishes. For example, the first column of the 4,000 by 30 matrix is a vector of 4,000 posterior simulation draws for the first fish's (*fi_species*:T449202_1) varying intercept α1. One quantitative way to summarize the posterior probability distribution of these 4,000 estimates for α1 is to examine their quantiles.

- Posterior mean of each alpha

`a_mean <- apply(X = fish_inter, MARGIN = 2, FUN = mean)`

`a_mean'

```
b[(Intercept) fi_fishid:T449202_1] b[(Intercept) fi_fishid:T449203_1] b[(Intercept) fi_fishid:T449204_1] b[(Intercept) fi_fishid:T449207_1]
                          21.95700                           31.48724                           19.73718                           17.99401
b[(Intercept) fi_fishid:T449208_1] b[(Intercept) fi_fishid:T449209_1] b[(Intercept) fi_fishid:T449213_1] b[(Intercept) fi_fishid:T449215_1]
                          15.55361                           25.12275                           29.52254                           29.29625
b[(Intercept) fi_fishid:T449268_1] b[(Intercept) fi_fishid:T449269_1] b[(Intercept) fi_fishid:T449270_2] b[(Intercept) fi_fishid:T449271_1]
                          30.58495                           28.70831                           31.33827                           24.31860
b[(Intercept) fi_fishid:T449272_1] b[(Intercept) fi_fishid:T449274_1] b[(Intercept) fi_fishid:T449275_1] b[(Intercept) fi_fishid:T449276_1]
                          35.77633                           32.34754                           29.77935                           26.88497
b[(Intercept) fi_fishid:T449277_1] b[(Intercept) fi_fishid:T449278_1] b[(Intercept) fi_fishid:T449280_1] b[(Intercept) fi_fishid:T449282_1]
                          25.77907                           19.53713                           24.45548                           16.71129
b[(Intercept) fi_fishid:T449284_1] b[(Intercept) fi_fishid:T449285_1] b[(Intercept) fi_fishid:T449286_1] b[(Intercept) fi_fishid:T449287_1]
                          21.91539                           16.86023                           24.86218                           17.31536
b[(Intercept) fi_fishid:T449288_1] b[(Intercept) fi_fishid:T449310_1] b[(Intercept) fi_fishid:T449313_1] b[(Intercept) fi_fishid:T449314_1]
                          27.97337                           23.97253                           22.67943                           24.64581
b[(Intercept) fi_fishid:T449317_1] b[(Intercept) fi_fishid:T449318_1] b[(Intercept) fi_fishid:T449319_1]
                          20.94213                           26.63956                           28.45852
```

- Posterior SD of each alpha

`a_sd <- apply(X = fish_inter, MARGIN = 2, FUN = sd)`

`a_sd`

```

b[(Intercept) fi_fishid:T449202_1] b[(Intercept) fi_fishid:T449203_1] b[(Intercept) fi_fishid:T449204_1] b[(Intercept) fi_fishid:T449207_1]
                         4.0503599                          1.1634064                          1.2923251                          1.1888972
b[(Intercept) fi_fishid:T449208_1] b[(Intercept) fi_fishid:T449209_1] b[(Intercept) fi_fishid:T449213_1] b[(Intercept) fi_fishid:T449215_1]
                         5.4752529                          0.8026270                          4.2640924                          4.3294357
b[(Intercept) fi_fishid:T449268_1] b[(Intercept) fi_fishid:T449269_1] b[(Intercept) fi_fishid:T449270_2] b[(Intercept) fi_fishid:T449271_1]
                         4.6824850                          4.5480285                          1.0268729                          1.0936767
b[(Intercept) fi_fishid:T449272_1] b[(Intercept) fi_fishid:T449274_1] b[(Intercept) fi_fishid:T449275_1] b[(Intercept) fi_fishid:T449276_1]
                         0.8611784                          6.0360773                          4.6105516                          4.3237239
b[(Intercept) fi_fishid:T449277_1] b[(Intercept) fi_fishid:T449278_1] b[(Intercept) fi_fishid:T449280_1] b[(Intercept) fi_fishid:T449282_1]
                         0.8190759                          4.8824842                          4.0694682                          5.7041190
b[(Intercept) fi_fishid:T449284_1] b[(Intercept) fi_fishid:T449285_1] b[(Intercept) fi_fishid:T449286_1] b[(Intercept) fi_fishid:T449287_1]
                         4.3942324                          1.1197563                          4.1927338                          0.8799184
b[(Intercept) fi_fishid:T449288_1] b[(Intercept) fi_fishid:T449310_1] b[(Intercept) fi_fishid:T449313_1] b[(Intercept) fi_fishid:T449314_1]
                         4.6102834                          3.8419194                          3.8900220                          4.0955722
b[(Intercept) fi_fishid:T449317_1] b[(Intercept) fi_fishid:T449318_1] b[(Intercept) fi_fishid:T449319_1]
                         4.2741867                          4.2281053                          4.3839122
```

- Posterior median and 95% credible interval

`a_quant <- apply(X = fish_inter, MARGIN = 2, FUN = quantile, probs = c(0.025, 0.50, 0.975))`

`a_quant <- data.frame(t(a_quant))`

`a_quant`

```
                                       X2.5.     X50.   X97.5.
b[(Intercept) fi_fishid:T449202_1] 14.184501 21.71751 30.93627
b[(Intercept) fi_fishid:T449203_1] 29.158400 31.51147 33.66297
b[(Intercept) fi_fishid:T449204_1] 17.200029 19.74348 22.23308
b[(Intercept) fi_fishid:T449207_1] 15.652671 18.00094 20.35453
b[(Intercept) fi_fishid:T449208_1]  6.669127 14.87272 28.68106
b[(Intercept) fi_fishid:T449209_1] 23.559104 25.12277 26.66797
b[(Intercept) fi_fishid:T449213_1] 19.300859 29.94826 37.26813
b[(Intercept) fi_fishid:T449215_1] 19.265796 29.69448 37.16858
b[(Intercept) fi_fishid:T449268_1] 20.033538 30.97921 39.41564
b[(Intercept) fi_fishid:T449269_1] 18.627083 28.90509 37.27754
b[(Intercept) fi_fishid:T449270_2] 29.352210 31.33236 33.35157
b[(Intercept) fi_fishid:T449271_1] 22.090606 24.33079 26.43465
b[(Intercept) fi_fishid:T449272_1] 34.101251 35.78679 37.44235
b[(Intercept) fi_fishid:T449274_1] 19.527892 32.57793 43.94675
b[(Intercept) fi_fishid:T449275_1] 19.548235 30.04037 38.48061
b[(Intercept) fi_fishid:T449276_1] 17.985294 26.89380 35.73952
b[(Intercept) fi_fishid:T449277_1] 24.189835 25.78930 27.33821
b[(Intercept) fi_fishid:T449278_1] 10.668993 19.00646 31.07496
b[(Intercept) fi_fishid:T449280_1] 16.402295 24.42791 32.93987
b[(Intercept) fi_fishid:T449282_1]  7.012280 16.12271 29.92247
b[(Intercept) fi_fishid:T449284_1] 13.327292 21.66256 31.43702
b[(Intercept) fi_fishid:T449285_1] 14.720926 16.85903 19.08166
b[(Intercept) fi_fishid:T449286_1] 16.399089 24.84523 33.95567
b[(Intercept) fi_fishid:T449287_1] 15.620698 17.32604 19.08638
b[(Intercept) fi_fishid:T449288_1] 17.424269 28.29769 36.56417
b[(Intercept) fi_fishid:T449310_1] 16.086454 23.83706 32.32856
b[(Intercept) fi_fishid:T449313_1] 15.107776 22.39658 31.72164
b[(Intercept) fi_fishid:T449314_1] 16.098796 24.58720 33.38375
b[(Intercept) fi_fishid:T449317_1] 13.183896 20.64539 30.61765
b[(Intercept) fi_fishid:T449318_1] 17.785543 26.76289 35.05855
b[(Intercept) fi_fishid:T449319_1] 18.995709 28.59335 36.86479
```

`names(a_quant) <- c("X2.5.", "X50.", "X97.5.")`

- Combine summary statistics of posterior simulation draws

`a_df <- data.frame(a_mean, a_sd, a_quant)`

`round(head(a_df), 2)`

```
                                   a_mean a_sd X2.5.  X50. X97.5.
b[(Intercept) fi_fishid:T449202_1]  21.96 4.05 14.18 21.72  30.94
b[(Intercept) fi_fishid:T449203_1]  31.49 1.16 29.16 31.51  33.66
b[(Intercept) fi_fishid:T449204_1]  19.74 1.29 17.20 19.74  22.23
b[(Intercept) fi_fishid:T449207_1]  17.99 1.19 15.65 18.00  20.35
b[(Intercept) fi_fishid:T449208_1]  15.55 5.48  6.67 14.87  28.68
b[(Intercept) fi_fishid:T449209_1]  25.12 0.80 23.56 25.12  26.67

```

### Plots

Caterpillar plot to show the fully Bayes estimates for the *fi_species* varying intercepts in rank order together with their 95% credible interval

- Sort dataframe containing an estimated alpha's mean and sd for every fi_species

`a_df <- a_df[order(a_df$a_mean), ]`

`a_df`
```
                                     a_mean      a_sd     X2.5.     X50.   X97.5.
b[(Intercept) fi_fishid:T449208_1] 15.55361 5.4752529  6.669127 14.87272 28.68106
b[(Intercept) fi_fishid:T449282_1] 16.71129 5.7041190  7.012280 16.12271 29.92247
b[(Intercept) fi_fishid:T449285_1] 16.86023 1.1197563 14.720926 16.85903 19.08166
b[(Intercept) fi_fishid:T449287_1] 17.31536 0.8799184 15.620698 17.32604 19.08638
b[(Intercept) fi_fishid:T449207_1] 17.99401 1.1888972 15.652671 18.00094 20.35453
b[(Intercept) fi_fishid:T449278_1] 19.53713 4.8824842 10.668993 19.00646 31.07496
b[(Intercept) fi_fishid:T449204_1] 19.73718 1.2923251 17.200029 19.74348 22.23308
b[(Intercept) fi_fishid:T449317_1] 20.94213 4.2741867 13.183896 20.64539 30.61765
b[(Intercept) fi_fishid:T449284_1] 21.91539 4.3942324 13.327292 21.66256 31.43702
b[(Intercept) fi_fishid:T449202_1] 21.95700 4.0503599 14.184501 21.71751 30.93627
b[(Intercept) fi_fishid:T449313_1] 22.67943 3.8900220 15.107776 22.39658 31.72164
b[(Intercept) fi_fishid:T449310_1] 23.97253 3.8419194 16.086454 23.83706 32.32856
b[(Intercept) fi_fishid:T449271_1] 24.31860 1.0936767 22.090606 24.33079 26.43465
b[(Intercept) fi_fishid:T449280_1] 24.45548 4.0694682 16.402295 24.42791 32.93987
b[(Intercept) fi_fishid:T449314_1] 24.64581 4.0955722 16.098796 24.58720 33.38375
b[(Intercept) fi_fishid:T449286_1] 24.86218 4.1927338 16.399089 24.84523 33.95567
b[(Intercept) fi_fishid:T449209_1] 25.12275 0.8026270 23.559104 25.12277 26.66797
b[(Intercept) fi_fishid:T449277_1] 25.77907 0.8190759 24.189835 25.78930 27.33821
b[(Intercept) fi_fishid:T449318_1] 26.63956 4.2281053 17.785543 26.76289 35.05855
b[(Intercept) fi_fishid:T449276_1] 26.88497 4.3237239 17.985294 26.89380 35.73952
b[(Intercept) fi_fishid:T449288_1] 27.97337 4.6102834 17.424269 28.29769 36.56417
b[(Intercept) fi_fishid:T449319_1] 28.45852 4.3839122 18.995709 28.59335 36.86479
b[(Intercept) fi_fishid:T449269_1] 28.70831 4.5480285 18.627083 28.90509 37.27754
b[(Intercept) fi_fishid:T449215_1] 29.29625 4.3294357 19.265796 29.69448 37.16858
b[(Intercept) fi_fishid:T449213_1] 29.52254 4.2640924 19.300859 29.94826 37.26813
b[(Intercept) fi_fishid:T449275_1] 29.77935 4.6105516 19.548235 30.04037 38.48061
b[(Intercept) fi_fishid:T449268_1] 30.58495 4.6824850 20.033538 30.97921 39.41564
b[(Intercept) fi_fishid:T449270_2] 31.33827 1.0268729 29.352210 31.33236 33.35157
b[(Intercept) fi_fishid:T449203_1] 31.48724 1.1634064 29.158400 31.51147 33.66297
b[(Intercept) fi_fishid:T449274_1] 32.34754 6.0360773 19.527892 32.57793 43.94675
b[(Intercept) fi_fishid:T449272_1] 35.77633 0.8611784 34.101251 35.78679 37.44235
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
![M7_s1](/Plots/M7_s1.png "M7_s1")

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
   mean   sd   Q2.5   Q50 Q97.5
1 -9.53 4.37 -18.16 -9.69 -0.04
>

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
  geom_text(aes(0.45, 20, label = "mean = -9.53"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 4.37"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M7_s2](/Plots/M7_s2.png "M7_s2")

The expected difference comes to 3.30 with a standard deviation of 0.46 and a wide range of uncertainty. The 95% credible interval is [2.48,4.29], so we are 95% certain that the true value of the difference between the two species lies within the range, given the data

We also can get the proportion of the time that pike has a higher mean than pikeperch:

`prop.table(table(fish_inter[, 1] > fish_inter[, 2]))`

```
FALSE  TRUE
0.975 0.025
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
   mean  sd Q2.5   Q50 Q97.5
1 11.75 1.7 8.44 11.78  15.1
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
  geom_text(aes(0.45, 20, label = "mean = 11.75"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 1.7"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M7_s3](/Plots/M7_s3.png "M7_s3")

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
  mean   sd  Q2.5  Q50 Q97.5
1 2.22 4.23 -5.68 2.02 11.52
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
  geom_text(aes(0.45, 20, label = "mean = 2.22"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 4.23"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M7_s4](/Plots/M7_s4.png "M7_s4")

### Cross-validation checking

We can use Pareto-smoothed importance sampling LOO CV as model checking tool (2017b)

`library(loo)`

`loo<-loo(m7)`

`loo`

```
Computed from 4000 by 8846 log-likelihood matrix

         Estimate    SE
elpd_loo -35856.4  72.5
p_loo        36.3   0.7
looic     71712.8 144.9
------
Monte Carlo SE of elpd_loo is 0.1.

All Pareto k estimates are good (k < 0.5).
See help('pareto-k-diagnostic') for details.

```
`plot(loo, diagnostic = c("k", "n_eff"), label_points = FALSE, main = "PSIS diagnostic plot")`

![M7_sloo](/Plots/M7_sloo.png "M7_sloo")


### Analyse posterior

#### Plot posterior

- Including random effects from *fi_species*

`library(bayesplot)`

`mcmc_areas(as.matrix(m7), prob_outer = .999)`

![M7_s5](/Plots/M7_s5.png "M7_s5")

- Excluding random effects from *fi_species*

`mcmc_areas(as.matrix(m7), prob_outer = .999`

![M7_s6](/Plots/M7_s6.png "M7_s6")

- *fi_species:26*,*fi_species:27*,*fi_species:28*,*fi_species:10*,*fi_species:19*,*fi_species:18* have both lot of probability mass on both sides of 0


#### Plot paired marginals

`mcmc_pairs(as.matrix(m7), pars = c("(Intercept)","b[(Intercept) fi_species:pike]", "b[(Intercept) fi_species:pikeperch]", "b[(Intercept) fi_species:wels]"))`

![M7_s7](/Plots/M7_s7.png "M7_s7")


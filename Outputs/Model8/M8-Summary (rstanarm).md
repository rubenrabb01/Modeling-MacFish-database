`m8 <- stan_lmer(formula=sqrt(ranged2d + 1) ~ 1 + ca_tl_mm * fi_species + (1 + season|fi_fishid), data = mean.ranged2d, seed=350)`

### Model 1c results

```
SAMPLING FOR MODEL 'continuous' NOW (CHAIN 1).
Chain 1:
Chain 1: Gradient evaluation took 0.001589 seconds
Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 15.89 seconds.
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
Chain 1:  Elapsed Time: 1373.56 seconds (Warm-up)
Chain 1:                1079.61 seconds (Sampling)
Chain 1:                2453.17 seconds (Total)
Chain 1:

SAMPLING FOR MODEL 'continuous' NOW (CHAIN 2).
Chain 2:
Chain 2: Gradient evaluation took 0.001374 seconds
Chain 2: 1000 transitions using 10 leapfrog steps per transition would take 13.74 seconds.
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
Chain 2:  Elapsed Time: 1336.51 seconds (Warm-up)
Chain 2:                1049.1 seconds (Sampling)
Chain 2:                2385.61 seconds (Total)
Chain 2:

SAMPLING FOR MODEL 'continuous' NOW (CHAIN 3).
Chain 3:
Chain 3: Gradient evaluation took 0.001271 seconds
Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 12.71 seconds.
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
Chain 3:  Elapsed Time: 1426.6 seconds (Warm-up)
Chain 3:                1085.25 seconds (Sampling)
Chain 3:                2511.85 seconds (Total)
Chain 3:

SAMPLING FOR MODEL 'continuous' NOW (CHAIN 4).
Chain 4:
Chain 4: Gradient evaluation took 0.001343 seconds
Chain 4: 1000 transitions using 10 leapfrog steps per transition would take 13.43 seconds.
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
Chain 4:  Elapsed Time: 1388.35 seconds (Warm-up)
Chain 4:                1339.13 seconds (Sampling)
Chain 4:                2727.48 seconds (Total)
```

### Quick Summary

`print(m8, digits = 2)`

```
stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + ca_tl_mm * fi_species + (1 + season |
	   fi_fishid)
 observations: 8846
------
                             Median MAD_SD
(Intercept)                   3.50   8.90
ca_tl_mm                      0.03   0.01
fi_speciespikeperch          30.17  21.98
fi_specieswels               31.81  13.58
ca_tl_mm:fi_speciespikeperch -0.06   0.04
ca_tl_mm:fi_specieswels      -0.04   0.02

Auxiliary parameter(s):
      Median MAD_SD
sigma 12.50   0.10

Error terms:
 Groups    Name            Std.Dev. Corr
 fi_fishid (Intercept)      8.22
           seasonspring_I   9.46    -0.52
           seasonspring_II 11.08    -0.09  0.17
           seasonsummer    10.17    -0.14  0.56 -0.33
           seasonwinter     9.22    -0.09  0.20  0.61  0.01
 Residual                  12.50
Num. levels: fi_fishid 31

------
* For help interpreting the printed output see ?print.stanreg
* For info on the priors used see ?prior_summary.stanreg
```

`summary(m8)`

```

Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + ca_tl_mm * fi_species + (1 + season |
	   fi_fishid)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
                                                   mean   sd    10%   50%   90%
(Intercept)                                        3.4    9.2  -7.9   3.5  15.1
ca_tl_mm                                           0.0    0.0   0.0   0.0   0.0
fi_speciespikeperch                               30.0   22.1   1.4  30.2  57.8
fi_specieswels                                    31.4   13.8  13.9  31.8  48.4
ca_tl_mm:fi_speciespikeperch                      -0.1    0.0  -0.1  -0.1   0.0
ca_tl_mm:fi_specieswels                            0.0    0.0  -0.1   0.0   0.0
b[(Intercept) fi_fishid:T449202_1]                -1.9    3.2  -6.0  -1.9   2.2
b[seasonspring_I fi_fishid:T449202_1]             13.7    2.2  11.0  13.7  16.5
b[seasonspring_II fi_fishid:T449202_1]            10.5    3.0   6.7  10.4  14.2
b[seasonsummer fi_fishid:T449202_1]               -2.2    1.9  -4.5  -2.2   0.2
b[seasonwinter fi_fishid:T449202_1]               -1.6    1.9  -4.0  -1.5   0.8
b[(Intercept) fi_fishid:T449203_1]                 2.5    6.7  -6.1   2.5  10.8
b[seasonspring_I fi_fishid:T449203_1]             -7.2    2.2 -10.0  -7.2  -4.4
b[seasonspring_II fi_fishid:T449203_1]             4.9    3.0   1.0   4.9   8.8
b[seasonsummer fi_fishid:T449203_1]              -15.9    2.0 -18.4 -16.0 -13.4
b[seasonwinter fi_fishid:T449203_1]              -12.5    1.9 -15.0 -12.6 -10.1
b[(Intercept) fi_fishid:T449204_1]                 0.1    6.7  -8.5   0.0   8.4
b[seasonspring_I fi_fishid:T449204_1]             -4.2    6.9 -12.8  -4.2   4.5
b[seasonspring_II fi_fishid:T449204_1]            -3.3    9.1 -14.7  -3.2   8.1
b[seasonsummer fi_fishid:T449204_1]                0.4    6.8  -8.1   0.3   9.0
b[seasonwinter fi_fishid:T449204_1]               -1.6    8.8 -12.7  -1.7   9.4
b[(Intercept) fi_fishid:T449207_1]                -2.2    3.6  -6.8  -2.2   2.4
b[seasonspring_I fi_fishid:T449207_1]             -4.4    2.3  -7.4  -4.5  -1.4
b[seasonspring_II fi_fishid:T449207_1]            14.4    2.8  10.8  14.4  17.9
b[seasonsummer fi_fishid:T449207_1]               -5.8    1.8  -8.1  -5.8  -3.5
b[seasonwinter fi_fishid:T449207_1]               14.0    1.8  11.6  14.0  16.4
b[(Intercept) fi_fishid:T449208_1]                -7.2    5.1 -13.8  -7.0  -0.9
b[seasonspring_I fi_fishid:T449208_1]             -0.4    2.1  -3.2  -0.4   2.3
b[seasonspring_II fi_fishid:T449208_1]            -6.2    2.9  -9.8  -6.2  -2.4
b[seasonsummer fi_fishid:T449208_1]                9.8    1.8   7.5   9.9  12.2
b[seasonwinter fi_fishid:T449208_1]               -1.9    1.8  -4.3  -2.0   0.4
b[(Intercept) fi_fishid:T449209_1]                -2.9    2.8  -6.4  -2.9   0.8
b[seasonspring_I fi_fishid:T449209_1]              7.9    2.1   5.2   7.9  10.7
b[seasonspring_II fi_fishid:T449209_1]            18.1    2.9  14.4  18.2  21.9
b[seasonsummer fi_fishid:T449209_1]                4.1    1.9   1.7   4.1   6.5
b[seasonwinter fi_fishid:T449209_1]               12.5    1.8  10.2  12.5  14.9
b[(Intercept) fi_fishid:T449213_1]                 4.2    5.1  -2.4   4.3  10.7
b[seasonspring_I fi_fishid:T449213_1]             16.0    2.2  13.2  16.0  18.9
b[seasonspring_II fi_fishid:T449213_1]             0.8    2.9  -3.0   0.8   4.6
b[seasonsummer fi_fishid:T449213_1]               24.5    1.8  22.1  24.4  26.8
b[seasonwinter fi_fishid:T449213_1]                7.3    1.9   4.9   7.2   9.6
b[(Intercept) fi_fishid:T449215_1]                 2.5    4.2  -2.9   2.5   8.0
b[seasonspring_I fi_fishid:T449215_1]             -1.1    3.5  -5.6  -1.1   3.4
b[seasonspring_II fi_fishid:T449215_1]           -13.8    9.7 -25.8 -14.2  -1.5
b[seasonsummer fi_fishid:T449215_1]               18.8    3.4  14.5  18.7  23.1
b[seasonwinter fi_fishid:T449215_1]               -3.1    9.4 -14.9  -3.2   9.0
b[(Intercept) fi_fishid:T449268_1]                 5.2    3.6   0.6   5.2   9.9
b[seasonspring_I fi_fishid:T449268_1]             -1.4    2.1  -4.1  -1.4   1.4
b[seasonspring_II fi_fishid:T449268_1]             0.7    2.7  -2.8   0.7   4.1
b[seasonsummer fi_fishid:T449268_1]                6.3    1.8   4.0   6.2   8.5
b[seasonwinter fi_fishid:T449268_1]                3.8    1.8   1.5   3.8   6.1
b[(Intercept) fi_fishid:T449269_1]                -3.7    2.8  -7.2  -3.7  -0.1
b[seasonspring_I fi_fishid:T449269_1]             14.7    2.1  11.9  14.7  17.5
b[seasonspring_II fi_fishid:T449269_1]            -3.6    7.2 -12.5  -3.9   5.5
b[seasonsummer fi_fishid:T449269_1]               15.0    1.8  12.7  15.1  17.4
b[seasonwinter fi_fishid:T449269_1]               -1.9    1.8  -4.3  -1.9   0.4
b[(Intercept) fi_fishid:T449270_2]                 7.4    2.7   4.1   7.3  10.8
b[seasonspring_I fi_fishid:T449270_2]             -6.6    6.6 -15.0  -6.7   1.7
b[seasonspring_II fi_fishid:T449270_2]             9.8    8.1  -0.1  10.0  19.7
b[seasonsummer fi_fishid:T449270_2]               -9.0    2.2 -11.8  -9.0  -6.2
b[seasonwinter fi_fishid:T449270_2]                8.8    1.9   6.3   8.8  11.3
b[(Intercept) fi_fishid:T449271_1]                -0.7    4.0  -5.9  -0.6   4.3
b[seasonspring_I fi_fishid:T449271_1]             -3.2    2.1  -5.9  -3.1  -0.5
b[seasonspring_II fi_fishid:T449271_1]            -1.7    6.5  -9.6  -1.6   6.3
b[seasonsummer fi_fishid:T449271_1]               -8.3    1.8 -10.7  -8.3  -6.0
b[seasonwinter fi_fishid:T449271_1]               -8.2    2.3 -11.1  -8.2  -5.3
b[(Intercept) fi_fishid:T449272_1]                13.0    2.7   9.7  13.0  16.5
b[seasonspring_I fi_fishid:T449272_1]             -5.3    2.1  -8.1  -5.3  -2.6
b[seasonspring_II fi_fishid:T449272_1]            -6.1    7.8 -15.5  -6.5   3.7
b[seasonsummer fi_fishid:T449272_1]                4.6    1.8   2.2   4.6   7.0
b[seasonwinter fi_fishid:T449272_1]               -4.1    2.1  -6.8  -4.1  -1.4
b[(Intercept) fi_fishid:T449274_1]                 7.3    3.8   2.4   7.3  12.1
b[seasonspring_I fi_fishid:T449274_1]              1.9    2.1  -0.8   1.9   4.5
b[seasonspring_II fi_fishid:T449274_1]             3.7    2.9   0.0   3.7   7.4
b[seasonsummer fi_fishid:T449274_1]                1.1    1.8  -1.2   1.1   3.4
b[seasonwinter fi_fishid:T449274_1]                2.0    1.9  -0.4   2.0   4.4
b[(Intercept) fi_fishid:T449275_1]                 2.8    3.3  -1.2   2.7   7.0
b[seasonspring_I fi_fishid:T449275_1]              6.5    2.1   3.7   6.5   9.2
b[seasonspring_II fi_fishid:T449275_1]            -0.9    2.9  -4.7  -0.9   2.8
b[seasonsummer fi_fishid:T449275_1]                1.6    1.8  -0.8   1.6   3.9
b[seasonwinter fi_fishid:T449275_1]               -2.3    1.8  -4.7  -2.3   0.0
b[(Intercept) fi_fishid:T449276_1]                 4.3    2.5   1.0   4.3   7.5
b[seasonspring_I fi_fishid:T449276_1]             -8.8    2.0 -11.4  -8.8  -6.1
b[seasonspring_II fi_fishid:T449276_1]            -1.7    2.7  -5.3  -1.7   1.7
b[seasonsummer fi_fishid:T449276_1]               -5.5    1.8  -7.9  -5.5  -3.2
b[seasonwinter fi_fishid:T449276_1]               -4.8    1.8  -7.1  -4.8  -2.5
b[(Intercept) fi_fishid:T449277_1]                 2.5    2.8  -1.0   2.5   6.0
b[seasonspring_I fi_fishid:T449277_1]             -0.8    2.2  -3.5  -0.8   2.0
b[seasonspring_II fi_fishid:T449277_1]            15.1    4.0  10.1  15.0  20.3
b[seasonsummer fi_fishid:T449277_1]               -4.8    1.9  -7.1  -4.8  -2.4
b[seasonwinter fi_fishid:T449277_1]               10.3    1.9   7.9  10.3  12.6
b[(Intercept) fi_fishid:T449278_1]               -12.3    2.5 -15.4 -12.3  -9.3
b[seasonspring_I fi_fishid:T449278_1]              9.6    2.1   6.9   9.6  12.4
b[seasonspring_II fi_fishid:T449278_1]           -11.2    8.7 -21.9 -11.5   0.2
b[seasonsummer fi_fishid:T449278_1]                7.1    1.8   4.7   7.1   9.4
b[seasonwinter fi_fishid:T449278_1]              -14.0    2.0 -16.6 -14.0 -11.4
b[(Intercept) fi_fishid:T449280_1]               -14.8    2.9 -18.5 -14.9 -11.1
b[seasonspring_I fi_fishid:T449280_1]             21.9    2.2  19.0  21.9  24.8
b[seasonspring_II fi_fishid:T449280_1]            25.7   10.1  12.6  26.2  38.0
b[seasonsummer fi_fishid:T449280_1]                3.3    2.0   0.7   3.2   5.8
b[seasonwinter fi_fishid:T449280_1]               26.6    1.9  24.2  26.5  29.0
b[(Intercept) fi_fishid:T449282_1]               -18.9    3.3 -23.1 -18.9 -14.6
b[seasonspring_I fi_fishid:T449282_1]             14.0    2.3  11.1  14.0  17.0
b[seasonspring_II fi_fishid:T449282_1]             5.4   11.0  -8.6   5.9  19.2
b[seasonsummer fi_fishid:T449282_1]                1.9    2.0  -0.8   1.9   4.5
b[seasonwinter fi_fishid:T449282_1]                3.2    9.9  -9.1   3.4  15.9
b[(Intercept) fi_fishid:T449284_1]                -6.0    2.4  -9.1  -6.0  -2.9
b[seasonspring_I fi_fishid:T449284_1]              2.1    2.1  -0.6   2.0   4.7
b[seasonspring_II fi_fishid:T449284_1]             6.2    3.1   2.3   6.2  10.2
b[seasonsummer fi_fishid:T449284_1]               -6.8    1.8  -9.1  -6.8  -4.6
b[seasonwinter fi_fishid:T449284_1]               -0.2    1.8  -2.5  -0.1   2.2
b[(Intercept) fi_fishid:T449285_1]               -12.0    3.0 -15.9 -12.0  -8.2
b[seasonspring_I fi_fishid:T449285_1]             11.0    2.7   7.5  10.9  14.5
b[seasonspring_II fi_fishid:T449285_1]            -0.3    9.7 -12.3  -0.5  12.1
b[seasonsummer fi_fishid:T449285_1]                8.7    2.4   5.6   8.8  11.9
b[seasonwinter fi_fishid:T449285_1]                1.5    9.1 -10.2   1.4  13.3
b[(Intercept) fi_fishid:T449286_1]                -2.3    2.4  -5.4  -2.3   0.9
b[seasonspring_I fi_fishid:T449286_1]              0.6    2.1  -2.1   0.7   3.3
b[seasonspring_II fi_fishid:T449286_1]             0.2    6.1  -7.3   0.2   7.7
b[seasonsummer fi_fishid:T449286_1]               -2.0    1.8  -4.3  -2.0   0.4
b[seasonwinter fi_fishid:T449286_1]               -1.8    2.1  -4.5  -1.8   1.0
b[(Intercept) fi_fishid:T449287_1]                -0.1    2.9  -3.7   0.0   3.6
b[seasonspring_I fi_fishid:T449287_1]             -3.5    2.1  -6.2  -3.5  -0.7
b[seasonspring_II fi_fishid:T449287_1]            -3.1    6.5 -11.0  -3.1   4.8
b[seasonsummer fi_fishid:T449287_1]               -4.1    1.8  -6.4  -4.1  -1.9
b[seasonwinter fi_fishid:T449287_1]               -6.0    2.3  -8.9  -5.9  -3.0
b[(Intercept) fi_fishid:T449288_1]                 1.8    2.5  -1.3   1.7   5.0
b[seasonspring_I fi_fishid:T449288_1]              5.5    2.1   2.9   5.5   8.2
b[seasonspring_II fi_fishid:T449288_1]             8.0    6.6  -0.3   8.1  16.3
b[seasonsummer fi_fishid:T449288_1]               -3.9    1.8  -6.3  -3.9  -1.6
b[seasonwinter fi_fishid:T449288_1]                3.1    3.0  -0.9   3.1   7.0
b[(Intercept) fi_fishid:T449310_1]                 6.6    3.7   1.9   6.7  11.2
b[seasonspring_I fi_fishid:T449310_1]            -14.1    2.2 -16.8 -14.1 -11.3
b[seasonspring_II fi_fishid:T449310_1]             1.1    7.3  -8.0   1.2  10.4
b[seasonsummer fi_fishid:T449310_1]               -9.8    1.8 -12.2  -9.7  -7.4
b[seasonwinter fi_fishid:T449310_1]                1.8    1.9  -0.6   1.8   4.4
b[(Intercept) fi_fishid:T449313_1]                -5.7    3.3 -10.0  -5.6  -1.4
b[seasonspring_I fi_fishid:T449313_1]             15.6    2.1  12.9  15.6  18.4
b[seasonspring_II fi_fishid:T449313_1]           -11.9    7.9 -21.8 -12.0  -1.9
b[seasonsummer fi_fishid:T449313_1]               21.8    1.8  19.5  21.8  24.2
b[seasonwinter fi_fishid:T449313_1]               -8.2    2.1 -11.0  -8.2  -5.5
b[(Intercept) fi_fishid:T449314_1]                -2.2    2.3  -5.0  -2.2   0.8
b[seasonspring_I fi_fishid:T449314_1]              1.3    2.1  -1.4   1.3   4.0
b[seasonspring_II fi_fishid:T449314_1]             8.3    2.9   4.5   8.4  12.1
b[seasonsummer fi_fishid:T449314_1]               -8.2    1.8 -10.5  -8.2  -5.9
b[seasonwinter fi_fishid:T449314_1]                5.2    1.8   2.9   5.1   7.5
b[(Intercept) fi_fishid:T449317_1]                -5.0    4.1 -10.1  -5.0   0.3
b[seasonspring_I fi_fishid:T449317_1]              7.5    3.2   3.5   7.4  11.6
b[seasonspring_II fi_fishid:T449317_1]             3.8    8.8  -7.5   4.0  15.0
b[seasonsummer fi_fishid:T449317_1]                0.9    3.0  -2.9   0.9   4.7
b[seasonwinter fi_fishid:T449317_1]                1.7    8.5  -9.2   1.8  12.5
b[(Intercept) fi_fishid:T449318_1]                 0.4    2.6  -3.0   0.4   3.6
b[seasonspring_I fi_fishid:T449318_1]             -0.5    2.1  -3.3  -0.5   2.2
b[seasonspring_II fi_fishid:T449318_1]            -1.5    6.0  -8.9  -1.4   5.9
b[seasonsummer fi_fishid:T449318_1]               -1.0    1.8  -3.3  -1.0   1.3
b[seasonwinter fi_fishid:T449318_1]               -2.7    2.1  -5.4  -2.7  -0.1
b[(Intercept) fi_fishid:T449319_1]                 2.8    2.4  -0.3   2.8   5.9
b[seasonspring_I fi_fishid:T449319_1]              0.5    2.1  -2.2   0.5   3.2
b[seasonspring_II fi_fishid:T449319_1]            -2.9    5.0  -8.9  -3.1   3.6
b[seasonsummer fi_fishid:T449319_1]                5.0    1.8   2.7   5.0   7.3
b[seasonwinter fi_fishid:T449319_1]               -5.9    1.8  -8.2  -5.9  -3.5
sigma                                             12.5    0.1  12.4  12.5  12.6
Sigma[fi_fishid:(Intercept),(Intercept)]          67.6   20.7  45.1  64.3  93.9
Sigma[fi_fishid:seasonspring_I,(Intercept)]      -40.6   17.2 -62.2 -39.2 -20.4
Sigma[fi_fishid:seasonspring_II,(Intercept)]      -8.0   23.7 -38.6  -8.7  22.5
Sigma[fi_fishid:seasonsummer,(Intercept)]        -11.8   19.0 -35.2 -11.4  11.3
Sigma[fi_fishid:seasonwinter,(Intercept)]         -6.8   17.0 -27.9  -6.4  14.3
Sigma[fi_fishid:seasonspring_I,seasonspring_I]    89.5   23.7  62.3  86.1 120.3
Sigma[fi_fishid:seasonspring_II,seasonspring_I]   18.2   24.9 -11.8  18.3  49.4
Sigma[fi_fishid:seasonsummer,seasonspring_I]      53.5   18.7  32.0  51.4  78.0
Sigma[fi_fishid:seasonwinter,seasonspring_I]      17.2   15.8  -2.0  16.7  36.7
Sigma[fi_fishid:seasonspring_II,seasonspring_II] 122.7   36.5  81.7 117.0 169.0
Sigma[fi_fishid:seasonsummer,seasonspring_II]    -37.2   26.2 -71.2 -34.8  -6.5
Sigma[fi_fishid:seasonwinter,seasonspring_II]     62.7   20.7  37.0  62.3  88.4
Sigma[fi_fishid:seasonsummer,seasonsummer]       103.4   24.5  74.9 100.3 135.7
Sigma[fi_fishid:seasonwinter,seasonsummer]         1.2   17.9 -20.8   0.9  23.3
Sigma[fi_fishid:seasonwinter,seasonwinter]        85.1   22.0  60.7  82.0 113.7

Fit Diagnostics:
           mean   sd   10%   50%   90%
mean_PPD 26.9    0.2 26.7  26.9  27.2

The mean_ppd is the sample average posterior predictive distribution of the outcome variable (for details see help('summary.stanreg')).

MCMC diagnostics
                                                 mcse Rhat n_eff
(Intercept)                                      0.3  1.0   864
ca_tl_mm                                         0.0  1.0   846
fi_speciespikeperch                              0.7  1.0   995
fi_specieswels                                   0.5  1.0   765
ca_tl_mm:fi_speciespikeperch                     0.0  1.0  1024
ca_tl_mm:fi_specieswels                          0.0  1.0   786
b[(Intercept) fi_fishid:T449202_1]               0.1  1.0  1477
b[seasonspring_I fi_fishid:T449202_1]            0.0  1.0  5626
b[seasonspring_II fi_fishid:T449202_1]           0.0  1.0  5439
b[seasonsummer fi_fishid:T449202_1]              0.0  1.0  4647
b[seasonwinter fi_fishid:T449202_1]              0.0  1.0  4782
b[(Intercept) fi_fishid:T449203_1]               0.2  1.0   890
b[seasonspring_I fi_fishid:T449203_1]            0.0  1.0  4396
b[seasonspring_II fi_fishid:T449203_1]           0.1  1.0  3221
b[seasonsummer fi_fishid:T449203_1]              0.0  1.0  4004
b[seasonwinter fi_fishid:T449203_1]              0.0  1.0  4008
b[(Intercept) fi_fishid:T449204_1]               0.1  1.0  3875
b[seasonspring_I fi_fishid:T449204_1]            0.1  1.0  3299
b[seasonspring_II fi_fishid:T449204_1]           0.1  1.0  4632
b[seasonsummer fi_fishid:T449204_1]              0.1  1.0  3286
b[seasonwinter fi_fishid:T449204_1]              0.1  1.0  5236
b[(Intercept) fi_fishid:T449207_1]               0.1  1.0   950
b[seasonspring_I fi_fishid:T449207_1]            0.0  1.0  6645
b[seasonspring_II fi_fishid:T449207_1]           0.0  1.0  4869
b[seasonsummer fi_fishid:T449207_1]              0.0  1.0  4451
b[seasonwinter fi_fishid:T449207_1]              0.0  1.0  4849
b[(Intercept) fi_fishid:T449208_1]               0.2  1.0  1004
b[seasonspring_I fi_fishid:T449208_1]            0.0  1.0  4318
b[seasonspring_II fi_fishid:T449208_1]           0.0  1.0  3900
b[seasonsummer fi_fishid:T449208_1]              0.0  1.0  3701
b[seasonwinter fi_fishid:T449208_1]              0.0  1.0  4100
b[(Intercept) fi_fishid:T449209_1]               0.1  1.0  1076
b[seasonspring_I fi_fishid:T449209_1]            0.0  1.0  6625
b[seasonspring_II fi_fishid:T449209_1]           0.0  1.0  3579
b[seasonsummer fi_fishid:T449209_1]              0.0  1.0  5289
b[seasonwinter fi_fishid:T449209_1]              0.0  1.0  4606
b[(Intercept) fi_fishid:T449213_1]               0.2  1.0   983
b[seasonspring_I fi_fishid:T449213_1]            0.0  1.0  4507
b[seasonspring_II fi_fishid:T449213_1]           0.0  1.0  4101
b[seasonsummer fi_fishid:T449213_1]              0.0  1.0  3993
b[seasonwinter fi_fishid:T449213_1]              0.0  1.0  4060
b[(Intercept) fi_fishid:T449215_1]               0.1  1.0  1573
b[seasonspring_I fi_fishid:T449215_1]            0.0  1.0  6567
b[seasonspring_II fi_fishid:T449215_1]           0.2  1.0  3119
b[seasonsummer fi_fishid:T449215_1]              0.0  1.0  5947
b[seasonwinter fi_fishid:T449215_1]              0.1  1.0  4400
b[(Intercept) fi_fishid:T449268_1]               0.1  1.0  1294
b[seasonspring_I fi_fishid:T449268_1]            0.0  1.0  6182
b[seasonspring_II fi_fishid:T449268_1]           0.0  1.0  5232
b[seasonsummer fi_fishid:T449268_1]              0.0  1.0  4877
b[seasonwinter fi_fishid:T449268_1]              0.0  1.0  4981
b[(Intercept) fi_fishid:T449269_1]               0.1  1.0   965
b[seasonspring_I fi_fishid:T449269_1]            0.0  1.0  6228
b[seasonspring_II fi_fishid:T449269_1]           0.1  1.0  2372
b[seasonsummer fi_fishid:T449269_1]              0.0  1.0  5488
b[seasonwinter fi_fishid:T449269_1]              0.0  1.0  5023
b[(Intercept) fi_fishid:T449270_2]               0.1  1.0  1095
b[seasonspring_I fi_fishid:T449270_2]            0.1  1.0  3400
b[seasonspring_II fi_fishid:T449270_2]           0.2  1.0  2094
b[seasonsummer fi_fishid:T449270_2]              0.0  1.0  4248
b[seasonwinter fi_fishid:T449270_2]              0.0  1.0  4441
b[(Intercept) fi_fishid:T449271_1]               0.1  1.0  1005
b[seasonspring_I fi_fishid:T449271_1]            0.0  1.0  6363
b[seasonspring_II fi_fishid:T449271_1]           0.1  1.0  3132
b[seasonsummer fi_fishid:T449271_1]              0.0  1.0  4875
b[seasonwinter fi_fishid:T449271_1]              0.0  1.0  4275
b[(Intercept) fi_fishid:T449272_1]               0.1  1.0  1225
b[seasonspring_I fi_fishid:T449272_1]            0.0  1.0  5891
b[seasonspring_II fi_fishid:T449272_1]           0.2  1.0  2024
b[seasonsummer fi_fishid:T449272_1]              0.0  1.0  4544
b[seasonwinter fi_fishid:T449272_1]              0.0  1.0  4467
b[(Intercept) fi_fishid:T449274_1]               0.1  1.0  1437
b[seasonspring_I fi_fishid:T449274_1]            0.0  1.0  4985
b[seasonspring_II fi_fishid:T449274_1]           0.0  1.0  4542
b[seasonsummer fi_fishid:T449274_1]              0.0  1.0  4353
b[seasonwinter fi_fishid:T449274_1]              0.0  1.0  4009
b[(Intercept) fi_fishid:T449275_1]               0.1  1.0   958
b[seasonspring_I fi_fishid:T449275_1]            0.0  1.0  6406
b[seasonspring_II fi_fishid:T449275_1]           0.0  1.0  4337
b[seasonsummer fi_fishid:T449275_1]              0.0  1.0  4232
b[seasonwinter fi_fishid:T449275_1]              0.0  1.0  5006
b[(Intercept) fi_fishid:T449276_1]               0.1  1.0   970
b[seasonspring_I fi_fishid:T449276_1]            0.0  1.0  6270
b[seasonspring_II fi_fishid:T449276_1]           0.0  1.0  4288
b[seasonsummer fi_fishid:T449276_1]              0.0  1.0  5016
b[seasonwinter fi_fishid:T449276_1]              0.0  1.0  4584
b[(Intercept) fi_fishid:T449277_1]               0.1  1.0  1138
b[seasonspring_I fi_fishid:T449277_1]            0.0  1.0  6181
b[seasonspring_II fi_fishid:T449277_1]           0.1  1.0  4525
b[seasonsummer fi_fishid:T449277_1]              0.0  1.0  4966
b[seasonwinter fi_fishid:T449277_1]              0.0  1.0  5592
b[(Intercept) fi_fishid:T449278_1]               0.1  1.0  1051
b[seasonspring_I fi_fishid:T449278_1]            0.0  1.0  5695
b[seasonspring_II fi_fishid:T449278_1]           0.2  1.0  1515
b[seasonsummer fi_fishid:T449278_1]              0.0  1.0  4539
b[seasonwinter fi_fishid:T449278_1]              0.0  1.0  4703
b[(Intercept) fi_fishid:T449280_1]               0.1  1.0  1309
b[seasonspring_I fi_fishid:T449280_1]            0.0  1.0  5147
b[seasonspring_II fi_fishid:T449280_1]           0.3  1.0  1463
b[seasonsummer fi_fishid:T449280_1]              0.0  1.0  4306
b[seasonwinter fi_fishid:T449280_1]              0.0  1.0  4360
b[(Intercept) fi_fishid:T449282_1]               0.1  1.0  1544
b[seasonspring_I fi_fishid:T449282_1]            0.0  1.0  4691
b[seasonspring_II fi_fishid:T449282_1]           0.2  1.0  2084
b[seasonsummer fi_fishid:T449282_1]              0.0  1.0  4429
b[seasonwinter fi_fishid:T449282_1]              0.2  1.0  2863
b[(Intercept) fi_fishid:T449284_1]               0.1  1.0  1007
b[seasonspring_I fi_fishid:T449284_1]            0.0  1.0  6559
b[seasonspring_II fi_fishid:T449284_1]           0.0  1.0  4508
b[seasonsummer fi_fishid:T449284_1]              0.0  1.0  5026
b[seasonwinter fi_fishid:T449284_1]              0.0  1.0  5560
b[(Intercept) fi_fishid:T449285_1]               0.1  1.0  1531
b[seasonspring_I fi_fishid:T449285_1]            0.0  1.0  6151
b[seasonspring_II fi_fishid:T449285_1]           0.2  1.0  2808
b[seasonsummer fi_fishid:T449285_1]              0.0  1.0  4723
b[seasonwinter fi_fishid:T449285_1]              0.1  1.0  4220
b[(Intercept) fi_fishid:T449286_1]               0.1  1.0  1019
b[seasonspring_I fi_fishid:T449286_1]            0.0  1.0  6167
b[seasonspring_II fi_fishid:T449286_1]           0.1  1.0  3380
b[seasonsummer fi_fishid:T449286_1]              0.0  1.0  5099
b[seasonwinter fi_fishid:T449286_1]              0.0  1.0  4765
b[(Intercept) fi_fishid:T449287_1]               0.1  1.0  1124
b[seasonspring_I fi_fishid:T449287_1]            0.0  1.0  6640
b[seasonspring_II fi_fishid:T449287_1]           0.1  1.0  3110
b[seasonsummer fi_fishid:T449287_1]              0.0  1.0  5509
b[seasonwinter fi_fishid:T449287_1]              0.0  1.0  5202
b[(Intercept) fi_fishid:T449288_1]               0.1  1.0  1010
b[seasonspring_I fi_fishid:T449288_1]            0.0  1.0  7010
b[seasonspring_II fi_fishid:T449288_1]           0.1  1.0  2789
b[seasonsummer fi_fishid:T449288_1]              0.0  1.0  4865
b[seasonwinter fi_fishid:T449288_1]              0.0  1.0  5086
b[(Intercept) fi_fishid:T449310_1]               0.1  1.0  1588
b[seasonspring_I fi_fishid:T449310_1]            0.0  1.0  5787
b[seasonspring_II fi_fishid:T449310_1]           0.1  1.0  2395
b[seasonsummer fi_fishid:T449310_1]              0.0  1.0  4553
b[seasonwinter fi_fishid:T449310_1]              0.0  1.0  4517
b[(Intercept) fi_fishid:T449313_1]               0.1  1.0  1117
b[seasonspring_I fi_fishid:T449313_1]            0.0  1.0  5110
b[seasonspring_II fi_fishid:T449313_1]           0.2  1.0  1867
b[seasonsummer fi_fishid:T449313_1]              0.0  1.0  4428
b[seasonwinter fi_fishid:T449313_1]              0.0  1.0  4432
b[(Intercept) fi_fishid:T449314_1]               0.1  1.0  1075
b[seasonspring_I fi_fishid:T449314_1]            0.0  1.0  6122
b[seasonspring_II fi_fishid:T449314_1]           0.0  1.0  4443
b[seasonsummer fi_fishid:T449314_1]              0.0  1.0  5233
b[seasonwinter fi_fishid:T449314_1]              0.0  1.0  5559
b[(Intercept) fi_fishid:T449317_1]               0.1  1.0  1971
b[seasonspring_I fi_fishid:T449317_1]            0.0  1.0  6638
b[seasonspring_II fi_fishid:T449317_1]           0.1  1.0  4306
b[seasonsummer fi_fishid:T449317_1]              0.0  1.0  5916
b[seasonwinter fi_fishid:T449317_1]              0.1  1.0  4985
b[(Intercept) fi_fishid:T449318_1]               0.1  1.0  1011
b[seasonspring_I fi_fishid:T449318_1]            0.0  1.0  6739
b[seasonspring_II fi_fishid:T449318_1]           0.1  1.0  3325
b[seasonsummer fi_fishid:T449318_1]              0.0  1.0  5026
b[seasonwinter fi_fishid:T449318_1]              0.0  1.0  5075
b[(Intercept) fi_fishid:T449319_1]               0.1  1.0  1050
b[seasonspring_I fi_fishid:T449319_1]            0.0  1.0  6981
b[seasonspring_II fi_fishid:T449319_1]           0.1  1.0  2315
b[seasonsummer fi_fishid:T449319_1]              0.0  1.0  5939
b[seasonwinter fi_fishid:T449319_1]              0.0  1.0  5433
sigma                                            0.0  1.0  6983
Sigma[fi_fishid:(Intercept),(Intercept)]         0.5  1.0  1805
Sigma[fi_fishid:seasonspring_I,(Intercept)]      0.5  1.0  1364
Sigma[fi_fishid:seasonspring_II,(Intercept)]     0.8  1.0   853
Sigma[fi_fishid:seasonsummer,(Intercept)]        0.7  1.0   823
Sigma[fi_fishid:seasonwinter,(Intercept)]        0.5  1.0  1202
Sigma[fi_fishid:seasonspring_I,seasonspring_I]   0.6  1.0  1384
Sigma[fi_fishid:seasonspring_II,seasonspring_I]  0.7  1.0  1194
Sigma[fi_fishid:seasonsummer,seasonspring_I]     0.5  1.0  1555
Sigma[fi_fishid:seasonwinter,seasonspring_I]     0.3  1.0  2617
Sigma[fi_fishid:seasonspring_II,seasonspring_II] 0.8  1.0  1916
Sigma[fi_fishid:seasonsummer,seasonspring_II]    0.7  1.0  1495
Sigma[fi_fishid:seasonwinter,seasonspring_II]    0.5  1.0  1452
Sigma[fi_fishid:seasonsummer,seasonsummer]       0.6  1.0  1772
Sigma[fi_fishid:seasonwinter,seasonsummer]       0.4  1.0  2543
Sigma[fi_fishid:seasonwinter,seasonwinter]       0.4  1.0  2433
mean_PPD                                         0.0  1.0  3970
log-posterior                                    0.4  1.0   924

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1)
```

### Check priors used

`prior_summary(object = m8)`

```{r, echo=FALSE, eval=TRUE}

Priors for model 'm8'
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
    ~ normal(location = [0,0,0,...], scale = [ 0.11,38.65,38.65,...])

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

`summary(m8, pars = c("(Intercept)", "sigma", "Sigma[fi_fishid:season,season"), probs = c(0.025, 0.975), digits = 2)`

```
Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + ca_tl_mm * fi_species + (1 + season |
	   fi_fishid)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
              mean   sd     2.5%   97.5%
(Intercept)   3.40   9.18 -14.74  22.01
sigma        12.50   0.10  12.32  12.69

MCMC diagnostics
            mcse Rhat n_eff
(Intercept) 0.31 1.01  864
sigma       0.00 1.00 6983

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).
```

### Extract the posterior draws for all parameters

`sims <- as.matrix(m8)`

`dim(sims)`

```
[1] 4000   177

```

`para_name <- colnames(sims)`

`para_name`

```
  [1] "(Intercept)"                                      "ca_tl_mm"                                         "fi_speciespikeperch"
  [4] "fi_specieswels"                                   "ca_tl_mm:fi_speciespikeperch"                     "ca_tl_mm:fi_specieswels"
  [7] "b[(Intercept) fi_fishid:T449202_1]"               "b[seasonspring_I fi_fishid:T449202_1]"            "b[seasonspring_II fi_fishid:T449202_1]"
 [10] "b[seasonsummer fi_fishid:T449202_1]"              "b[seasonwinter fi_fishid:T449202_1]"              "b[(Intercept) fi_fishid:T449203_1]"
 [13] "b[seasonspring_I fi_fishid:T449203_1]"            "b[seasonspring_II fi_fishid:T449203_1]"           "b[seasonsummer fi_fishid:T449203_1]"
 [16] "b[seasonwinter fi_fishid:T449203_1]"              "b[(Intercept) fi_fishid:T449204_1]"               "b[seasonspring_I fi_fishid:T449204_1]"
 [19] "b[seasonspring_II fi_fishid:T449204_1]"           "b[seasonsummer fi_fishid:T449204_1]"              "b[seasonwinter fi_fishid:T449204_1]"
 [22] "b[(Intercept) fi_fishid:T449207_1]"               "b[seasonspring_I fi_fishid:T449207_1]"            "b[seasonspring_II fi_fishid:T449207_1]"
 [25] "b[seasonsummer fi_fishid:T449207_1]"              "b[seasonwinter fi_fishid:T449207_1]"              "b[(Intercept) fi_fishid:T449208_1]"
 [28] "b[seasonspring_I fi_fishid:T449208_1]"            "b[seasonspring_II fi_fishid:T449208_1]"           "b[seasonsummer fi_fishid:T449208_1]"
 [31] "b[seasonwinter fi_fishid:T449208_1]"              "b[(Intercept) fi_fishid:T449209_1]"               "b[seasonspring_I fi_fishid:T449209_1]"
 [34] "b[seasonspring_II fi_fishid:T449209_1]"           "b[seasonsummer fi_fishid:T449209_1]"              "b[seasonwinter fi_fishid:T449209_1]"
 [37] "b[(Intercept) fi_fishid:T449213_1]"               "b[seasonspring_I fi_fishid:T449213_1]"            "b[seasonspring_II fi_fishid:T449213_1]"
 [40] "b[seasonsummer fi_fishid:T449213_1]"              "b[seasonwinter fi_fishid:T449213_1]"              "b[(Intercept) fi_fishid:T449215_1]"
 [43] "b[seasonspring_I fi_fishid:T449215_1]"            "b[seasonspring_II fi_fishid:T449215_1]"           "b[seasonsummer fi_fishid:T449215_1]"
 [46] "b[seasonwinter fi_fishid:T449215_1]"              "b[(Intercept) fi_fishid:T449268_1]"               "b[seasonspring_I fi_fishid:T449268_1]"
 [49] "b[seasonspring_II fi_fishid:T449268_1]"           "b[seasonsummer fi_fishid:T449268_1]"              "b[seasonwinter fi_fishid:T449268_1]"
 [52] "b[(Intercept) fi_fishid:T449269_1]"               "b[seasonspring_I fi_fishid:T449269_1]"            "b[seasonspring_II fi_fishid:T449269_1]"
 [55] "b[seasonsummer fi_fishid:T449269_1]"              "b[seasonwinter fi_fishid:T449269_1]"              "b[(Intercept) fi_fishid:T449270_2]"
 [58] "b[seasonspring_I fi_fishid:T449270_2]"            "b[seasonspring_II fi_fishid:T449270_2]"           "b[seasonsummer fi_fishid:T449270_2]"
 [61] "b[seasonwinter fi_fishid:T449270_2]"              "b[(Intercept) fi_fishid:T449271_1]"               "b[seasonspring_I fi_fishid:T449271_1]"
 [64] "b[seasonspring_II fi_fishid:T449271_1]"           "b[seasonsummer fi_fishid:T449271_1]"              "b[seasonwinter fi_fishid:T449271_1]"
 [67] "b[(Intercept) fi_fishid:T449272_1]"               "b[seasonspring_I fi_fishid:T449272_1]"            "b[seasonspring_II fi_fishid:T449272_1]"
 [70] "b[seasonsummer fi_fishid:T449272_1]"              "b[seasonwinter fi_fishid:T449272_1]"              "b[(Intercept) fi_fishid:T449274_1]"
 [73] "b[seasonspring_I fi_fishid:T449274_1]"            "b[seasonspring_II fi_fishid:T449274_1]"           "b[seasonsummer fi_fishid:T449274_1]"
 [76] "b[seasonwinter fi_fishid:T449274_1]"              "b[(Intercept) fi_fishid:T449275_1]"               "b[seasonspring_I fi_fishid:T449275_1]"
 [79] "b[seasonspring_II fi_fishid:T449275_1]"           "b[seasonsummer fi_fishid:T449275_1]"              "b[seasonwinter fi_fishid:T449275_1]"
 [82] "b[(Intercept) fi_fishid:T449276_1]"               "b[seasonspring_I fi_fishid:T449276_1]"            "b[seasonspring_II fi_fishid:T449276_1]"
 [85] "b[seasonsummer fi_fishid:T449276_1]"              "b[seasonwinter fi_fishid:T449276_1]"              "b[(Intercept) fi_fishid:T449277_1]"
 [88] "b[seasonspring_I fi_fishid:T449277_1]"            "b[seasonspring_II fi_fishid:T449277_1]"           "b[seasonsummer fi_fishid:T449277_1]"
 [91] "b[seasonwinter fi_fishid:T449277_1]"              "b[(Intercept) fi_fishid:T449278_1]"               "b[seasonspring_I fi_fishid:T449278_1]"
 [94] "b[seasonspring_II fi_fishid:T449278_1]"           "b[seasonsummer fi_fishid:T449278_1]"              "b[seasonwinter fi_fishid:T449278_1]"
 [97] "b[(Intercept) fi_fishid:T449280_1]"               "b[seasonspring_I fi_fishid:T449280_1]"            "b[seasonspring_II fi_fishid:T449280_1]"
[100] "b[seasonsummer fi_fishid:T449280_1]"              "b[seasonwinter fi_fishid:T449280_1]"              "b[(Intercept) fi_fishid:T449282_1]"
[103] "b[seasonspring_I fi_fishid:T449282_1]"            "b[seasonspring_II fi_fishid:T449282_1]"           "b[seasonsummer fi_fishid:T449282_1]"
[106] "b[seasonwinter fi_fishid:T449282_1]"              "b[(Intercept) fi_fishid:T449284_1]"               "b[seasonspring_I fi_fishid:T449284_1]"
[109] "b[seasonspring_II fi_fishid:T449284_1]"           "b[seasonsummer fi_fishid:T449284_1]"              "b[seasonwinter fi_fishid:T449284_1]"
[112] "b[(Intercept) fi_fishid:T449285_1]"               "b[seasonspring_I fi_fishid:T449285_1]"            "b[seasonspring_II fi_fishid:T449285_1]"
[115] "b[seasonsummer fi_fishid:T449285_1]"              "b[seasonwinter fi_fishid:T449285_1]"              "b[(Intercept) fi_fishid:T449286_1]"
[118] "b[seasonspring_I fi_fishid:T449286_1]"            "b[seasonspring_II fi_fishid:T449286_1]"           "b[seasonsummer fi_fishid:T449286_1]"
[121] "b[seasonwinter fi_fishid:T449286_1]"              "b[(Intercept) fi_fishid:T449287_1]"               "b[seasonspring_I fi_fishid:T449287_1]"
[124] "b[seasonspring_II fi_fishid:T449287_1]"           "b[seasonsummer fi_fishid:T449287_1]"              "b[seasonwinter fi_fishid:T449287_1]"
[127] "b[(Intercept) fi_fishid:T449288_1]"               "b[seasonspring_I fi_fishid:T449288_1]"            "b[seasonspring_II fi_fishid:T449288_1]"
[130] "b[seasonsummer fi_fishid:T449288_1]"              "b[seasonwinter fi_fishid:T449288_1]"              "b[(Intercept) fi_fishid:T449310_1]"
[133] "b[seasonspring_I fi_fishid:T449310_1]"            "b[seasonspring_II fi_fishid:T449310_1]"           "b[seasonsummer fi_fishid:T449310_1]"
[136] "b[seasonwinter fi_fishid:T449310_1]"              "b[(Intercept) fi_fishid:T449313_1]"               "b[seasonspring_I fi_fishid:T449313_1]"
[139] "b[seasonspring_II fi_fishid:T449313_1]"           "b[seasonsummer fi_fishid:T449313_1]"              "b[seasonwinter fi_fishid:T449313_1]"
[142] "b[(Intercept) fi_fishid:T449314_1]"               "b[seasonspring_I fi_fishid:T449314_1]"            "b[seasonspring_II fi_fishid:T449314_1]"
[145] "b[seasonsummer fi_fishid:T449314_1]"              "b[seasonwinter fi_fishid:T449314_1]"              "b[(Intercept) fi_fishid:T449317_1]"
[148] "b[seasonspring_I fi_fishid:T449317_1]"            "b[seasonspring_II fi_fishid:T449317_1]"           "b[seasonsummer fi_fishid:T449317_1]"
[151] "b[seasonwinter fi_fishid:T449317_1]"              "b[(Intercept) fi_fishid:T449318_1]"               "b[seasonspring_I fi_fishid:T449318_1]"
[154] "b[seasonspring_II fi_fishid:T449318_1]"           "b[seasonsummer fi_fishid:T449318_1]"              "b[seasonwinter fi_fishid:T449318_1]"
[157] "b[(Intercept) fi_fishid:T449319_1]"               "b[seasonspring_I fi_fishid:T449319_1]"            "b[seasonspring_II fi_fishid:T449319_1]"
[160] "b[seasonsummer fi_fishid:T449319_1]"              "b[seasonwinter fi_fishid:T449319_1]"              "sigma"
[163] "Sigma[fi_fishid:(Intercept),(Intercept)]"         "Sigma[fi_fishid:seasonspring_I,(Intercept)]"      "Sigma[fi_fishid:seasonspring_II,(Intercept)]"
[166] "Sigma[fi_fishid:seasonsummer,(Intercept)]"        "Sigma[fi_fishid:seasonwinter,(Intercept)]"        "Sigma[fi_fishid:seasonspring_I,seasonspring_I]"
[169] "Sigma[fi_fishid:seasonspring_II,seasonspring_I]"  "Sigma[fi_fishid:seasonsummer,seasonspring_I]"     "Sigma[fi_fishid:seasonwinter,seasonspring_I]"
[172] "Sigma[fi_fishid:seasonspring_II,seasonspring_II]" "Sigma[fi_fishid:seasonsummer,seasonspring_II]"    "Sigma[fi_fishid:seasonwinter,seasonspring_II]"
[175] "Sigma[fi_fishid:seasonsummer,seasonsummer]"       "Sigma[fi_fishid:seasonwinter,seasonsummer]"       "Sigma[fi_fishid:seasonwinter,seasonwinter]"
```

- Obtain fi_species-level varying intercept a_j

 - draws for overall mean

`mu_a_sims <- as.matrix(m8, pars = "(Intercept)")`

 - draws for 3 fi_species-level error

`fish_err <- as.matrix(m8,regex_pars = "b\\[\\(Intercept\\) fi_fishid\\:")`

 - draws for 3 species' varying intercepts

`fish_inter <- as.numeric(mu_a_sims) + fish_err`

- Obtain sigma_y and sigma_alpha^2

 -draws for sigma_y

`sigma_y <- as.matrix(m8, pars = "sigma")`

 - draws for sigma_alpha^2

`sigma_alpha <- as.matrix(m8, pars = "Sigma[fi_fishid:(Intercept),(Intercept)]")`


### Obtaining means, SD, medians and 95% credible intervals of varying intercepts

In *fish_inter*, we have saved 4,000 posterior draws (from all 4 chains) for the varying intercepts αj of the 30 fishes. For example, the first column of the 4,000 by 30 matrix is a vector of 4,000 posterior simulation draws for the first fish's (*fi_species*:T449202_1) varying intercept α1. One quantitative way to summarize the posterior probability distribution of these 4,000 estimates for α1 is to examine their quantiles.

- Posterior mean of each alpha

`a_mean <- apply(X = fish_inter, MARGIN = 2, FUN = mean)`

`a_mean'

```
b[(Intercept) fi_fishid:T449202_1] b[(Intercept) fi_fishid:T449203_1] b[(Intercept) fi_fishid:T449204_1] b[(Intercept) fi_fishid:T449207_1] b[(Intercept) fi_fishid:T449208_1]
                    1.488096776305                     5.930341354422                     3.470938959616                     1.202027575608                    -3.775466113095
b[(Intercept) fi_fishid:T449209_1] b[(Intercept) fi_fishid:T449213_1] b[(Intercept) fi_fishid:T449215_1] b[(Intercept) fi_fishid:T449268_1] b[(Intercept) fi_fishid:T449269_1]
                    0.528027869590                     7.589143271203                     5.909803987485                     8.627844210418                    -0.254354718171
b[(Intercept) fi_fishid:T449270_2] b[(Intercept) fi_fishid:T449271_1] b[(Intercept) fi_fishid:T449272_1] b[(Intercept) fi_fishid:T449274_1] b[(Intercept) fi_fishid:T449275_1]
                   10.792297906424                     2.717321454444                    16.450497186986                    10.673726303672                     6.160411387694
b[(Intercept) fi_fishid:T449276_1] b[(Intercept) fi_fishid:T449277_1] b[(Intercept) fi_fishid:T449278_1] b[(Intercept) fi_fishid:T449280_1] b[(Intercept) fi_fishid:T449282_1]
                    7.708812869494                     5.904631164791                    -8.935728091715                   -11.415112133370                   -15.485303368182
b[(Intercept) fi_fishid:T449284_1] b[(Intercept) fi_fishid:T449285_1] b[(Intercept) fi_fishid:T449286_1] b[(Intercept) fi_fishid:T449287_1] b[(Intercept) fi_fishid:T449288_1]
                   -2.596597642768                    -8.617643078606                     1.134202093496                     3.339118403444                     5.212303503747
b[(Intercept) fi_fishid:T449310_1] b[(Intercept) fi_fishid:T449313_1] b[(Intercept) fi_fishid:T449314_1] b[(Intercept) fi_fishid:T449317_1] b[(Intercept) fi_fishid:T449318_1]
                   10.020160214260                    -2.261193626734                     1.210269242607                    -1.577369846520                     3.771989102092
b[(Intercept) fi_fishid:T449319_1]
                    6.167663633275
```

- Posterior SD of each alpha

`a_sd <- apply(X = fish_inter, MARGIN = 2, FUN = sd)`

`a_sd`

```
b[(Intercept) fi_fishid:T449202_1] b[(Intercept) fi_fishid:T449203_1] b[(Intercept) fi_fishid:T449204_1] b[(Intercept) fi_fishid:T449207_1] b[(Intercept) fi_fishid:T449208_1]
                     9.65329749812                     15.01202139668                     10.69375684928                      6.64136912492                      9.69433945211
b[(Intercept) fi_fishid:T449209_1] b[(Intercept) fi_fishid:T449213_1] b[(Intercept) fi_fishid:T449215_1] b[(Intercept) fi_fishid:T449268_1] b[(Intercept) fi_fishid:T449269_1]
                     8.35485726738                      9.80144666435                      9.56803288237                      9.42140009577                      9.93376124122
b[(Intercept) fi_fishid:T449270_2] b[(Intercept) fi_fishid:T449271_1] b[(Intercept) fi_fishid:T449272_1] b[(Intercept) fi_fishid:T449274_1] b[(Intercept) fi_fishid:T449275_1]
                     8.97954428680                     11.88189799082                      9.09513823437                      9.43303671296                     10.24803768672
b[(Intercept) fi_fishid:T449276_1] b[(Intercept) fi_fishid:T449277_1] b[(Intercept) fi_fishid:T449278_1] b[(Intercept) fi_fishid:T449280_1] b[(Intercept) fi_fishid:T449282_1]
                     9.83358679231                      8.41400415205                      9.70422613434                      9.23887411446                      9.39575899337
b[(Intercept) fi_fishid:T449284_1] b[(Intercept) fi_fishid:T449285_1] b[(Intercept) fi_fishid:T449286_1] b[(Intercept) fi_fishid:T449287_1] b[(Intercept) fi_fishid:T449288_1]
                     9.69241721753                      8.51502502728                      9.72024918437                      7.85142242042                      9.79614873701
b[(Intercept) fi_fishid:T449310_1] b[(Intercept) fi_fishid:T449313_1] b[(Intercept) fi_fishid:T449314_1] b[(Intercept) fi_fishid:T449317_1] b[(Intercept) fi_fishid:T449318_1]
                     9.88848615563                      9.32150150869                      9.47556917991                     10.00497470542                      9.84115253344
b[(Intercept) fi_fishid:T449319_1]
                     9.74506191025
```

- Posterior median and 95% credible interval

`a_quant <- apply(X = fish_inter, MARGIN = 2, FUN = quantile, probs = c(0.025, 0.50, 0.975))`

`a_quant <- data.frame(t(a_quant))`

`a_quant`

```
                                             X2.5.              X50.         X97.5.
b[(Intercept) fi_fishid:T449202_1] -17.82166047404   1.4932389180534 20.40613386223
b[(Intercept) fi_fishid:T449203_1] -23.71225512711   5.8948705531974 35.93748684381
b[(Intercept) fi_fishid:T449204_1] -17.51308152637   3.4921389757709 24.68833948675
b[(Intercept) fi_fishid:T449207_1] -11.61047602210   1.2333900463444 14.40391511062
b[(Intercept) fi_fishid:T449208_1] -23.15041601491  -3.6500301563851 15.13794667984
b[(Intercept) fi_fishid:T449209_1] -15.91906613465   0.5015916905774 17.01189193137
b[(Intercept) fi_fishid:T449213_1] -11.90214663606   7.6229792563710 27.17680079692
b[(Intercept) fi_fishid:T449215_1] -13.08134821957   5.9323262031203 24.89024856858
b[(Intercept) fi_fishid:T449268_1]  -9.75487383653   8.5676866653886 27.62812743444
b[(Intercept) fi_fishid:T449269_1] -19.74101661984  -0.0258195417174 19.69015002901
b[(Intercept) fi_fishid:T449270_2]  -6.56836174447  10.8560103656001 28.76375792807
b[(Intercept) fi_fishid:T449271_1] -20.74439524743   2.6758949275230 26.39053393708
b[(Intercept) fi_fishid:T449272_1]  -1.28427391313  16.2999307028560 34.40934306412
b[(Intercept) fi_fishid:T449274_1]  -7.87961186120  10.6345774978319 29.47623731363
b[(Intercept) fi_fishid:T449275_1] -13.93884715753   6.1243766960950 26.47494814371
b[(Intercept) fi_fishid:T449276_1] -11.75708685510   7.8050930819379 27.31395522746
b[(Intercept) fi_fishid:T449277_1] -10.89909342411   5.8768772389476 22.40541030869
b[(Intercept) fi_fishid:T449278_1] -28.15253569557  -8.9066066848145 10.46640898469
b[(Intercept) fi_fishid:T449280_1] -29.53059687591 -11.5153816746803  6.86866694446
b[(Intercept) fi_fishid:T449282_1] -34.14065602995 -15.5027604850179  3.20786566639
b[(Intercept) fi_fishid:T449284_1] -21.86968643957  -2.4558824577844 16.95541225063
b[(Intercept) fi_fishid:T449285_1] -25.54368247644  -8.5985872457861  8.13978250282
b[(Intercept) fi_fishid:T449286_1] -18.05159516159   1.1976531639863 20.75983380560
b[(Intercept) fi_fishid:T449287_1] -12.02263786225   3.4263187073033 18.99879907907
b[(Intercept) fi_fishid:T449288_1] -13.94915411231   5.2422174082118 24.96870470466
b[(Intercept) fi_fishid:T449310_1]  -9.30261672756  10.1045487405226 29.77786020301
b[(Intercept) fi_fishid:T449313_1] -20.73448890141  -2.1385232629541 15.92084647210
b[(Intercept) fi_fishid:T449314_1] -17.61070637628   1.2018879739441 20.49727135555
b[(Intercept) fi_fishid:T449317_1] -20.90605947708  -1.4686081635592 18.50002122759
b[(Intercept) fi_fishid:T449318_1] -16.00933981930   3.7222651850417 23.88654345262
b[(Intercept) fi_fishid:T449319_1] -13.09479251582   6.2504788185429 25.91466613664
```

`names(a_quant) <- c("X2.5.", "X50.", "X97.5.")`

- Combine summary statistics of posterior simulation draws

`a_df <- data.frame(a_mean, a_sd, a_quant)`

`round(head(a_df), 2)`

```
                                  a_mean  a_sd  X2.5.  X50. X97.5.
b[(Intercept) fi_fishid:T449202_1]   1.49  9.65 -17.82  1.49  20.41
b[(Intercept) fi_fishid:T449203_1]   5.93 15.01 -23.71  5.89  35.94
b[(Intercept) fi_fishid:T449204_1]   3.47 10.69 -17.51  3.49  24.69
b[(Intercept) fi_fishid:T449207_1]   1.20  6.64 -11.61  1.23  14.40
b[(Intercept) fi_fishid:T449208_1]  -3.78  9.69 -23.15 -3.65  15.14
b[(Intercept) fi_fishid:T449209_1]   0.53  8.35 -15.92  0.50  17.01
```

### Plots

Caterpillar plot to show the fully Bayes estimates for the *fi_species* varying intercepts in rank order together with their 95% credible interval

- Sort dataframe containing an estimated alpha's mean and sd for every fi_species

`a_df <- a_df[order(a_df$a_mean), ]`

`a_df`

```
                                             a_mean           a_sd           X2.5.              X50.         X97.5.
b[(Intercept) fi_fishid:T449282_1] -15.485303368182  9.39575899337 -34.14065602995 -15.5027604850179  3.20786566639
b[(Intercept) fi_fishid:T449280_1] -11.415112133370  9.23887411446 -29.53059687591 -11.5153816746803  6.86866694446
b[(Intercept) fi_fishid:T449278_1]  -8.935728091715  9.70422613434 -28.15253569557  -8.9066066848145 10.46640898469
b[(Intercept) fi_fishid:T449285_1]  -8.617643078606  8.51502502728 -25.54368247644  -8.5985872457861  8.13978250282
b[(Intercept) fi_fishid:T449208_1]  -3.775466113095  9.69433945211 -23.15041601491  -3.6500301563851 15.13794667984
b[(Intercept) fi_fishid:T449284_1]  -2.596597642768  9.69241721753 -21.86968643957  -2.4558824577844 16.95541225063
b[(Intercept) fi_fishid:T449313_1]  -2.261193626734  9.32150150869 -20.73448890141  -2.1385232629541 15.92084647210
b[(Intercept) fi_fishid:T449317_1]  -1.577369846520 10.00497470542 -20.90605947708  -1.4686081635592 18.50002122759
b[(Intercept) fi_fishid:T449269_1]  -0.254354718171  9.93376124122 -19.74101661984  -0.0258195417174 19.69015002901
b[(Intercept) fi_fishid:T449209_1]   0.528027869590  8.35485726738 -15.91906613465   0.5015916905774 17.01189193137
b[(Intercept) fi_fishid:T449286_1]   1.134202093496  9.72024918437 -18.05159516159   1.1976531639863 20.75983380560
b[(Intercept) fi_fishid:T449207_1]   1.202027575608  6.64136912492 -11.61047602210   1.2333900463444 14.40391511062
b[(Intercept) fi_fishid:T449314_1]   1.210269242607  9.47556917991 -17.61070637628   1.2018879739441 20.49727135555
b[(Intercept) fi_fishid:T449202_1]   1.488096776305  9.65329749812 -17.82166047404   1.4932389180534 20.40613386223
b[(Intercept) fi_fishid:T449271_1]   2.717321454444 11.88189799082 -20.74439524743   2.6758949275230 26.39053393708
b[(Intercept) fi_fishid:T449287_1]   3.339118403444  7.85142242042 -12.02263786225   3.4263187073033 18.99879907907
b[(Intercept) fi_fishid:T449204_1]   3.470938959616 10.69375684928 -17.51308152637   3.4921389757709 24.68833948675
b[(Intercept) fi_fishid:T449318_1]   3.771989102092  9.84115253344 -16.00933981930   3.7222651850417 23.88654345262
b[(Intercept) fi_fishid:T449288_1]   5.212303503747  9.79614873701 -13.94915411231   5.2422174082118 24.96870470466
b[(Intercept) fi_fishid:T449277_1]   5.904631164791  8.41400415205 -10.89909342411   5.8768772389476 22.40541030869
b[(Intercept) fi_fishid:T449215_1]   5.909803987485  9.56803288237 -13.08134821957   5.9323262031203 24.89024856858
b[(Intercept) fi_fishid:T449203_1]   5.930341354422 15.01202139668 -23.71225512711   5.8948705531974 35.93748684381
b[(Intercept) fi_fishid:T449275_1]   6.160411387694 10.24803768672 -13.93884715753   6.1243766960950 26.47494814371
b[(Intercept) fi_fishid:T449319_1]   6.167663633275  9.74506191025 -13.09479251582   6.2504788185429 25.91466613664
b[(Intercept) fi_fishid:T449213_1]   7.589143271203  9.80144666435 -11.90214663606   7.6229792563710 27.17680079692
b[(Intercept) fi_fishid:T449276_1]   7.708812869494  9.83358679231 -11.75708685510   7.8050930819379 27.31395522746
b[(Intercept) fi_fishid:T449268_1]   8.627844210418  9.42140009577  -9.75487383653   8.5676866653886 27.62812743444
b[(Intercept) fi_fishid:T449310_1]  10.020160214260  9.88848615563  -9.30261672756  10.1045487405226 29.77786020301
b[(Intercept) fi_fishid:T449274_1]  10.673726303672  9.43303671296  -7.87961186120  10.6345774978319 29.47623731363
b[(Intercept) fi_fishid:T449270_2]  10.792297906424  8.97954428680  -6.56836174447  10.8560103656001 28.76375792807
b[(Intercept) fi_fishid:T449272_1]  16.450497186986  9.09513823437  -1.28427391313  16.2999307028560 34.40934306412
```

`a_df$a_rank <- c(1 : dim(a_df)[1])`    # a vector of fi_fishid rank

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
![M8_s1](/Plots/M8_s1.png "M8_s1")

### Differences between species averages

Here I'll explore the posterior distribution of the differences between each two fish individuals with descriptive statistics and histograms

- - The difference between fish 1 and fish 2 is:

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
1 -4.44 7.38 -19.14 -4.45 10.41

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
  geom_text(aes(0.45, 20, label = "mean = -4.44"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 7.38"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M8_s2](/Plots/M8_s2.png "M8_s2")

The expected difference comes to 3.30 with a standard deviation of 0.46 and a wide range of uncertainty. The 95% credible interval is [2.48,4.29], so we are 95% certain that the true value of the difference between the two species lies within the range, given the data

We also can get the proportion of the time that pike has a higher mean than pikeperch:

`prop.table(table(fish_inter[, 1] > fish_inter[, 2]))`

```
  FALSE    TRUE
0.73075 0.26925
```

### Cross-validation checking

We can use Pareto-smoothed importance sampling LOO CV as model checking tool (2017b)

`library(loo)`

`loo<-loo(m8)`

`loo`

```
Computed from 4000 by 8846 log-likelihood matrix

         Estimate    SE
elpd_loo -34961.7  79.3
p_loo       128.6   2.8
looic     69923.3 158.6
------
Monte Carlo SE of elpd_loo is 0.2.

All Pareto k estimates are good (k < 0.5).
See help('pareto-k-diagnostic') for details.
```
`plot(loo, diagnostic = c("k", "n_eff"), label_points = FALSE, main = "PSIS diagnostic plot")`

![M8_sloo](/Plots/M8_sloo.png "M8_sloo")


### Analyse posterior

#### Plot posterior

- Including random effects from *fi_species*

`library(bayesplot)`

- Excluding random effects from *fi_fishid*

`mcmc_areas(as.matrix(m8), prob_outer = .999, pars = c("(Intercept)","b[(Intercept) fi_fishid:T449202_1]", "b[(Intercept) fi_fishid:T449203_1]"))`

![M8_s3](/Plots/M8_s3.png "M8_s3")


#### Plot paired marginals

`mcmc_pairs(as.matrix(m8), pars = c("(Intercept)","b[(Intercept) fi_fishid:T449202_1]", "b[(Intercept) fi_fishid:T449203_1]"))`

![M8_s4](/Plots/M8_s4.png "M8_s4")

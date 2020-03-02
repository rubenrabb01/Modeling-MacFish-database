`m6<-stan_lmer(formula=sqrt(ranged2d + 1) ~ 1 + fi_species +(1 + season|fi_fishid), data = mean.ranged2d, seed=350)`

### Model 6 results

```

```

### Quick Summary

`print(m6, digits = 2)`

```
stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + fi_species + (1 + season | fi_fishid)
 observations: 8846
------
                    Median MAD_SD
(Intercept)         24.65   2.64
fi_speciespikeperch -3.53   4.05
fi_specieswels       4.34   3.20

Auxiliary parameter(s):
      Median MAD_SD
sigma 12.50   0.09

Error terms:
 Groups    Name            Std.Dev. Corr
 fi_fishid (Intercept)      8.99
           seasonspring_I   9.43    -0.52
           seasonspring_II 11.21    -0.07  0.16
           seasonsummer    10.27    -0.29  0.55 -0.33
           seasonwinter     9.31    -0.31  0.23  0.63  0.02
 Residual                  12.50
Num. levels: fi_fishid 31

------
* For help interpreting the printed output see ?print.stanreg
* For info on the priors used see ?prior_summary.stanreg
```
`summary(m6)`

```
Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + fi_species + (1 + season | fi_fishid)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
                                                   mean   sd    10%   50%   90%
(Intercept)                                       24.7    2.6  21.3  24.6  28.1
fi_speciespikeperch                               -3.6    4.1  -8.8  -3.5   1.6
fi_specieswels                                     4.4    3.2   0.3   4.3   8.5
b[(Intercept) fi_fishid:T449202_1]                -1.3    3.4  -5.7  -1.3   2.9
b[seasonspring_I fi_fishid:T449202_1]             13.8    2.2  11.1  13.8  16.6
b[seasonspring_II fi_fishid:T449202_1]            10.1    2.9   6.4  10.1  13.8
b[seasonsummer fi_fishid:T449202_1]               -2.4    1.8  -4.7  -2.4   0.0
b[seasonwinter fi_fishid:T449202_1]               -1.7    1.9  -4.1  -1.7   0.7
b[(Intercept) fi_fishid:T449203_1]                16.6    2.9  12.9  16.6  20.2
b[seasonspring_I fi_fishid:T449203_1]             -7.4    2.1 -10.1  -7.3  -4.7
b[seasonspring_II fi_fishid:T449203_1]             5.2    3.0   1.4   5.1   9.0
b[seasonsummer fi_fishid:T449203_1]              -15.8    2.0 -18.4 -15.8 -13.3
b[seasonwinter fi_fishid:T449203_1]              -12.5    1.9 -15.0 -12.4 -10.1
b[(Intercept) fi_fishid:T449204_1]                -1.4    7.4 -10.8  -1.3   7.9
b[seasonspring_I fi_fishid:T449204_1]             -4.3    7.6 -13.7  -4.2   5.5
b[seasonspring_II fi_fishid:T449204_1]            -2.6    9.3 -14.3  -2.7   9.0
b[seasonsummer fi_fishid:T449204_1]                0.3    7.5  -9.3   0.3  10.0
b[seasonwinter fi_fishid:T449204_1]               -0.4    8.9 -11.7  -0.5  10.7
b[(Intercept) fi_fishid:T449207_1]                -8.3    2.9 -11.9  -8.2  -4.6
b[seasonspring_I fi_fishid:T449207_1]             -4.3    2.3  -7.3  -4.4  -1.3
b[seasonspring_II fi_fishid:T449207_1]            14.3    2.7  10.9  14.4  17.8
b[seasonsummer fi_fishid:T449207_1]               -5.7    1.8  -8.0  -5.7  -3.3
b[seasonwinter fi_fishid:T449207_1]               14.2    1.8  11.8  14.2  16.6
b[(Intercept) fi_fishid:T449208_1]               -10.4    3.5 -14.9 -10.4  -6.0
b[seasonspring_I fi_fishid:T449208_1]             -0.4    2.1  -3.1  -0.4   2.3
b[seasonspring_II fi_fishid:T449208_1]            -6.3    2.9 -10.0  -6.2  -2.6
b[seasonsummer fi_fishid:T449208_1]               10.0    1.8   7.7  10.0  12.3
b[seasonwinter fi_fishid:T449208_1]               -1.6    1.9  -3.9  -1.6   0.8
b[(Intercept) fi_fishid:T449209_1]                -4.6    2.9  -8.4  -4.6  -0.8
b[seasonspring_I fi_fishid:T449209_1]              7.8    2.1   5.1   7.8  10.5
b[seasonspring_II fi_fishid:T449209_1]            18.2    3.0  14.3  18.2  21.9
b[seasonsummer fi_fishid:T449209_1]                4.2    1.8   1.8   4.1   6.6
b[seasonwinter fi_fishid:T449209_1]               12.6    1.8  10.2  12.6  15.0
b[(Intercept) fi_fishid:T449213_1]                 1.0    3.4  -3.4   1.1   5.2
b[seasonspring_I fi_fishid:T449213_1]             16.0    2.2  13.2  15.9  18.7
b[seasonspring_II fi_fishid:T449213_1]             0.9    2.9  -2.8   0.8   4.7
b[seasonsummer fi_fishid:T449213_1]               24.3    1.8  22.0  24.3  26.6
b[seasonwinter fi_fishid:T449213_1]                7.0    1.8   4.7   7.1   9.4
b[(Intercept) fi_fishid:T449215_1]                 0.9    4.3  -4.5   0.9   6.3
b[seasonspring_I fi_fishid:T449215_1]             -0.7    3.5  -5.1  -0.8   3.9
b[seasonspring_II fi_fishid:T449215_1]           -12.9   10.0 -25.6 -13.0  -0.2
b[seasonsummer fi_fishid:T449215_1]               19.2    3.4  14.9  19.2  23.6
b[seasonwinter fi_fishid:T449215_1]               -3.1    9.2 -14.9  -3.1   8.8
b[(Intercept) fi_fishid:T449268_1]                 3.5    2.4   0.5   3.6   6.6
b[seasonspring_I fi_fishid:T449268_1]             -1.4    2.1  -4.0  -1.4   1.2
b[seasonspring_II fi_fishid:T449268_1]             0.9    2.7  -2.6   0.9   4.4
b[seasonsummer fi_fishid:T449268_1]                6.2    1.8   4.0   6.2   8.5
b[seasonwinter fi_fishid:T449268_1]                3.7    1.8   1.4   3.7   6.0
b[(Intercept) fi_fishid:T449269_1]                -2.4    2.4  -5.4  -2.4   0.7
b[seasonspring_I fi_fishid:T449269_1]             14.7    2.1  12.0  14.7  17.5
b[seasonspring_II fi_fishid:T449269_1]            -3.3    7.0 -12.0  -3.4   5.4
b[seasonsummer fi_fishid:T449269_1]               15.0    1.8  12.8  15.0  17.4
b[seasonwinter fi_fishid:T449269_1]               -1.9    1.9  -4.3  -1.9   0.5
b[(Intercept) fi_fishid:T449270_2]                 7.1    2.9   3.4   7.1  10.8
b[seasonspring_I fi_fishid:T449270_2]             -5.5    7.1 -14.5  -5.7   3.6
b[seasonspring_II fi_fishid:T449270_2]            11.2    8.1   0.9  11.5  21.1
b[seasonsummer fi_fishid:T449270_2]               -9.2    2.2 -11.9  -9.2  -6.4
b[seasonwinter fi_fishid:T449270_2]                8.7    1.9   6.2   8.7  11.2
b[(Intercept) fi_fishid:T449271_1]                 6.0    2.9   2.3   6.0   9.6
b[seasonspring_I fi_fishid:T449271_1]             -3.2    2.0  -5.8  -3.2  -0.6
b[seasonspring_II fi_fishid:T449271_1]            -1.4    6.6  -9.2  -1.6   6.5
b[seasonsummer fi_fishid:T449271_1]               -8.3    1.8 -10.6  -8.3  -5.9
b[seasonwinter fi_fishid:T449271_1]               -8.2    2.2 -11.1  -8.1  -5.3
b[(Intercept) fi_fishid:T449272_1]                13.1    2.9   9.4  13.1  16.7
b[seasonspring_I fi_fishid:T449272_1]             -5.4    2.1  -8.1  -5.4  -2.7
b[seasonspring_II fi_fishid:T449272_1]            -5.2    7.3 -14.2  -5.4   3.8
b[seasonsummer fi_fishid:T449272_1]                4.4    1.8   2.0   4.4   6.7
b[seasonwinter fi_fishid:T449272_1]               -4.5    2.0  -7.1  -4.4  -1.9
b[(Intercept) fi_fishid:T449274_1]                 5.5    2.4   2.5   5.5   8.6
b[seasonspring_I fi_fishid:T449274_1]              1.9    2.2  -0.9   1.9   4.7
b[seasonspring_II fi_fishid:T449274_1]             3.6    2.9  -0.2   3.6   7.3
b[seasonsummer fi_fishid:T449274_1]                0.8    1.8  -1.5   0.8   3.1
b[seasonwinter fi_fishid:T449274_1]                1.7    1.8  -0.6   1.7   4.1
b[(Intercept) fi_fishid:T449275_1]                 4.7    2.4   1.6   4.7   7.8
b[seasonspring_I fi_fishid:T449275_1]              6.4    2.1   3.6   6.4   9.2
b[seasonspring_II fi_fishid:T449275_1]            -0.9    2.9  -4.6  -0.8   2.8
b[seasonsummer fi_fishid:T449275_1]                1.4    1.8  -0.8   1.4   3.7
b[seasonwinter fi_fishid:T449275_1]               -2.5    1.8  -4.8  -2.5  -0.2
b[(Intercept) fi_fishid:T449276_1]                 5.4    2.4   2.3   5.4   8.4
b[seasonspring_I fi_fishid:T449276_1]             -8.8    2.0 -11.4  -8.8  -6.2
b[seasonspring_II fi_fishid:T449276_1]            -1.7    2.7  -5.1  -1.7   1.7
b[seasonsummer fi_fishid:T449276_1]               -5.5    1.7  -7.8  -5.5  -3.3
b[seasonwinter fi_fishid:T449276_1]               -4.8    1.8  -7.1  -4.8  -2.6
b[(Intercept) fi_fishid:T449277_1]                 0.8    2.9  -2.9   0.8   4.5
b[seasonspring_I fi_fishid:T449277_1]             -0.8    2.1  -3.4  -0.7   1.8
b[seasonspring_II fi_fishid:T449277_1]            15.2    3.9  10.4  15.1  20.2
b[seasonsummer fi_fishid:T449277_1]               -4.9    1.9  -7.2  -4.9  -2.5
b[seasonwinter fi_fishid:T449277_1]               10.2    1.8   7.9  10.2  12.6
b[(Intercept) fi_fishid:T449278_1]               -11.6    2.4 -14.7 -11.6  -8.5
b[seasonspring_I fi_fishid:T449278_1]              9.6    2.1   6.9   9.6  12.3
b[seasonspring_II fi_fishid:T449278_1]           -12.9    9.0 -23.9 -13.4  -1.2
b[seasonsummer fi_fishid:T449278_1]                7.3    1.9   4.9   7.3   9.7
b[seasonwinter fi_fishid:T449278_1]              -13.8    2.0 -16.4 -13.7 -11.2
b[(Intercept) fi_fishid:T449280_1]               -15.8    2.4 -19.0 -15.8 -12.7
b[seasonspring_I fi_fishid:T449280_1]             21.9    2.2  19.1  21.9  24.7
b[seasonspring_II fi_fishid:T449280_1]            25.9    9.7  13.8  26.2  37.6
b[seasonsummer fi_fishid:T449280_1]                3.4    2.0   0.9   3.4   6.0
b[seasonwinter fi_fishid:T449280_1]               26.8    1.9  24.3  26.8  29.2
b[(Intercept) fi_fishid:T449282_1]               -20.3    2.6 -23.7 -20.2 -17.0
b[seasonspring_I fi_fishid:T449282_1]             14.0    2.3  11.1  14.0  17.0
b[seasonspring_II fi_fishid:T449282_1]             7.0   11.1  -7.5   7.4  21.0
b[seasonsummer fi_fishid:T449282_1]                2.1    2.0  -0.5   2.1   4.7
b[seasonwinter fi_fishid:T449282_1]                7.9    9.4  -4.2   8.1  19.5
b[(Intercept) fi_fishid:T449284_1]                -5.2    2.4  -8.3  -5.1  -2.1
b[seasonspring_I fi_fishid:T449284_1]              2.1    2.1  -0.6   2.1   4.8
b[seasonspring_II fi_fishid:T449284_1]             5.9    3.1   1.9   5.8   9.9
b[seasonsummer fi_fishid:T449284_1]               -6.7    1.8  -9.1  -6.7  -4.5
b[seasonwinter fi_fishid:T449284_1]                0.0    1.9  -2.4   0.0   2.4
b[(Intercept) fi_fishid:T449285_1]               -13.9    3.2 -18.0 -14.0  -9.7
b[seasonspring_I fi_fishid:T449285_1]             11.0    2.7   7.6  11.0  14.5
b[seasonspring_II fi_fishid:T449285_1]             1.0   10.1 -11.7   1.1  13.8
b[seasonsummer fi_fishid:T449285_1]                9.0    2.4   6.0   9.0  12.1
b[seasonwinter fi_fishid:T449285_1]                4.6    9.0  -7.0   4.7  16.0
b[(Intercept) fi_fishid:T449286_1]                -1.4    2.4  -4.5  -1.4   1.6
b[seasonspring_I fi_fishid:T449286_1]              0.6    2.2  -2.2   0.6   3.4
b[seasonspring_II fi_fishid:T449286_1]            -0.3    6.0  -7.4  -0.3   7.1
b[seasonsummer fi_fishid:T449286_1]               -2.0    1.9  -4.4  -2.0   0.4
b[seasonwinter fi_fishid:T449286_1]               -1.8    2.1  -4.5  -1.8   0.8
b[(Intercept) fi_fishid:T449287_1]                -3.1    2.9  -6.8  -3.1   0.6
b[seasonspring_I fi_fishid:T449287_1]             -3.4    2.1  -6.1  -3.4  -0.7
b[seasonspring_II fi_fishid:T449287_1]            -4.2    6.7 -12.3  -4.3   4.2
b[seasonsummer fi_fishid:T449287_1]               -4.2    1.8  -6.5  -4.2  -1.9
b[seasonwinter fi_fishid:T449287_1]               -5.9    2.3  -8.9  -5.9  -3.1
b[(Intercept) fi_fishid:T449288_1]                 2.8    2.4  -0.2   2.8   5.9
b[seasonspring_I fi_fishid:T449288_1]              5.5    2.1   2.8   5.6   8.2
b[seasonspring_II fi_fishid:T449288_1]             7.9    6.7  -0.5   8.0  15.9
b[seasonsummer fi_fishid:T449288_1]               -4.1    1.8  -6.4  -4.2  -1.9
b[seasonwinter fi_fishid:T449288_1]                2.9    3.0  -0.9   2.9   6.7
b[(Intercept) fi_fishid:T449310_1]                 7.7    3.4   3.4   7.7  12.0
b[seasonspring_I fi_fishid:T449310_1]            -14.2    2.2 -17.0 -14.2 -11.5
b[seasonspring_II fi_fishid:T449310_1]             2.1    7.2  -7.2   2.1  11.2
b[seasonsummer fi_fishid:T449310_1]               -9.8    1.8 -12.0  -9.8  -7.5
b[seasonwinter fi_fishid:T449310_1]                1.8    1.9  -0.6   1.8   4.3
b[(Intercept) fi_fishid:T449313_1]                -7.0    3.4 -11.4  -6.9  -2.9
b[seasonspring_I fi_fishid:T449313_1]             15.5    2.2  12.8  15.5  18.3
b[seasonspring_II fi_fishid:T449313_1]           -12.4    8.3 -22.4 -12.7  -1.9
b[seasonsummer fi_fishid:T449313_1]               21.8    1.8  19.5  21.8  24.0
b[seasonwinter fi_fishid:T449313_1]               -8.2    2.1 -10.8  -8.1  -5.5
b[(Intercept) fi_fishid:T449314_1]                -2.0    2.4  -5.0  -2.0   1.0
b[seasonspring_I fi_fishid:T449314_1]              1.4    2.1  -1.4   1.4   4.0
b[seasonspring_II fi_fishid:T449314_1]             8.4    2.9   4.8   8.4  12.0
b[seasonsummer fi_fishid:T449314_1]               -8.3    1.8 -10.6  -8.3  -5.9
b[seasonwinter fi_fishid:T449314_1]                5.2    1.8   2.8   5.2   7.6
b[(Intercept) fi_fishid:T449317_1]                -4.2    4.1  -9.4  -4.2   1.2
b[seasonspring_I fi_fishid:T449317_1]              7.5    3.2   3.4   7.6  11.6
b[seasonspring_II fi_fishid:T449317_1]             3.9    9.5  -8.4   4.0  15.4
b[seasonsummer fi_fishid:T449317_1]                1.0    3.0  -2.9   1.0   4.9
b[seasonwinter fi_fishid:T449317_1]                2.2    8.6  -8.6   2.3  13.1
b[(Intercept) fi_fishid:T449318_1]                 1.4    2.4  -1.6   1.4   4.5
b[seasonspring_I fi_fishid:T449318_1]             -0.5    2.1  -3.1  -0.5   2.2
b[seasonspring_II fi_fishid:T449318_1]            -1.4    5.9  -8.4  -1.4   5.7
b[seasonsummer fi_fishid:T449318_1]               -1.0    1.8  -3.3  -1.0   1.4
b[seasonwinter fi_fishid:T449318_1]               -2.6    2.1  -5.3  -2.6   0.0
b[(Intercept) fi_fishid:T449319_1]                 3.6    2.4   0.5   3.6   6.7
b[seasonspring_I fi_fishid:T449319_1]              0.5    2.2  -2.3   0.5   3.3
b[seasonspring_II fi_fishid:T449319_1]            -2.9    5.0  -9.1  -3.2   3.8
b[seasonsummer fi_fishid:T449319_1]                5.0    1.8   2.7   5.0   7.4
b[seasonwinter fi_fishid:T449319_1]               -5.9    1.8  -8.2  -5.9  -3.5
sigma                                             12.5    0.1  12.4  12.5  12.6
Sigma[fi_fishid:(Intercept),(Intercept)]          80.9   22.3  56.3  77.4 109.9
Sigma[fi_fishid:seasonspring_I,(Intercept)]      -44.2   18.2 -67.7 -42.3 -23.2
Sigma[fi_fishid:seasonspring_II,(Intercept)]      -7.5   24.4 -38.6  -7.4  23.4
Sigma[fi_fishid:seasonsummer,(Intercept)]        -26.8   17.9 -49.5 -25.5  -6.0
Sigma[fi_fishid:seasonwinter,(Intercept)]        -25.7   16.4 -46.5 -25.1  -5.8
Sigma[fi_fishid:seasonspring_I,seasonspring_I]    88.9   24.2  62.0  85.8 120.5
Sigma[fi_fishid:seasonspring_II,seasonspring_I]   17.1   23.7 -12.5  17.0  46.4
Sigma[fi_fishid:seasonsummer,seasonspring_I]      52.9   18.8  30.6  51.3  77.0
Sigma[fi_fishid:seasonwinter,seasonspring_I]      19.9   16.3   0.1  19.0  40.2
Sigma[fi_fishid:seasonspring_II,seasonspring_II] 125.7   39.4  82.6 119.2 176.9
Sigma[fi_fishid:seasonsummer,seasonspring_II]    -37.7   27.5 -72.5 -34.9  -6.3
Sigma[fi_fishid:seasonwinter,seasonspring_II]     66.3   21.0  41.0  65.5  92.4
Sigma[fi_fishid:seasonsummer,seasonsummer]       105.4   25.1  76.9 102.1 138.6
Sigma[fi_fishid:seasonwinter,seasonsummer]         2.3   18.1 -20.1   2.0  24.3
Sigma[fi_fishid:seasonwinter,seasonwinter]        86.7   22.4  61.7  83.6 116.2

Fit Diagnostics:
           mean   sd   10%   50%   90%
mean_PPD 26.9    0.2 26.7  26.9  27.2

The mean_ppd is the sample average posterior predictive distribution of the outcome variable (for details see help('summary.stanreg')).

MCMC diagnostics
                                                 mcse Rhat n_eff
(Intercept)                                      0.1  1.0   770
fi_speciespikeperch                              0.1  1.0   924
fi_specieswels                                   0.1  1.0   734
b[(Intercept) fi_fishid:T449202_1]               0.1  1.0   994
b[seasonspring_I fi_fishid:T449202_1]            0.0  1.0  6106
b[seasonspring_II fi_fishid:T449202_1]           0.0  1.0  4164
b[seasonsummer fi_fishid:T449202_1]              0.0  1.0  5003
b[seasonwinter fi_fishid:T449202_1]              0.0  1.0  4775
b[(Intercept) fi_fishid:T449203_1]               0.1  1.0   893
b[seasonspring_I fi_fishid:T449203_1]            0.0  1.0  4552
b[seasonspring_II fi_fishid:T449203_1]           0.0  1.0  3841
b[seasonsummer fi_fishid:T449203_1]              0.0  1.0  3708
b[seasonwinter fi_fishid:T449203_1]              0.0  1.0  3546
b[(Intercept) fi_fishid:T449204_1]               0.1  1.0  3876
b[seasonspring_I fi_fishid:T449204_1]            0.1  1.0  3604
b[seasonspring_II fi_fishid:T449204_1]           0.1  1.0  4652
b[seasonsummer fi_fishid:T449204_1]              0.1  1.0  3432
b[seasonwinter fi_fishid:T449204_1]              0.1  1.0  5847
b[(Intercept) fi_fishid:T449207_1]               0.1  1.0   923
b[seasonspring_I fi_fishid:T449207_1]            0.0  1.0  5060
b[seasonspring_II fi_fishid:T449207_1]           0.0  1.0  5127
b[seasonsummer fi_fishid:T449207_1]              0.0  1.0  4419
b[seasonwinter fi_fishid:T449207_1]              0.0  1.0  4393
b[(Intercept) fi_fishid:T449208_1]               0.1  1.0  1009
b[seasonspring_I fi_fishid:T449208_1]            0.0  1.0  4303
b[seasonspring_II fi_fishid:T449208_1]           0.1  1.0  2925
b[seasonsummer fi_fishid:T449208_1]              0.0  1.0  4122
b[seasonwinter fi_fishid:T449208_1]              0.0  1.0  4081
b[(Intercept) fi_fishid:T449209_1]               0.1  1.0   889
b[seasonspring_I fi_fishid:T449209_1]            0.0  1.0  5577
b[seasonspring_II fi_fishid:T449209_1]           0.0  1.0  3603
b[seasonsummer fi_fishid:T449209_1]              0.0  1.0  4107
b[seasonwinter fi_fishid:T449209_1]              0.0  1.0  4335
b[(Intercept) fi_fishid:T449213_1]               0.1  1.0  1035
b[seasonspring_I fi_fishid:T449213_1]            0.0  1.0  5494
b[seasonspring_II fi_fishid:T449213_1]           0.0  1.0  3831
b[seasonsummer fi_fishid:T449213_1]              0.0  1.0  4287
b[seasonwinter fi_fishid:T449213_1]              0.0  1.0  3538
b[(Intercept) fi_fishid:T449215_1]               0.1  1.0  1640
b[seasonspring_I fi_fishid:T449215_1]            0.0  1.0  6487
b[seasonspring_II fi_fishid:T449215_1]           0.2  1.0  2638
b[seasonsummer fi_fishid:T449215_1]              0.0  1.0  5640
b[seasonwinter fi_fishid:T449215_1]              0.2  1.0  3748
b[(Intercept) fi_fishid:T449268_1]               0.1  1.0   825
b[seasonspring_I fi_fishid:T449268_1]            0.0  1.0  6265
b[seasonspring_II fi_fishid:T449268_1]           0.0  1.0  4995
b[seasonsummer fi_fishid:T449268_1]              0.0  1.0  4541
b[seasonwinter fi_fishid:T449268_1]              0.0  1.0  4955
b[(Intercept) fi_fishid:T449269_1]               0.1  1.0   800
b[seasonspring_I fi_fishid:T449269_1]            0.0  1.0  5256
b[seasonspring_II fi_fishid:T449269_1]           0.1  1.0  2514
b[seasonsummer fi_fishid:T449269_1]              0.0  1.0  4661
b[seasonwinter fi_fishid:T449269_1]              0.0  1.0  4932
b[(Intercept) fi_fishid:T449270_2]               0.1  1.0   914
b[seasonspring_I fi_fishid:T449270_2]            0.1  1.0  4183
b[seasonspring_II fi_fishid:T449270_2]           0.2  1.0  1744
b[seasonsummer fi_fishid:T449270_2]              0.0  1.0  4394
b[seasonwinter fi_fishid:T449270_2]              0.0  1.0  4375
b[(Intercept) fi_fishid:T449271_1]               0.1  1.0   942
b[seasonspring_I fi_fishid:T449271_1]            0.0  1.0  5818
b[seasonspring_II fi_fishid:T449271_1]           0.1  1.0  2744
b[seasonsummer fi_fishid:T449271_1]              0.0  1.0  4668
b[seasonwinter fi_fishid:T449271_1]              0.0  1.0  4441
b[(Intercept) fi_fishid:T449272_1]               0.1  1.0   902
b[seasonspring_I fi_fishid:T449272_1]            0.0  1.0  5793
b[seasonspring_II fi_fishid:T449272_1]           0.2  1.0  1999
b[seasonsummer fi_fishid:T449272_1]              0.0  1.0  3799
b[seasonwinter fi_fishid:T449272_1]              0.0  1.0  3997
b[(Intercept) fi_fishid:T449274_1]               0.1  1.0   864
b[seasonspring_I fi_fishid:T449274_1]            0.0  1.0  6055
b[seasonspring_II fi_fishid:T449274_1]           0.0  1.0  4396
b[seasonsummer fi_fishid:T449274_1]              0.0  1.0  5165
b[seasonwinter fi_fishid:T449274_1]              0.0  1.0  5175
b[(Intercept) fi_fishid:T449275_1]               0.1  1.0   846
b[seasonspring_I fi_fishid:T449275_1]            0.0  1.0  6715
b[seasonspring_II fi_fishid:T449275_1]           0.0  1.0  4338
b[seasonsummer fi_fishid:T449275_1]              0.0  1.0  4482
b[seasonwinter fi_fishid:T449275_1]              0.0  1.0  4642
b[(Intercept) fi_fishid:T449276_1]               0.1  1.0   838
b[seasonspring_I fi_fishid:T449276_1]            0.0  1.0  6819
b[seasonspring_II fi_fishid:T449276_1]           0.0  1.0  3981
b[seasonsummer fi_fishid:T449276_1]              0.0  1.0  5345
b[seasonwinter fi_fishid:T449276_1]              0.0  1.0  4713
b[(Intercept) fi_fishid:T449277_1]               0.1  1.0   926
b[seasonspring_I fi_fishid:T449277_1]            0.0  1.0  6767
b[seasonspring_II fi_fishid:T449277_1]           0.1  1.0  4738
b[seasonsummer fi_fishid:T449277_1]              0.0  1.0  4363
b[seasonwinter fi_fishid:T449277_1]              0.0  1.0  4643
b[(Intercept) fi_fishid:T449278_1]               0.1  1.0   853
b[seasonspring_I fi_fishid:T449278_1]            0.0  1.0  5218
b[seasonspring_II fi_fishid:T449278_1]           0.3  1.0  1177
b[seasonsummer fi_fishid:T449278_1]              0.0  1.0  4333
b[seasonwinter fi_fishid:T449278_1]              0.0  1.0  3956
b[(Intercept) fi_fishid:T449280_1]               0.1  1.0   817
b[seasonspring_I fi_fishid:T449280_1]            0.0  1.0  4215
b[seasonspring_II fi_fishid:T449280_1]           0.2  1.0  1527
b[seasonsummer fi_fishid:T449280_1]              0.0  1.0  4384
b[seasonwinter fi_fishid:T449280_1]              0.0  1.0  4370
b[(Intercept) fi_fishid:T449282_1]               0.1  1.0   866
b[seasonspring_I fi_fishid:T449282_1]            0.0  1.0  5026
b[seasonspring_II fi_fishid:T449282_1]           0.2  1.0  2204
b[seasonsummer fi_fishid:T449282_1]              0.0  1.0  4295
b[seasonwinter fi_fishid:T449282_1]              0.1  1.0  4672
b[(Intercept) fi_fishid:T449284_1]               0.1  1.0   802
b[seasonspring_I fi_fishid:T449284_1]            0.0  1.0  7050
b[seasonspring_II fi_fishid:T449284_1]           0.1  1.0  3777
b[seasonsummer fi_fishid:T449284_1]              0.0  1.0  4874
b[seasonwinter fi_fishid:T449284_1]              0.0  1.0  5080
b[(Intercept) fi_fishid:T449285_1]               0.1  1.0  1073
b[seasonspring_I fi_fishid:T449285_1]            0.0  1.0  5410
b[seasonspring_II fi_fishid:T449285_1]           0.2  1.0  3161
b[seasonsummer fi_fishid:T449285_1]              0.0  1.0  4699
b[seasonwinter fi_fishid:T449285_1]              0.1  1.0  4777
b[(Intercept) fi_fishid:T449286_1]               0.1  1.0   823
b[seasonspring_I fi_fishid:T449286_1]            0.0  1.0  7149
b[seasonspring_II fi_fishid:T449286_1]           0.1  1.0  3018
b[seasonsummer fi_fishid:T449286_1]              0.0  1.0  5065
b[seasonwinter fi_fishid:T449286_1]              0.0  1.0  5605
b[(Intercept) fi_fishid:T449287_1]               0.1  1.0   895
b[seasonspring_I fi_fishid:T449287_1]            0.0  1.0  7422
b[seasonspring_II fi_fishid:T449287_1]           0.1  1.0  2283
b[seasonsummer fi_fishid:T449287_1]              0.0  1.0  4856
b[seasonwinter fi_fishid:T449287_1]              0.0  1.0  4826
b[(Intercept) fi_fishid:T449288_1]               0.1  1.0   827
b[seasonspring_I fi_fishid:T449288_1]            0.0  1.0  6766
b[seasonspring_II fi_fishid:T449288_1]           0.1  1.0  2642
b[seasonsummer fi_fishid:T449288_1]              0.0  1.0  4877
b[seasonwinter fi_fishid:T449288_1]              0.0  1.0  4833
b[(Intercept) fi_fishid:T449310_1]               0.1  1.0  1066
b[seasonspring_I fi_fishid:T449310_1]            0.0  1.0  4738
b[seasonspring_II fi_fishid:T449310_1]           0.2  1.0  1669
b[seasonsummer fi_fishid:T449310_1]              0.0  1.0  4601
b[seasonwinter fi_fishid:T449310_1]              0.0  1.0  4335
b[(Intercept) fi_fishid:T449313_1]               0.1  1.0  1005
b[seasonspring_I fi_fishid:T449313_1]            0.0  1.0  5633
b[seasonspring_II fi_fishid:T449313_1]           0.2  1.0  1722
b[seasonsummer fi_fishid:T449313_1]              0.0  1.0  4376
b[seasonwinter fi_fishid:T449313_1]              0.0  1.0  4293
b[(Intercept) fi_fishid:T449314_1]               0.1  1.0   782
b[seasonspring_I fi_fishid:T449314_1]            0.0  1.0  5589
b[seasonspring_II fi_fishid:T449314_1]           0.0  1.0  3973
b[seasonsummer fi_fishid:T449314_1]              0.0  1.0  4267
b[seasonwinter fi_fishid:T449314_1]              0.0  1.0  4224
b[(Intercept) fi_fishid:T449317_1]               0.1  1.0  1483
b[seasonspring_I fi_fishid:T449317_1]            0.0  1.0  7225
b[seasonspring_II fi_fishid:T449317_1]           0.1  1.0  4119
b[seasonsummer fi_fishid:T449317_1]              0.0  1.0  5549
b[seasonwinter fi_fishid:T449317_1]              0.1  1.0  4831
b[(Intercept) fi_fishid:T449318_1]               0.1  1.0   798
b[seasonspring_I fi_fishid:T449318_1]            0.0  1.0  6725
b[seasonspring_II fi_fishid:T449318_1]           0.1  1.0  3114
b[seasonsummer fi_fishid:T449318_1]              0.0  1.0  5483
b[seasonwinter fi_fishid:T449318_1]              0.0  1.0  5043
b[(Intercept) fi_fishid:T449319_1]               0.1  1.0   841
b[seasonspring_I fi_fishid:T449319_1]            0.0  1.0  6831
b[seasonspring_II fi_fishid:T449319_1]           0.1  1.0  2233
b[seasonsummer fi_fishid:T449319_1]              0.0  1.0  4830
b[seasonwinter fi_fishid:T449319_1]              0.0  1.0  5051
sigma                                            0.0  1.0  8860
Sigma[fi_fishid:(Intercept),(Intercept)]         0.6  1.0  1588
Sigma[fi_fishid:seasonspring_I,(Intercept)]      0.5  1.0  1202
Sigma[fi_fishid:seasonspring_II,(Intercept)]     0.8  1.0   930
Sigma[fi_fishid:seasonsummer,(Intercept)]        0.5  1.0  1414
Sigma[fi_fishid:seasonwinter,(Intercept)]        0.4  1.0  1962
Sigma[fi_fishid:seasonspring_I,seasonspring_I]   0.6  1.0  1405
Sigma[fi_fishid:seasonspring_II,seasonspring_I]  0.7  1.0  1155
Sigma[fi_fishid:seasonsummer,seasonspring_I]     0.4  1.0  1777
Sigma[fi_fishid:seasonwinter,seasonspring_I]     0.4  1.0  1754
Sigma[fi_fishid:seasonspring_II,seasonspring_II] 0.8  1.0  2269
Sigma[fi_fishid:seasonsummer,seasonspring_II]    0.7  1.0  1377
Sigma[fi_fishid:seasonwinter,seasonspring_II]    0.6  1.0  1048
Sigma[fi_fishid:seasonsummer,seasonsummer]       0.6  1.0  1979
Sigma[fi_fishid:seasonwinter,seasonsummer]       0.4  1.0  1961
Sigma[fi_fishid:seasonwinter,seasonwinter]       0.5  1.0  1799
mean_PPD                                         0.0  1.0  3304
log-posterior                                    0.4  1.0   944

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).
```

### Check priors used

`prior_summary(object = m6)`

```{r, echo=FALSE, eval=TRUE}

Priors for model 'm6'
------
Intercept (after predictors centered)
  Specified prior:
    ~ normal(location = 0, scale = 10)
  Adjusted prior:
    ~ normal(location = 0, scale = 155)

Coefficients
  Specified prior:
    ~ normal(location = [0,0], scale = [2.5,2.5])
  Adjusted prior:
    ~ normal(location = [0,0], scale = [38.65,38.65])

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

`summary(m6, pars = c("b[(Intercept) fi_fishid", "sigma", "Sigma[fi_fishid:(Intercept),(Intercept)]"), probs = c(0.025, 0.975), digits = 2)`

```
Model Info:
 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + fi_species + (1 + season | fi_fishid)
 algorithm:    sampling
 sample:       4000 (posterior sample size)
 priors:       see help('prior_summary')
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
                                           mean   sd     2.5%   97.5%
sigma                                     12.50   0.09  12.33  12.68
Sigma[fi_fishid:(Intercept),(Intercept)]  80.86  22.33  48.05 133.48

MCMC diagnostics
                                         mcse Rhat n_eff
sigma                                    0.00 1.00 8860
Sigma[fi_fishid:(Intercept),(Intercept)] 0.56 1.00 1588

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).
```

### Extract the posterior draws for all parameters

`sims <- as.matrix(m6)`

`dim(sims)`

```
[1] 4000   174

```

`para_name <- colnames(sims)`

`para_name`

```
  [1] "(Intercept)"                                      "fi_speciespikeperch"
  [3] "fi_specieswels"                                   "b[(Intercept) fi_fishid:T449202_1]"
  [5] "b[seasonspring_I fi_fishid:T449202_1]"            "b[seasonspring_II fi_fishid:T449202_1]"
  [7] "b[seasonsummer fi_fishid:T449202_1]"              "b[seasonwinter fi_fishid:T449202_1]"
  [9] "b[(Intercept) fi_fishid:T449203_1]"               "b[seasonspring_I fi_fishid:T449203_1]"
 [11] "b[seasonspring_II fi_fishid:T449203_1]"           "b[seasonsummer fi_fishid:T449203_1]"
 [13] "b[seasonwinter fi_fishid:T449203_1]"              "b[(Intercept) fi_fishid:T449204_1]"
 [15] "b[seasonspring_I fi_fishid:T449204_1]"            "b[seasonspring_II fi_fishid:T449204_1]"
 [17] "b[seasonsummer fi_fishid:T449204_1]"              "b[seasonwinter fi_fishid:T449204_1]"
 [19] "b[(Intercept) fi_fishid:T449207_1]"               "b[seasonspring_I fi_fishid:T449207_1]"
 [21] "b[seasonspring_II fi_fishid:T449207_1]"           "b[seasonsummer fi_fishid:T449207_1]"
 [23] "b[seasonwinter fi_fishid:T449207_1]"              "b[(Intercept) fi_fishid:T449208_1]"
 [25] "b[seasonspring_I fi_fishid:T449208_1]"            "b[seasonspring_II fi_fishid:T449208_1]"
 [27] "b[seasonsummer fi_fishid:T449208_1]"              "b[seasonwinter fi_fishid:T449208_1]"
 [29] "b[(Intercept) fi_fishid:T449209_1]"               "b[seasonspring_I fi_fishid:T449209_1]"
 [31] "b[seasonspring_II fi_fishid:T449209_1]"           "b[seasonsummer fi_fishid:T449209_1]"
 [33] "b[seasonwinter fi_fishid:T449209_1]"              "b[(Intercept) fi_fishid:T449213_1]"
 [35] "b[seasonspring_I fi_fishid:T449213_1]"            "b[seasonspring_II fi_fishid:T449213_1]"
 [37] "b[seasonsummer fi_fishid:T449213_1]"              "b[seasonwinter fi_fishid:T449213_1]"
 [39] "b[(Intercept) fi_fishid:T449215_1]"               "b[seasonspring_I fi_fishid:T449215_1]"
 [41] "b[seasonspring_II fi_fishid:T449215_1]"           "b[seasonsummer fi_fishid:T449215_1]"
 [43] "b[seasonwinter fi_fishid:T449215_1]"              "b[(Intercept) fi_fishid:T449268_1]"
 [45] "b[seasonspring_I fi_fishid:T449268_1]"            "b[seasonspring_II fi_fishid:T449268_1]"
 [47] "b[seasonsummer fi_fishid:T449268_1]"              "b[seasonwinter fi_fishid:T449268_1]"
 [49] "b[(Intercept) fi_fishid:T449269_1]"               "b[seasonspring_I fi_fishid:T449269_1]"
 [51] "b[seasonspring_II fi_fishid:T449269_1]"           "b[seasonsummer fi_fishid:T449269_1]"
 [53] "b[seasonwinter fi_fishid:T449269_1]"              "b[(Intercept) fi_fishid:T449270_2]"
 [55] "b[seasonspring_I fi_fishid:T449270_2]"            "b[seasonspring_II fi_fishid:T449270_2]"
 [57] "b[seasonsummer fi_fishid:T449270_2]"              "b[seasonwinter fi_fishid:T449270_2]"
 [59] "b[(Intercept) fi_fishid:T449271_1]"               "b[seasonspring_I fi_fishid:T449271_1]"
 [61] "b[seasonspring_II fi_fishid:T449271_1]"           "b[seasonsummer fi_fishid:T449271_1]"
 [63] "b[seasonwinter fi_fishid:T449271_1]"              "b[(Intercept) fi_fishid:T449272_1]"
 [65] "b[seasonspring_I fi_fishid:T449272_1]"            "b[seasonspring_II fi_fishid:T449272_1]"
 [67] "b[seasonsummer fi_fishid:T449272_1]"              "b[seasonwinter fi_fishid:T449272_1]"
 [69] "b[(Intercept) fi_fishid:T449274_1]"               "b[seasonspring_I fi_fishid:T449274_1]"
 [71] "b[seasonspring_II fi_fishid:T449274_1]"           "b[seasonsummer fi_fishid:T449274_1]"
 [73] "b[seasonwinter fi_fishid:T449274_1]"              "b[(Intercept) fi_fishid:T449275_1]"
 [75] "b[seasonspring_I fi_fishid:T449275_1]"            "b[seasonspring_II fi_fishid:T449275_1]"
 [77] "b[seasonsummer fi_fishid:T449275_1]"              "b[seasonwinter fi_fishid:T449275_1]"
 [79] "b[(Intercept) fi_fishid:T449276_1]"               "b[seasonspring_I fi_fishid:T449276_1]"
 [81] "b[seasonspring_II fi_fishid:T449276_1]"           "b[seasonsummer fi_fishid:T449276_1]"
 [83] "b[seasonwinter fi_fishid:T449276_1]"              "b[(Intercept) fi_fishid:T449277_1]"
 [85] "b[seasonspring_I fi_fishid:T449277_1]"            "b[seasonspring_II fi_fishid:T449277_1]"
 [87] "b[seasonsummer fi_fishid:T449277_1]"              "b[seasonwinter fi_fishid:T449277_1]"
 [89] "b[(Intercept) fi_fishid:T449278_1]"               "b[seasonspring_I fi_fishid:T449278_1]"
 [91] "b[seasonspring_II fi_fishid:T449278_1]"           "b[seasonsummer fi_fishid:T449278_1]"
 [93] "b[seasonwinter fi_fishid:T449278_1]"              "b[(Intercept) fi_fishid:T449280_1]"
 [95] "b[seasonspring_I fi_fishid:T449280_1]"            "b[seasonspring_II fi_fishid:T449280_1]"
 [97] "b[seasonsummer fi_fishid:T449280_1]"              "b[seasonwinter fi_fishid:T449280_1]"
 [99] "b[(Intercept) fi_fishid:T449282_1]"               "b[seasonspring_I fi_fishid:T449282_1]"
[101] "b[seasonspring_II fi_fishid:T449282_1]"           "b[seasonsummer fi_fishid:T449282_1]"
[103] "b[seasonwinter fi_fishid:T449282_1]"              "b[(Intercept) fi_fishid:T449284_1]"
[105] "b[seasonspring_I fi_fishid:T449284_1]"            "b[seasonspring_II fi_fishid:T449284_1]"
[107] "b[seasonsummer fi_fishid:T449284_1]"              "b[seasonwinter fi_fishid:T449284_1]"
[109] "b[(Intercept) fi_fishid:T449285_1]"               "b[seasonspring_I fi_fishid:T449285_1]"
[111] "b[seasonspring_II fi_fishid:T449285_1]"           "b[seasonsummer fi_fishid:T449285_1]"
[113] "b[seasonwinter fi_fishid:T449285_1]"              "b[(Intercept) fi_fishid:T449286_1]"
[115] "b[seasonspring_I fi_fishid:T449286_1]"            "b[seasonspring_II fi_fishid:T449286_1]"
[117] "b[seasonsummer fi_fishid:T449286_1]"              "b[seasonwinter fi_fishid:T449286_1]"
[119] "b[(Intercept) fi_fishid:T449287_1]"               "b[seasonspring_I fi_fishid:T449287_1]"
[121] "b[seasonspring_II fi_fishid:T449287_1]"           "b[seasonsummer fi_fishid:T449287_1]"
[123] "b[seasonwinter fi_fishid:T449287_1]"              "b[(Intercept) fi_fishid:T449288_1]"
[125] "b[seasonspring_I fi_fishid:T449288_1]"            "b[seasonspring_II fi_fishid:T449288_1]"
[127] "b[seasonsummer fi_fishid:T449288_1]"              "b[seasonwinter fi_fishid:T449288_1]"
[129] "b[(Intercept) fi_fishid:T449310_1]"               "b[seasonspring_I fi_fishid:T449310_1]"
[131] "b[seasonspring_II fi_fishid:T449310_1]"           "b[seasonsummer fi_fishid:T449310_1]"
[133] "b[seasonwinter fi_fishid:T449310_1]"              "b[(Intercept) fi_fishid:T449313_1]"
[135] "b[seasonspring_I fi_fishid:T449313_1]"            "b[seasonspring_II fi_fishid:T449313_1]"
[137] "b[seasonsummer fi_fishid:T449313_1]"              "b[seasonwinter fi_fishid:T449313_1]"
[139] "b[(Intercept) fi_fishid:T449314_1]"               "b[seasonspring_I fi_fishid:T449314_1]"
[141] "b[seasonspring_II fi_fishid:T449314_1]"           "b[seasonsummer fi_fishid:T449314_1]"
[143] "b[seasonwinter fi_fishid:T449314_1]"              "b[(Intercept) fi_fishid:T449317_1]"
[145] "b[seasonspring_I fi_fishid:T449317_1]"            "b[seasonspring_II fi_fishid:T449317_1]"
[147] "b[seasonsummer fi_fishid:T449317_1]"              "b[seasonwinter fi_fishid:T449317_1]"
[149] "b[(Intercept) fi_fishid:T449318_1]"               "b[seasonspring_I fi_fishid:T449318_1]"
[151] "b[seasonspring_II fi_fishid:T449318_1]"           "b[seasonsummer fi_fishid:T449318_1]"
[153] "b[seasonwinter fi_fishid:T449318_1]"              "b[(Intercept) fi_fishid:T449319_1]"
[155] "b[seasonspring_I fi_fishid:T449319_1]"            "b[seasonspring_II fi_fishid:T449319_1]"
[157] "b[seasonsummer fi_fishid:T449319_1]"              "b[seasonwinter fi_fishid:T449319_1]"
[159] "sigma"                                            "Sigma[fi_fishid:(Intercept),(Intercept)]"
[161] "Sigma[fi_fishid:seasonspring_I,(Intercept)]"      "Sigma[fi_fishid:seasonspring_II,(Intercept)]"
[163] "Sigma[fi_fishid:seasonsummer,(Intercept)]"        "Sigma[fi_fishid:seasonwinter,(Intercept)]"
[165] "Sigma[fi_fishid:seasonspring_I,seasonspring_I]"   "Sigma[fi_fishid:seasonspring_II,seasonspring_I]"
[167] "Sigma[fi_fishid:seasonsummer,seasonspring_I]"     "Sigma[fi_fishid:seasonwinter,seasonspring_I]"
[169] "Sigma[fi_fishid:seasonspring_II,seasonspring_II]" "Sigma[fi_fishid:seasonsummer,seasonspring_II]"
[171] "Sigma[fi_fishid:seasonwinter,seasonspring_II]"    "Sigma[fi_fishid:seasonsummer,seasonsummer]"
[173] "Sigma[fi_fishid:seasonwinter,seasonsummer]"       "Sigma[fi_fishid:seasonwinter,seasonwinter]"

```

- Obtain fi_species-level varying intercept a_j

 - draws for overall mean

`mu_a_sims <- as.matrix(m6, pars = "(Intercept)")`

 - draws for 3 fi_species-level error

`fish_err <- as.matrix(m6,regex_pars = "b\\[\\(Intercept\\) fi_fishid\\:")`

 - draws for 3 species' varying intercepts

`fish_inter <- as.numeric(mu_a_sims) + fish_err`

- Obtain sigma_y and sigma_alpha^2

 -draws for sigma_y

`signma_y <- as.matrix(m6, pars = "sigma")`

 - draws for sigma_alpha^2

`sigma_alpha <- as.matrix(m6, pars = "Sigma[fi_fishid:(Intercept),(Intercept)]")`


### Obtaining means, SD, medians and 95% credible intervals of varying intercepts

In *fish_inter*, we have saved 4,000 posterior draws (from all 4 chains) for the varying intercepts αj of the 30 fishes. For example, the first column of the 4,000 by 30 matrix is a vector of 4,000 posterior simulation draws for the first fish's (*fi_species*:T449202_1) varying intercept α1. One quantitative way to summarize the posterior probability distribution of these 4,000 estimates for α1 is to examine their quantiles.

- Posterior mean of each alpha

`a_mean <- apply(X = fish_inter, MARGIN = 2, FUN = mean)`

`a_mean'

```
     b[(Intercept) fi_fishid:T449202_1] b[(Intercept) fi_fishid:T449203_1] b[(Intercept) fi_fishid:T449204_1]
                         23.357074                          41.278970                          23.303850
b[(Intercept) fi_fishid:T449207_1] b[(Intercept) fi_fishid:T449208_1] b[(Intercept) fi_fishid:T449209_1]
                         16.420296                          14.257421                          20.090490
b[(Intercept) fi_fishid:T449213_1] b[(Intercept) fi_fishid:T449215_1] b[(Intercept) fi_fishid:T449268_1]
                         25.717262                          25.553383                          28.207753
b[(Intercept) fi_fishid:T449269_1] b[(Intercept) fi_fishid:T449270_2] b[(Intercept) fi_fishid:T449271_1]
                         22.291375                          31.797542                          30.655808
b[(Intercept) fi_fishid:T449272_1] b[(Intercept) fi_fishid:T449274_1] b[(Intercept) fi_fishid:T449275_1]
                         37.740055                          30.226853                          29.340619
b[(Intercept) fi_fishid:T449276_1] b[(Intercept) fi_fishid:T449277_1] b[(Intercept) fi_fishid:T449278_1]
                         30.054926                          25.507544                          13.073218
b[(Intercept) fi_fishid:T449280_1] b[(Intercept) fi_fishid:T449282_1] b[(Intercept) fi_fishid:T449284_1]
                          8.880983                           4.366196                          19.475980
b[(Intercept) fi_fishid:T449285_1] b[(Intercept) fi_fishid:T449286_1] b[(Intercept) fi_fishid:T449287_1]
                         10.787338                          23.232288                          21.593162
b[(Intercept) fi_fishid:T449288_1] b[(Intercept) fi_fishid:T449310_1] b[(Intercept) fi_fishid:T449313_1]
                         27.508822                          32.428542                          17.690511
b[(Intercept) fi_fishid:T449314_1] b[(Intercept) fi_fishid:T449317_1] b[(Intercept) fi_fishid:T449318_1]
                         22.668341                          20.510519                          26.087980
b[(Intercept) fi_fishid:T449319_1]
                         28.285563
```

- Posterior SD of each alpha

`a_sd <- apply(X = fish_inter, MARGIN = 2, FUN = sd)`

`a_sd`

b[(Intercept) fi_fishid:T449202_1] b[(Intercept) fi_fishid:T449203_1] b[(Intercept) fi_fishid:T449204_1]
                          4.229106                           1.324974                           7.464405
b[(Intercept) fi_fishid:T449207_1] b[(Intercept) fi_fishid:T449208_1] b[(Intercept) fi_fishid:T449209_1]
                          1.288661                           4.250297                           1.326219
b[(Intercept) fi_fishid:T449213_1] b[(Intercept) fi_fishid:T449215_1] b[(Intercept) fi_fishid:T449268_1]
                          4.214109                           4.984999                           3.430869
b[(Intercept) fi_fishid:T449269_1] b[(Intercept) fi_fishid:T449270_2] b[(Intercept) fi_fishid:T449271_1]
                          3.383524                           1.316491                           1.306497
b[(Intercept) fi_fishid:T449272_1] b[(Intercept) fi_fishid:T449274_1] b[(Intercept) fi_fishid:T449275_1]
                          1.279591                           3.429134                           3.379411
b[(Intercept) fi_fishid:T449276_1] b[(Intercept) fi_fishid:T449277_1] b[(Intercept) fi_fishid:T449278_1]
                          3.381010                           1.307149                           3.448058
b[(Intercept) fi_fishid:T449280_1] b[(Intercept) fi_fishid:T449282_1] b[(Intercept) fi_fishid:T449284_1]
                          3.444906                           3.570062                           3.394476
b[(Intercept) fi_fishid:T449285_1] b[(Intercept) fi_fishid:T449286_1] b[(Intercept) fi_fishid:T449287_1]
                          2.036999                           3.413537                           1.283027
b[(Intercept) fi_fishid:T449288_1] b[(Intercept) fi_fishid:T449310_1] b[(Intercept) fi_fishid:T449313_1]
                          3.404037                           4.210054                           4.252608
b[(Intercept) fi_fishid:T449314_1] b[(Intercept) fi_fishid:T449317_1] b[(Intercept) fi_fishid:T449318_1]
                          3.403647                           4.841234                           3.410574
b[(Intercept) fi_fishid:T449319_1]
                          3.429829
```

- Posterior median and 95% credible interval

`a_quant <- apply(X = fish_inter, MARGIN = 2, FUN = quantile, probs = c(0.025, 0.50, 0.975))`

`a_quant <- data.frame(t(a_quant))`

`a_quant`

```
                                       X2.5.      X50.   X97.5.
b[(Intercept) fi_fishid:T449202_1] 15.059714 23.294820 31.71013
b[(Intercept) fi_fishid:T449203_1] 38.745085 41.265211 43.88082
b[(Intercept) fi_fishid:T449204_1]  8.363744 23.418342 38.10366
b[(Intercept) fi_fishid:T449207_1] 13.910712 16.435921 18.94063
b[(Intercept) fi_fishid:T449208_1]  6.117938 14.181613 22.62849
b[(Intercept) fi_fishid:T449209_1] 17.481025 20.120174 22.61445
b[(Intercept) fi_fishid:T449213_1] 17.646674 25.686695 34.19274
b[(Intercept) fi_fishid:T449215_1] 15.986957 25.534825 35.31668
b[(Intercept) fi_fishid:T449268_1] 21.647827 28.132811 34.93453
b[(Intercept) fi_fishid:T449269_1] 15.708730 22.284004 29.12247
b[(Intercept) fi_fishid:T449270_2] 29.151485 31.802406 34.31239
b[(Intercept) fi_fishid:T449271_1] 28.113859 30.646824 33.23973
b[(Intercept) fi_fishid:T449272_1] 35.298228 37.730605 40.30840
b[(Intercept) fi_fishid:T449274_1] 23.601833 30.277627 37.04984
b[(Intercept) fi_fishid:T449275_1] 22.839940 29.357405 35.91572
b[(Intercept) fi_fishid:T449276_1] 23.637560 30.055014 36.73707
b[(Intercept) fi_fishid:T449277_1] 22.981026 25.541950 28.04313
b[(Intercept) fi_fishid:T449278_1]  6.454197 13.039758 19.78347
b[(Intercept) fi_fishid:T449280_1]  2.104407  8.909976 15.54900
b[(Intercept) fi_fishid:T449282_1] -2.608366  4.324651 11.28307
b[(Intercept) fi_fishid:T449284_1] 12.956794 19.474945 26.11962
b[(Intercept) fi_fishid:T449285_1]  6.685989 10.818546 14.74267
b[(Intercept) fi_fishid:T449286_1] 16.747478 23.204843 29.83264
b[(Intercept) fi_fishid:T449287_1] 19.075846 21.573452 24.11421
b[(Intercept) fi_fishid:T449288_1] 21.011941 27.522322 34.07727
b[(Intercept) fi_fishid:T449310_1] 24.292674 32.353301 40.85601
b[(Intercept) fi_fishid:T449313_1]  9.531977 17.701535 26.22799
b[(Intercept) fi_fishid:T449314_1] 16.263051 22.641271 29.14893
b[(Intercept) fi_fishid:T449317_1] 11.050215 20.565624 30.09546
b[(Intercept) fi_fishid:T449318_1] 19.435011 26.145172 32.88801
b[(Intercept) fi_fishid:T449319_1] 21.727535 28.300474 35.07983

```

`names(a_quant) <- c("X2.5.", "X50.", "X97.5.")`

- Combine summary statistics of posterior simulation draws

`a_df <- data.frame(a_mean, a_sd, a_quant)`

`round(head(a_df), 2)`

```
                                   a_mean a_sd X2.5.  X50. X97.5.
b[(Intercept) fi_fishid:T449202_1]  23.36 4.23 15.06 23.29  31.71
b[(Intercept) fi_fishid:T449203_1]  41.28 1.32 38.75 41.27  43.88
b[(Intercept) fi_fishid:T449204_1]  23.30 7.46  8.36 23.42  38.10
b[(Intercept) fi_fishid:T449207_1]  16.42 1.29 13.91 16.44  18.94
b[(Intercept) fi_fishid:T449208_1]  14.26 4.25  6.12 14.18  22.63
b[(Intercept) fi_fishid:T449209_1]  20.09 1.33 17.48 20.12  22.61

```

### Plots

Caterpillar plot to show the fully Bayes estimates for the *fi_species* varying intercepts in rank order together with their 95% credible interval

- Sort dataframe containing an estimated alpha's mean and sd for every fi_species

`a_df <- a_df[order(a_df$a_mean), ]`

`a_df`
```
                                      a_mean     a_sd     X2.5.      X50.   X97.5.
b[(Intercept) fi_fishid:T449282_1]  4.366196 3.570062 -2.608366  4.324651 11.28307
b[(Intercept) fi_fishid:T449280_1]  8.880983 3.444906  2.104407  8.909976 15.54900
b[(Intercept) fi_fishid:T449285_1] 10.787338 2.036999  6.685989 10.818546 14.74267
b[(Intercept) fi_fishid:T449278_1] 13.073218 3.448058  6.454197 13.039758 19.78347
b[(Intercept) fi_fishid:T449208_1] 14.257421 4.250297  6.117938 14.181613 22.62849
b[(Intercept) fi_fishid:T449207_1] 16.420296 1.288661 13.910712 16.435921 18.94063
b[(Intercept) fi_fishid:T449313_1] 17.690511 4.252608  9.531977 17.701535 26.22799
b[(Intercept) fi_fishid:T449284_1] 19.475980 3.394476 12.956794 19.474945 26.11962
b[(Intercept) fi_fishid:T449209_1] 20.090490 1.326219 17.481025 20.120174 22.61445
b[(Intercept) fi_fishid:T449317_1] 20.510519 4.841234 11.050215 20.565624 30.09546
b[(Intercept) fi_fishid:T449287_1] 21.593162 1.283027 19.075846 21.573452 24.11421
b[(Intercept) fi_fishid:T449269_1] 22.291375 3.383524 15.708730 22.284004 29.12247
b[(Intercept) fi_fishid:T449314_1] 22.668341 3.403647 16.263051 22.641271 29.14893
b[(Intercept) fi_fishid:T449286_1] 23.232288 3.413537 16.747478 23.204843 29.83264
b[(Intercept) fi_fishid:T449204_1] 23.303850 7.464405  8.363744 23.418342 38.10366
b[(Intercept) fi_fishid:T449202_1] 23.357074 4.229106 15.059714 23.294820 31.71013
b[(Intercept) fi_fishid:T449277_1] 25.507544 1.307149 22.981026 25.541950 28.04313
b[(Intercept) fi_fishid:T449215_1] 25.553383 4.984999 15.986957 25.534825 35.31668
b[(Intercept) fi_fishid:T449213_1] 25.717262 4.214109 17.646674 25.686695 34.19274
b[(Intercept) fi_fishid:T449318_1] 26.087980 3.410574 19.435011 26.145172 32.88801
b[(Intercept) fi_fishid:T449288_1] 27.508822 3.404037 21.011941 27.522322 34.07727
b[(Intercept) fi_fishid:T449268_1] 28.207753 3.430869 21.647827 28.132811 34.93453
b[(Intercept) fi_fishid:T449319_1] 28.285563 3.429829 21.727535 28.300474 35.07983
b[(Intercept) fi_fishid:T449275_1] 29.340619 3.379411 22.839940 29.357405 35.91572
b[(Intercept) fi_fishid:T449276_1] 30.054926 3.381010 23.637560 30.055014 36.73707
b[(Intercept) fi_fishid:T449274_1] 30.226853 3.429134 23.601833 30.277627 37.04984
b[(Intercept) fi_fishid:T449271_1] 30.655808 1.306497 28.113859 30.646824 33.23973
b[(Intercept) fi_fishid:T449270_2] 31.797542 1.316491 29.151485 31.802406 34.31239
b[(Intercept) fi_fishid:T449310_1] 32.428542 4.210054 24.292674 32.353301 40.85601
b[(Intercept) fi_fishid:T449272_1] 37.740055 1.279591 35.298228 37.730605 40.30840
b[(Intercept) fi_fishid:T449203_1] 41.278970 1.324974 38.745085 41.265211 43.88082
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
![M6_s1](/Plots/M6_s1.png "M6_s1")

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
    mean  sd   Q2.5    Q50 Q97.5
1 -17.92 4.4 -26.54 -17.98 -9.21

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
  geom_text(aes(0.45, 20, label = "mean = -17.92"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 4.4"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M6_s2](/Plots/M6_s2.png "M6_s2")

The expected difference comes to 3.30 with a standard deviation of 0.46 and a wide range of uncertainty. The 95% credible interval is [2.48,4.29], so we are 95% certain that the true value of the difference between the two species lies within the range, given the data

We also can get the proportion of the time that pike has a higher mean than pikeperch:

`prop.table(table(fish_inter[, 1] > fish_inter[, 2]))`

```
FALSE
    1
```

This means that the posterior probability that pike is better than pikeperch is 0%. Any pair of fishes within the sample of fishes can be compared in this manner.

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
   mean   sd Q2.5   Q50 Q97.5
1 17.98 7.56 2.87 17.99 32.99
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
  geom_text(aes(0.45, 20, label = "mean = 17.98"),
            color = "red",
            size = 4) +
  geom_text(aes(9, 50, label = "SD = 7.56"),
            color = "blue",
            size = 4) +
  theme_bw( base_family = "serif")`

![M6_s3](/Plots/M6_s3.png "M6_s3")

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

![M6_s4](/Plots/M6_s4.png "M6_s4")

### Cross-validation checking

We can use Pareto-smoothed importance sampling LOO CV as model checking tool (2017b)

`library(loo)`

`loo<-loo(m6)`

`loo`

```
Computed from 4000 by 8846 log-likelihood matrix

         Estimate    SE
elpd_loo -34961.1  79.3
p_loo       128.5   2.8
looic     69922.2 158.6
------
Monte Carlo SE of elpd_loo is 0.2.

All Pareto k estimates are good (k < 0.5).
See help('pareto-k-diagnostic') for details.

```
`plot(loo, diagnostic = c("k", "n_eff"), label_points = FALSE, main = "PSIS diagnostic plot")`

![M6_sloo](/Plots/M6_sloo.png "M6_sloo")


### Analyse posterior

#### Plot posterior

- Including random effects from *fi_species*

`library(bayesplot)`

`mcmc_areas(as.matrix(m6), prob_outer = .999)`

![M6_s5](/Plots/M6_s5.png "M6_s5")

- Excluding random effects from three *fi_fishid*

`mcmc_areas(as.matrix(m6), prob_outer = .999, pars = c("(Intercept)","b[(Intercept) fi_fishid:T449284_1]", "b[(Intercept) fi_fishid:T449284_1]", "b[(Intercept) fi_fishid:T449207_1]"))`

![M6_s6](/Plots/M6_s6.png "M6_s6")

- *fi_species:26*,*fi_species:27*,*fi_species:28*,*fi_species:10*,*fi_species:19*,*fi_species:18* have both lot of probability mass on both sides of 0


#### Plot paired marginals

`mcmc_pairs(as.matrix(m6), pars = c("(Intercept)","b[(Intercept) fi_species:pike]", "b[(Intercept) fi_species:pikeperch]", "b[(Intercept) fi_species:wels]"))`

![M6_s7](/Plots/M6_s7.png "M6_s7")


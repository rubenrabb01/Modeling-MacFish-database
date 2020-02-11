# summary(m2)

> summary(m2)

Model Info:

 function:     stan_lmer
 family:       gaussian [identity]
 formula:      sqrt(ranged2d + 1) ~ 1 + season * fi_species + (1 | fi_fishid)
 algorithm:    sampling
 priors:       see help('prior_summary')
 sample:       4000 (posterior sample size)
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
                                           mean     sd       2.5%     25%      50%      75%      97.5% 
(Intercept)                                  26.6      2.2     22.1     25.1     26.5     28.0     31.1
seasonspring_I                               -1.8      0.8     -3.5     -2.4     -1.8     -1.2     -0.1
seasonspring_II                              14.6      1.7     11.4     13.5     14.6     15.7     18.1
seasonsummer                                 -3.3      0.7     -4.7     -3.7     -3.3     -2.8     -1.9
seasonwinter                                  3.7      0.8      2.2      3.2      3.7      4.2      5.2
fi_speciespikeperch                          -7.6      3.4    -14.3     -9.7     -7.7     -5.5     -1.0
fi_specieswels                                0.2      3.0     -5.9     -1.8      0.2      2.2      6.1
seasonspring_I:fi_speciespikeperch            7.6      1.3      5.0      6.7      7.6      8.4     10.1
seasonspring_II:fi_speciespikeperch         -13.7      2.5    -18.6    -15.3    -13.6    -12.0     -8.9
seasonsummer:fi_speciespikeperch             13.0      1.1     10.8     12.2     13.0     13.7     15.1
seasonwinter:fi_speciespikeperch             -3.5      1.2     -6.0     -4.3     -3.5     -2.7     -1.1
seasonspring_I:fi_specieswels                 6.8      1.1      4.8      6.1      6.8      7.5      8.9
seasonspring_II:fi_specieswels              -10.2      2.2    -14.5    -11.7    -10.2     -8.7     -6.1
seasonsummer:fi_specieswels                   4.3      0.9      2.6      3.7      4.3      4.9      6.1
seasonwinter:fi_specieswels                  -2.9      1.0     -4.8     -3.6     -2.9     -2.2     -1.0
b[(Intercept) fi_fishid:T449202_1]           -1.0      2.7     -6.4     -2.7     -0.9      0.7      4.2
b[(Intercept) fi_fishid:T449203_1]            6.1      2.3      1.7      4.6      6.1      7.7     10.7
b[(Intercept) fi_fishid:T449204_1]           -2.1      2.5     -6.9     -3.8     -2.1     -0.5      2.9
b[(Intercept) fi_fishid:T449207_1]           -8.2      2.3    -12.8     -9.7     -8.2     -6.7     -3.7
b[(Intercept) fi_fishid:T449208_1]          -10.0      2.7    -15.6    -11.7     -9.9     -8.3     -4.8
b[(Intercept) fi_fishid:T449209_1]           -0.5      2.3     -5.0     -1.9     -0.5      1.1      4.0
b[(Intercept) fi_fishid:T449213_1]           10.4      2.7      5.0      8.7     10.5     12.2     15.8
b[(Intercept) fi_fishid:T449215_1]            6.4      2.7      0.9      4.7      6.5      8.2     11.8
b[(Intercept) fi_fishid:T449268_1]            6.7      1.9      2.8      5.4      6.7      8.0     10.4
b[(Intercept) fi_fishid:T449269_1]            4.8      2.0      1.0      3.5      4.8      6.0      8.6
b[(Intercept) fi_fishid:T449270_2]            5.5      2.4      0.7      3.9      5.5      7.0     10.1
b[(Intercept) fi_fishid:T449271_1]            0.3      2.3     -4.3     -1.2      0.3      1.8      4.8
b[(Intercept) fi_fishid:T449272_1]           11.3      2.3      6.7      9.8     11.3     12.8     15.8
b[(Intercept) fi_fishid:T449274_1]            7.5      1.9      3.6      6.2      7.5      8.8     11.1
b[(Intercept) fi_fishid:T449275_1]            6.1      1.9      2.3      4.8      6.1      7.4      9.9
b[(Intercept) fi_fishid:T449276_1]            1.8      1.9     -2.1      0.5      1.9      3.1      5.6
b[(Intercept) fi_fishid:T449277_1]            0.5      2.3     -4.0     -1.0      0.6      2.0      5.2
b[(Intercept) fi_fishid:T449278_1]           -9.8      2.0    -13.7    -11.1     -9.8     -8.4     -6.1
b[(Intercept) fi_fishid:T449280_1]           -2.4      2.0     -6.3     -3.8     -2.5     -1.1      1.3
b[(Intercept) fi_fishid:T449282_1]          -15.0      2.0    -18.9    -16.4    -15.0    -13.7    -11.2
b[(Intercept) fi_fishid:T449284_1]           -5.7      1.9     -9.6     -7.0     -5.7     -4.4     -2.0
b[(Intercept) fi_fishid:T449285_1]           -6.0      2.4    -10.9     -7.6     -6.0     -4.4     -1.3
b[(Intercept) fi_fishid:T449286_1]           -1.5      2.0     -5.4     -2.8     -1.5     -0.1      2.3
b[(Intercept) fi_fishid:T449287_1]           -7.0      2.3    -11.6     -8.5     -7.0     -5.5     -2.5
b[(Intercept) fi_fishid:T449288_1]            3.5      2.0     -0.4      2.1      3.5      4.8      7.4
b[(Intercept) fi_fishid:T449310_1]            1.1      2.7     -4.2     -0.6      1.2      2.9      6.4
b[(Intercept) fi_fishid:T449313_1]           -0.8      2.6     -6.1     -2.5     -0.6      0.9      4.5
b[(Intercept) fi_fishid:T449314_1]           -1.3      2.0     -5.3     -2.6     -1.3      0.0      2.5
b[(Intercept) fi_fishid:T449317_1]           -6.1      2.7    -11.7     -7.9     -6.0     -4.4     -0.8
b[(Intercept) fi_fishid:T449318_1]            1.3      2.0     -2.5      0.0      1.4      2.6      5.1
b[(Intercept) fi_fishid:T449319_1]            4.3      1.9      0.6      3.0      4.3      5.6      8.0
sigma                                        13.7      0.1     13.5     13.6     13.7     13.7     13.9
Sigma[fi_fishid:(Intercept),(Intercept)]     48.1     14.2     28.0     38.1     45.5     55.3     82.6
mean_PPD                                     26.9      0.2     26.5     26.8     26.9     27.1     27.3
log-posterior                            -35748.5      6.3 -35761.6 -35752.7 -35748.3 -35744.1 -35736.9

Diagnostics:
                                         mcse Rhat n_eff
(Intercept)                              0.1  1.0   663 
seasonspring_I                           0.0  1.0  1943 
seasonspring_II                          0.0  1.0  2236 
seasonsummer                             0.0  1.0  1970 
seasonwinter                             0.0  1.0  2179 
fi_speciespikeperch                      0.1  1.0   903 
fi_specieswels                           0.1  1.0   845 
seasonspring_I:fi_speciespikeperch       0.0  1.0  2126 
seasonspring_II:fi_speciespikeperch      0.1  1.0  2077 
seasonsummer:fi_speciespikeperch         0.0  1.0  2034 
seasonwinter:fi_speciespikeperch         0.0  1.0  2255 
seasonspring_I:fi_specieswels            0.0  1.0  2015 
seasonspring_II:fi_specieswels           0.0  1.0  2371 
seasonsummer:fi_specieswels              0.0  1.0  2018 
seasonwinter:fi_specieswels              0.0  1.0  2197 
b[(Intercept) fi_fishid:T449202_1]       0.1  1.0  1066 
b[(Intercept) fi_fishid:T449203_1]       0.1  1.0   673 
b[(Intercept) fi_fishid:T449204_1]       0.1  1.0   719 
b[(Intercept) fi_fishid:T449207_1]       0.1  1.0   675 
b[(Intercept) fi_fishid:T449208_1]       0.1  1.0  1058 
b[(Intercept) fi_fishid:T449209_1]       0.1  1.0   732 
b[(Intercept) fi_fishid:T449213_1]       0.1  1.0  1114 
b[(Intercept) fi_fishid:T449215_1]       0.1  1.0  1133 
b[(Intercept) fi_fishid:T449268_1]       0.1  1.0   868 
b[(Intercept) fi_fishid:T449269_1]       0.1  1.0   856 
b[(Intercept) fi_fishid:T449270_2]       0.1  1.0   729 
b[(Intercept) fi_fishid:T449271_1]       0.1  1.0   670 
b[(Intercept) fi_fishid:T449272_1]       0.1  1.0   695 
b[(Intercept) fi_fishid:T449274_1]       0.1  1.0   876 
b[(Intercept) fi_fishid:T449275_1]       0.1  1.0   877 
b[(Intercept) fi_fishid:T449276_1]       0.1  1.0   866 
b[(Intercept) fi_fishid:T449277_1]       0.1  1.0   708 
b[(Intercept) fi_fishid:T449278_1]       0.1  1.0   908 
b[(Intercept) fi_fishid:T449280_1]       0.1  1.0   889 
b[(Intercept) fi_fishid:T449282_1]       0.1  1.0  1009 
b[(Intercept) fi_fishid:T449284_1]       0.1  1.0   859 
b[(Intercept) fi_fishid:T449285_1]       0.1  1.0   720 
b[(Intercept) fi_fishid:T449286_1]       0.1  1.0   916 
b[(Intercept) fi_fishid:T449287_1]       0.1  1.0   777 
b[(Intercept) fi_fishid:T449288_1]       0.1  1.0   941 
b[(Intercept) fi_fishid:T449310_1]       0.1  1.0  1073 
b[(Intercept) fi_fishid:T449313_1]       0.1  1.0  1109 
b[(Intercept) fi_fishid:T449314_1]       0.1  1.0   874 
b[(Intercept) fi_fishid:T449317_1]       0.1  1.0  1060 
b[(Intercept) fi_fishid:T449318_1]       0.1  1.0   887 
b[(Intercept) fi_fishid:T449319_1]       0.1  1.0   911 
sigma                                    0.0  1.0  3728 
Sigma[fi_fishid:(Intercept),(Intercept)] 0.4  1.0  1010 
mean_PPD                                 0.0  1.0  3698 
log-posterior                            0.2  1.0   777 

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).
> 

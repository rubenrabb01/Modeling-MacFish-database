# summary(m2)

Model Info:

 function:     stan_lmer
 family:       gaussian [identity]
 formula:      ranged2d ~ 1 + season * fi_species + (1 | fi_fishid)
 algorithm:    sampling
 priors:       see help('prior_summary')
 sample:       4000 (posterior sample size)
 observations: 8846
 groups:       fi_fishid (31)

Estimates:
                                           mean     sd       2.5%     25%      50%      75%      97.5% 
(Intercept)                                 965.1    121.3    723.1    886.6    965.8   1040.5   1219.7
seasonspring_I                             -134.1     51.1   -232.5   -168.6   -134.3    -99.7    -35.2
seasonspring_II                            1012.1    105.0    804.5    942.9   1014.0   1081.9   1215.3
seasonsummer                               -248.5     42.6   -332.0   -276.3   -249.0   -219.6   -165.3
seasonwinter                                195.4     47.1    101.2    164.2    195.6    226.4    288.8
fi_speciespikeperch                        -413.9    195.5   -791.2   -538.3   -412.2   -290.4    -25.4
fi_specieswels                              -83.4    157.1   -404.4   -184.8    -85.2     20.5    220.0
seasonspring_I:fi_speciespikeperch          535.6     78.9    379.4    481.5    536.3    589.9    688.9
seasonspring_II:fi_speciespikeperch        -956.6    154.4  -1252.3  -1063.2   -959.7   -850.4   -657.7
seasonsummer:fi_speciespikeperch            785.5     66.3    656.4    741.9    785.7    830.0    916.2
seasonwinter:fi_speciespikeperch           -165.5     74.5   -313.6   -213.5   -165.3   -114.6    -22.4
seasonspring_I:fi_specieswels               561.2     65.2    432.1    517.2    561.6    605.7    689.4
seasonspring_II:fi_specieswels             -737.7    131.7   -993.2   -825.8   -740.3   -650.1   -478.6
seasonsummer:fi_specieswels                 412.3     54.1    305.8    374.9    412.5    448.2    520.8
seasonwinter:fi_specieswels                -151.8     59.6   -263.8   -192.9   -153.2   -112.2    -32.7
b[(Intercept) fi_fishid:T449202_1]          -45.3    150.3   -331.6   -146.0    -45.0     53.2    262.2
b[(Intercept) fi_fishid:T449203_1]          533.5    124.2    288.6    453.5    533.5    611.2    782.0
b[(Intercept) fi_fishid:T449204_1]         -117.2    133.0   -388.5   -205.9   -118.5    -26.1    139.1
b[(Intercept) fi_fishid:T449207_1]         -477.7    124.7   -731.7   -557.7   -479.1   -396.7   -234.4
b[(Intercept) fi_fishid:T449208_1]         -462.6    149.3   -751.6   -561.0   -462.3   -365.5   -165.6
b[(Intercept) fi_fishid:T449209_1]         -120.5    124.4   -372.6   -199.6   -120.5    -38.9    130.5
b[(Intercept) fi_fishid:T449213_1]          543.0    151.1    253.4    442.2    540.5    639.0    849.4
b[(Intercept) fi_fishid:T449215_1]          364.4    155.6     64.4    261.0    362.5    463.0    674.9
b[(Intercept) fi_fishid:T449268_1]          388.5    104.9    186.3    315.8    387.0    459.3    594.0
b[(Intercept) fi_fishid:T449269_1]          310.6    104.1    109.1    241.8    308.7    380.4    517.7
b[(Intercept) fi_fishid:T449270_2]          300.9    129.1     38.4    216.1    300.4    383.1    549.0
b[(Intercept) fi_fishid:T449271_1]           40.8    125.9   -212.2    -41.3     40.3    123.2    284.1
b[(Intercept) fi_fishid:T449272_1]          659.9    125.2    407.3    580.7    659.1    740.1    907.6
b[(Intercept) fi_fishid:T449274_1]          368.8    104.0    168.9    297.7    366.3    438.8    574.4
b[(Intercept) fi_fishid:T449275_1]          283.0    103.6     83.8    214.5    280.9    353.4    488.4
b[(Intercept) fi_fishid:T449276_1]           90.5    103.8   -109.4     19.9     88.5    158.8    295.4
b[(Intercept) fi_fishid:T449277_1]          -71.3    125.4   -329.1   -148.9    -71.2      9.4    172.4
b[(Intercept) fi_fishid:T449278_1]         -528.2    104.1   -733.4   -597.1   -530.8   -457.2   -326.8
b[(Intercept) fi_fishid:T449280_1]           19.6    104.4   -179.7    -52.8     18.1     91.8    228.5
b[(Intercept) fi_fishid:T449282_1]         -613.9    110.7   -831.9   -687.9   -614.3   -539.2   -389.4
b[(Intercept) fi_fishid:T449284_1]         -279.6    103.8   -480.6   -350.3   -282.3   -209.6    -72.7
b[(Intercept) fi_fishid:T449285_1]         -339.4    131.2   -603.0   -422.9   -340.5   -254.0    -78.0
b[(Intercept) fi_fishid:T449286_1]         -224.9    105.5   -433.5   -295.0   -226.0   -152.9    -21.1
b[(Intercept) fi_fishid:T449287_1]         -388.0    125.7   -648.2   -467.4   -390.1   -305.9   -138.6
b[(Intercept) fi_fishid:T449288_1]           98.7    106.4   -105.4     27.6     98.5    169.9    309.7
b[(Intercept) fi_fishid:T449310_1]          -41.1    151.3   -338.6   -142.2    -40.8     58.1    258.4
b[(Intercept) fi_fishid:T449313_1]           10.0    150.4   -284.4    -91.1      8.5    107.9    313.9
b[(Intercept) fi_fishid:T449314_1]         -170.4    103.6   -370.8   -240.4   -170.9   -101.9     33.5
b[(Intercept) fi_fishid:T449317_1]         -405.5    154.7   -707.9   -509.0   -405.5   -307.1    -99.3
b[(Intercept) fi_fishid:T449318_1]           43.8    104.2   -152.9    -28.7     41.8    113.3    245.3
b[(Intercept) fi_fishid:T449319_1]          199.5    103.8     -3.9    128.5    195.9    270.3    404.2
sigma                                       822.5      6.3    809.8    818.2    822.3    826.7    834.9
Sigma[fi_fishid:(Intercept),(Intercept)] 139793.5  43460.2  79248.7 110524.0 133166.2 160962.2 238678.5
mean_PPD                                    963.0     12.6    938.0    954.2    963.4    971.4    987.6
log-posterior                            -71998.7      6.3 -72012.0 -72002.7 -71998.3 -71994.5 -71987.6

Diagnostics:
                                         mcse   Rhat   n_eff
(Intercept)                                 3.9    1.0  953 
seasonspring_I                              1.3    1.0 1603 
seasonspring_II                             2.2    1.0 2337 
seasonsummer                                0.9    1.0 2088 
seasonwinter                                1.1    1.0 1964 
fi_speciespikeperch                         5.7    1.0 1196 
fi_specieswels                              5.6    1.0  774 
seasonspring_I:fi_speciespikeperch          1.8    1.0 2016 
seasonspring_II:fi_speciespikeperch         3.2    1.0 2369 
seasonsummer:fi_speciespikeperch            1.4    1.0 2326 
seasonwinter:fi_speciespikeperch            1.5    1.0 2343 
seasonspring_I:fi_specieswels               1.5    1.0 1812 
seasonspring_II:fi_specieswels              2.7    1.0 2418 
seasonsummer:fi_specieswels                 1.2    1.0 2188 
seasonwinter:fi_specieswels                 1.3    1.0 2114 
b[(Intercept) fi_fishid:T449202_1]          4.2    1.0 1288 
b[(Intercept) fi_fishid:T449203_1]          3.8    1.0 1064 
b[(Intercept) fi_fishid:T449204_1]          4.0    1.0 1120 
b[(Intercept) fi_fishid:T449207_1]          3.9    1.0 1040 
b[(Intercept) fi_fishid:T449208_1]          4.2    1.0 1239 
b[(Intercept) fi_fishid:T449209_1]          3.8    1.0 1080 
b[(Intercept) fi_fishid:T449213_1]          4.2    1.0 1265 
b[(Intercept) fi_fishid:T449215_1]          4.2    1.0 1341 
b[(Intercept) fi_fishid:T449268_1]          3.2    1.0 1054 
b[(Intercept) fi_fishid:T449269_1]          3.2    1.0 1057 
b[(Intercept) fi_fishid:T449270_2]          3.9    1.0 1074 
b[(Intercept) fi_fishid:T449271_1]          3.9    1.0 1034 
b[(Intercept) fi_fishid:T449272_1]          3.9    1.0 1022 
b[(Intercept) fi_fishid:T449274_1]          3.2    1.0 1045 
b[(Intercept) fi_fishid:T449275_1]          3.2    1.0 1076 
b[(Intercept) fi_fishid:T449276_1]          3.2    1.0 1041 
b[(Intercept) fi_fishid:T449277_1]          3.9    1.0 1035 
b[(Intercept) fi_fishid:T449278_1]          3.2    1.0 1060 
b[(Intercept) fi_fishid:T449280_1]          3.2    1.0 1052 
b[(Intercept) fi_fishid:T449282_1]          3.3    1.0 1142 
b[(Intercept) fi_fishid:T449284_1]          3.3    1.0 1004 
b[(Intercept) fi_fishid:T449285_1]          3.9    1.0 1161 
b[(Intercept) fi_fishid:T449286_1]          3.2    1.0 1086 
b[(Intercept) fi_fishid:T449287_1]          3.8    1.0 1080 
b[(Intercept) fi_fishid:T449288_1]          3.2    1.0 1077 
b[(Intercept) fi_fishid:T449310_1]          4.3    1.0 1236 
b[(Intercept) fi_fishid:T449313_1]          4.2    1.0 1311 
b[(Intercept) fi_fishid:T449314_1]          3.2    1.0 1044 
b[(Intercept) fi_fishid:T449317_1]          4.2    1.0 1360 
b[(Intercept) fi_fishid:T449318_1]          3.3    1.0 1021 
b[(Intercept) fi_fishid:T449319_1]          3.2    1.0 1054 
sigma                                       0.1    1.0 3714 
Sigma[fi_fishid:(Intercept),(Intercept)] 1250.3    1.0 1208 
mean_PPD                                    0.2    1.0 3535 
log-posterior                               0.2    1.0  963 

For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).

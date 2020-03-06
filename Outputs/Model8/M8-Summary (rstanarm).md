`m8 <- stan_lmer(formula=sqrt(ranged2d + 1) ~ 1 + ca_tl_mm * fi_species + (1 + season|fi_fishid), data = mean.ranged2d, seed=350)`

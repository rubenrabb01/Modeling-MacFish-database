`m_final<-stan_lmer(formula= sqrt(ranged2d + 1) ~ 1 + fi_species + season + ca_tl_mm + season:fi_species + (date|fi_fishid), data = mean.ranged2d, seed=350)`

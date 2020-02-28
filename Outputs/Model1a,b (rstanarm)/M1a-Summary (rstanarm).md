# Consider the simplest multilevel model for fishes i=1,...,n nested within fi_species j=1,...,J and for whom we have examination scores as responses. We can write a two-level varying intercept model with no predictors using the usual two-stage formulation as

yij=αj+ϵij, where ϵij∼N(0,σ2y)
αj=μα+uj, where uj∼N(0,σ2α)

where yij is the examination score for the ith fish in the jth school, αj is the varying intercept for the jth school, and μα is the overall mean across fi_species. Alternatively, the model can be expressed in reduced form as
yij=μα+uj+ϵij.
. If we further assume that the fish-level errors ϵij are normally distributed with mean 0 and variance σ2y, and that the school-level varying intercepts αj are normally distributed with mean μα and variance σ2α, then the model can be expressed as

yij∼N(αj,σ2y),
αj∼N(μα,σ2α),

This model can then be fit using lmer(). We specify an intercept (the predictor “1”) and allow it to vary by the level-2 identifier (school). We also specify the REML = FALSE option to obtain maximum likelihood (ML) estimates as opposed to the default restricted maximum likelihood (REML) estimates.

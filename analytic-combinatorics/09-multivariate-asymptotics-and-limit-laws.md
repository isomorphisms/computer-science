# Chapter IX — Multivariate Asymptotics and Limit Laws

## Main idea

A marked generating function contains more than expectations. Its coefficient asymptotics can describe the full limiting distribution of parameters in large random combinatorial structures.

This chapter develops discrete and continuous limit laws, Gaussian limits, local limit laws, large deviations, non-Gaussian limits, and genuinely multivariate limits.

## Perturbing a marked generating function

Suppose `F(z,u)` marks a parameter with `u`. For `u` close to `1`, the dominant singularity in `z` generally moves. Tracking that movement gives asymptotics for the probability generating function of the parameter conditioned on size `n`.

The same principle can be applied in the principal analytic regimes developed earlier:

- perturbation of meromorphic asymptotics;
- perturbation of singularity-analysis asymptotics;
- perturbation of saddle-point asymptotics.

Under common quasi-power conditions, the normalized parameter approaches a Gaussian law. Other singular structures produce discrete or non-Gaussian limits.

## Why this is useful for estimates

An average alone can be misleading. For engineering questions one may need to know whether values are tightly concentrated, whether tails are substantial, or whether two parameters move together.

This chapter supplies a route from a structural model to statements such as:

- expected resource use is approximately linear in `n`;
- standard deviation is approximately proportional to `sqrt(n)`;
- normalized fluctuations are asymptotically Gaussian;
- unusually large deviations are exponentially rare;
- two marked quantities have a limiting joint distribution.

Such results can inform rough capacity planning before machine-specific measurements are available.

## Pen-and-paper workflow

1. Mark the parameter or parameters.
2. Determine the dominant asymptotic form uniformly near the marking point.
3. Differentiate for moments when that is sufficient.
4. Use a limit-law theorem when distributional information matters.
5. Translate the resulting quantiles or tails into resource margins rather than relying only on the mean.

## Source

Flajolet and Sedgewick, *Analytic Combinatorics*, Chapter IX. Official materials: https://ac.cs.princeton.edu/90multivariate/

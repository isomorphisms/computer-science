# Chapter VIII — Saddle-Point Asymptotics

## Main idea

Some generating functions are entire or otherwise lack a convenient dominant finite singularity. Cauchy's coefficient integral still applies, but the contour should be chosen so that the integral is concentrated near points where the exponential part of the integrand is stationary. These are **saddle points**.

For

\[
[z^n]f(z)=\frac{1}{2\pi i}\oint \frac{f(z)}{z^{n+1}}\,dz,
\]

one chooses a radius and local contour geometry that balance the growth of `f(z)` against `z^{-n}`. Near the saddle, a quadratic approximation often turns the dominant contribution into a Gaussian integral.

## Practical content

The chapter develops:

- modulus landscapes of analytic functions;
- saddle-point bounds;
- the saddle-point approximation itself;
- admissibility conditions that justify the approximation;
- applications including integer partitions;
- large powers;
- relations with probability distributions;
- multiple saddle points.

## Why this is useful for estimates

Saddle-point analysis handles coefficient regimes where the simpler “nearest singularity” heuristic is not enough. It is also a natural technique for large powers and for objects whose generating functions contain exponentials of rapidly growing functions.

For rough work, the most important step is often the saddle equation itself: it identifies the scale at which the main contribution occurs. The second derivative around that point determines the Gaussian-width correction.

This method connects naturally to Laplace's method, steepest descent, large-deviation estimates, and the normal approximations that appear throughout algorithm analysis and probability.

## Source

Flajolet and Sedgewick, *Analytic Combinatorics*, Chapter VIII. Official materials: https://ac.cs.princeton.edu/80saddle/

# Chapter IV — Complex Analysis, Rational and Meromorphic Asymptotics

## Main idea

The coefficients of a generating function are controlled by its behavior as a complex analytic function. Cauchy's coefficient formula makes the connection explicit:

\[
[z^n]f(z)=\frac{1}{2\pi i}\oint \frac{f(z)}{z^{n+1}}\,dz.
\]

Coefficient extraction is therefore a contour-integral problem.

## Dominant singularities

The singularities nearest the origin usually determine the main exponential scale of the coefficients. If the radius of convergence is `ρ`, then coefficients commonly grow on a scale involving `ρ^{-n}`. The type and multiplicity of the singularity determine the accompanying polynomial factors and constants.

For rational functions this is especially concrete: find the poles by locating zeros of the denominator, identify those of smallest modulus, and use partial fractions or residues. Meromorphic functions extend the same basic logic beyond rational functions.

## Why this is useful for estimates

This chapter supplies one of the fastest practical routes from an exact generating function to a magnitude estimate.

For a rational generating function:

1. find the denominator;
2. solve for its zeros;
3. locate the zero or zeros of smallest modulus;
4. determine pole orders and residues;
5. obtain the dominant coefficient contribution.

A simple positive dominant pole at `ρ` very often yields

\[
a_n \sim C\rho^{-n}.
\]

That immediately exposes the exponential base, which is often the most important fact in a feasibility calculation.

## Multiple dominant poles

Several singularities can lie on the same circle of convergence. Their contributions can interfere and produce periodic or oscillatory behavior. Looking only at the positive real singularity can therefore miss residue-class effects.

## Source

Flajolet and Sedgewick, *Analytic Combinatorics*, Chapter IV. Official materials: https://ac.cs.princeton.edu/40complex/

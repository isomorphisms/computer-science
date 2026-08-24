# Chapter VI — Singularity Analysis of Generating Functions

## Main idea

Not every important generating function is meromorphic. Trees and many recursively defined classes produce algebraic, logarithmic, or other non-pole singularities. **Singularity analysis** transfers a local expansion near a dominant singularity directly into an asymptotic expansion of coefficients.

The standard model is

\[
(1-z)^{-\alpha},
\]

whose coefficients satisfy, in the generic case,

\[
[z^n](1-z)^{-\alpha}\sim \frac{n^{\alpha-1}}{\Gamma(\alpha)}.
\]

After rescaling a singularity from `ρ` to `1`, the corresponding exponential factor is `ρ^{-n}`.

## Transfer principle

The practical recipe is:

1. locate the dominant singularity or singularities;
2. establish a domain where the function can be approached analytically near them;
3. expand the function locally in a standard singular scale;
4. transfer each singular term to a coefficient asymptotic;
5. keep as many terms as the desired accuracy requires.

This is much more informative than merely knowing the radius of convergence. The singular exponent supplies the polynomial correction multiplying the exponential growth.

## Common forms

Square-root singularities frequently lead to an `n^{-3/2}` factor. Logarithmic terms produce their own characteristic coefficient scales. Multiple singularities can add periodic effects. The chapter also develops inverse functions, polylogarithms, composition, closure properties, Tauberian ideas, and Darboux's method.

## Why this is useful for estimates

A rough statement such as “exponential” can hide orders of magnitude. Distinguishing

`C ρ^{-n}`, `C ρ^{-n} n^{-1/2}`, and `C ρ^{-n} n^{-3/2}`

can matter substantially at realistic `n`. Singularity analysis supplies these corrections systematically.

## Source

Flajolet and Sedgewick, *Analytic Combinatorics*, Chapter VI. Official materials: https://ac.cs.princeton.edu/60singularity/

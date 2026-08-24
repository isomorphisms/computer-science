# Chapter III — Combinatorial Parameters and Multivariate Generating Functions

## Main idea

Counting objects is only the beginning. Often the useful question is how some parameter behaves inside objects of size `n`: number of nodes of a kind, path length, number of components, number of cycles, number of occupied slots, and so on.

Introduce an additional variable to **mark** the parameter. A typical bivariate generating function is

\[
F(z,u)=\sum_{n,k} f_{n,k} z^n u^k,
\]

with the OGF or EGF normalization chosen for the underlying class.

Here `z` marks size and `u` marks the parameter.

## Extracting statistics

At fixed size `n`, the coefficients in `u` encode the complete distribution of the parameter. Differentiating with respect to `u` and then setting `u=1` gives factorial moments. From these one obtains the mean, variance, standard deviation, and higher moments.

This turns structural specifications into probabilistic information without needing to list every object explicitly.

## Why this is useful for estimates

This chapter is especially relevant to algorithm engineering because many resource questions are parameters rather than class sizes:

- expected tree height or degree statistics;
- expected number of components;
- expected number of occurrences of an operation or pattern;
- expected occupancy or collision counts;
- expected representation size conditioned on an input size.

A bivariate generating function gives a clean separation between the primary input scale and the resource or structural quantity being measured.

## Pen-and-paper workflow

1. Define the size variable `z`.
2. Introduce `u` for exactly one parameter at first.
3. Modify the symbolic specification so every occurrence of the marked feature contributes a factor of `u`.
4. Differentiate at `u=1` to get moment generating series.
5. Extract coefficients or apply asymptotic methods.

Later chapters show how the singularities of `F(z,u)` move as `u` changes; that movement often determines limiting probability laws.

## Source

Flajolet and Sedgewick, *Analytic Combinatorics*, Chapter III. Official materials: https://ac.cs.princeton.edu/30mgf/

# Chapter V — Applications of Rational and Meromorphic Asymptotics

## Main idea

Chapter IV develops the analytic machinery for poles. Chapter V shows how often ordinary combinatorial specifications lead directly to that situation.

The examples include strings, compositions, regular specifications and languages, nested sequences, lattice paths, graph paths, automata, and transfer-matrix models.

## Supercritical sequence schema

A particularly reusable pattern is a sequence construction with generating function

\[
F(z)=\frac{1}{1-G(z)}.
\]

If a positive solution `ρ` of `G(ρ)=1` occurs before any singularity of `G` itself and the usual regularity conditions hold, `F` has a simple pole at `ρ`. Consequently the coefficients normally have pure exponential leading growth

\[
[z^n]F(z)\sim C\rho^{-n}.
\]

The analytic problem has been reduced to solving a scalar equation and evaluating a derivative.

## Automata and transfer matrices

Finite-state restrictions on strings and paths naturally produce rational generating functions. The same structures can be represented by transfer matrices. Dominant eigenvalues, denominator roots, and poles are different views of closely related growth information.

This makes the chapter directly useful for computing problems involving finite-state machines, protocol states, constrained strings, instruction sequences, or walks through a finite graph.

## Pen-and-paper use

When an object is assembled by repeated choices from a finite or rationally describable collection, first check whether its generating function is rational or meromorphic. If so, heavy asymptotic machinery may be unnecessary. A pole computation can often give the exponential growth rate and leading constant directly.

For rough feasibility estimates, even `ρ^{-1}` alone can be valuable: it is the multiplicative growth factor per additional unit of size.

## Source

Flajolet and Sedgewick, *Analytic Combinatorics*, Chapter V. Official materials: https://ac.cs.princeton.edu/50applications/

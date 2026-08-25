# Chapter II — Labelled Structures and Exponential Generating Functions

## Main idea

When atoms are distinguishable, ordinary generating functions usually do not encode the natural product operation correctly. The appropriate object is often the exponential generating function

\[
A(z)=\sum_{n\ge 0} a_n\frac{z^n}{n!}.
\]

The factor `n!` accounts for the ways labels can be distributed among components.

## Symbolic method for labelled classes

The same structural philosophy survives, but the translations change. In particular, labelled products automatically account for the binomial choices involved in distributing labels. Important labelled constructions include products, sequences, sets, cycles, substitutions, labelled trees, mappings, permutations, and related structures.

Typical identities include the labelled set construction leading to exponentials such as `exp(A(z))`, while cycle constructions lead to logarithmic forms. The chapter develops these as systematic translations from combinatorial descriptions rather than isolated tricks.

## Why this is useful for estimates

Many structures in algorithms are naturally labelled: permutations, mappings, graphs on named vertices, labelled trees, allocations, and arrangements of distinct records. An exponential generating function often turns a complicated recurrence into a compact analytic expression.

A useful pen-and-paper workflow is:

1. ask whether exchanging two atoms produces a genuinely different object;
2. if labels matter, try an EGF before forcing the problem into an OGF;
3. derive the structural specification;
4. recover exact counts by multiplying the coefficient of `z^n` by `n!`;
5. pass the resulting analytic expression to later asymptotic methods.

## Scale intuition

The presence of `n!` is itself important. Labelled classes can naturally have factorial-scale counts. This matters immediately for storage or exhaustive-search estimates: even if a generating-function coefficient looks modest, the actual object count may include the `n!` factor.

## Source

Flajolet and Sedgewick, *Analytic Combinatorics*, Chapter II. Official materials: https://ac.cs.princeton.edu/20egf/

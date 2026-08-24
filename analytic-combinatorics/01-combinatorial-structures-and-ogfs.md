# Chapter I — Combinatorial Structures and Ordinary Generating Functions

## Main idea

Start with an unlabelled combinatorial class and encode the number of objects of each size in an ordinary generating function

\[
A(z)=\sum_{n\ge 0} a_n z^n.
\]

The coefficient `a_n` is the number of objects of size `n`. The central device is the **symbolic method**: build complicated classes from simple classes, and translate those constructions into algebraic operations on generating functions.

## Structural operations

For suitably defined classes, the basic correspondences include:

- disjoint union → addition of generating functions;
- product → multiplication;
- sequences → geometric-series constructions;
- substitutions → composition under the required admissibility conditions;
- sets, multisets, powersets, compositions, partitions, trees, and strings → characteristic generating-function constructions.

The important point is not merely that a generating function exists. A structural specification can often be translated almost mechanically into equations for it.

## Why this is useful for estimates

Once the generating function is known, exact initial counts can be obtained by coefficient extraction or recurrence. More importantly for later chapters, the form of the generating function exposes the analytic features that determine large-`n` behavior.

For pen-and-paper work this chapter is the specification stage:

1. decide what counts as an object and what its size is;
2. write the object recursively from standard constructions;
3. translate the construction to a generating-function equation;
4. compute enough coefficients to check that the model matches the intended objects.

This is particularly useful when the object being counted is a data structure, parse tree, path, state configuration, restricted string, or other discrete family arising in computing.

## Practical warning

A correct asymptotic analysis cannot rescue the wrong combinatorial model. Before doing complex analysis, check small values by direct enumeration whenever feasible.

## Source

Flajolet and Sedgewick, *Analytic Combinatorics*, Chapter I. Official materials: https://ac.cs.princeton.edu/10ogf/

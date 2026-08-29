# Numerical linear algebra: rotation/reflection choices

## Keep the semantic operation above the implementation

For a distinguished nonzero vector `x`, the common semantic task is to choose an orthogonal transform `Q` with

```text
Q x = ||x|| e1
```

possibly with the stronger requirement `det Q = +1`, and possibly with the requirement that the same `Q` be applied to accompanying data.

These requirements do not uniquely determine an algorithm.

## Givens / coordinate-plane rotations

A Givens rotation acts nontrivially on only two coordinates. In a chosen coordinate plane it has the form

```text
[ c  s]
[-s  c]
```

with `c^2 + s^2 = 1`, so it is orthogonal and has determinant `+1`.

A chain of Givens rotations can annihilate coordinates one at a time and rotate an `n`-vector to `e1`. This makes the sequence easy to inspect and provides a small cross-target conformance kernel, but the high-dimensional chain can expose ordering choices, zero/near-zero skips, dependency depth, branch behavior, and GPU divergence.

The standing tiny conformance cases from Computer Science #51 are:

```text
( 0.3, 0.4) -> (0.5, 0)
(-0.3, 0.4) -> (0.5, 0)
( 0.5, 0.0) -> (0.5, 0)
( 0.0, 0.0) -> finite, no NaN/Inf
```

Keep this 2D primitive distinct from the question of whether a long Givens chain is the best high-dimensional implementation.

## Householder reflection

A Householder reflector has the form

```text
H = I - 2 vv^T / (v^T v)
```

for nonzero `v`. It is orthogonal and has determinant `-1`.

With an appropriate `v`, one reflector can send a vector to a signed multiple of `e1`. Relative to a long coordinate-by-coordinate elimination, this can replace many local decisions with regular dense arithmetic. That may be good or bad depending on dimension, sparsity, reuse, memory layout, CPU/GPU target, and precision.

## Two reflections give a proper rotation

The product of two reflections has determinant `+1`. Therefore a semantic requirement for a proper rotation does not force a Givens chain: a Householder-style construction can be paired with a second reflection when necessary.

This is also the form that appears naturally in Hatcher's construction of `SO(n)`: `rho(v) = r(v) r(e1)`.

## Direct rotation in the data-defined plane

If the only geometric requirement is to take one distinguished direction to `e1`, the relevant action occurs in the 2-plane spanned by that direction and `e1`; the orthogonal complement can be fixed. This gives another proper-rotation construction that should remain visible alongside Givens and Householder approaches.

## The planner questions

Do not pick a winner from names alone. Candidate selection may depend on:

- dimension;
- actual sparsity/density and other structure;
- whether the transform is used once or replayed across a batch;
- whether the full transform must be retained;
- whether determinant `+1` is semantically required;
- numerical tolerance and available precision;
- CPU versus GPU target;
- branch predictability / lane divergence;
- dependency depth and available parallelism;
- register pressure, spills, and memory traffic;
- code size and compile time;
- whether a plan can be computed once and replayed branchlessly.

The important split is:

1. Which candidate algorithms are mathematically correct for the requested transformation?
2. Which observable facts are sufficient to select among them on a particular workload and target?

## Reading trail

Lloyd N. Trefethen and David Bau III, *Numerical Linear Algebra* (SIAM, 1997) is the central numerical-linear-algebra reference already recorded in Computer Science #54. Relevant topics include orthogonal matrices, Householder triangularization, its numerical stability, and comparisons with Givens rotations.

- Trefethen's book page: https://people.maths.ox.ac.uk/trefethen/text.html
- Computer Science #51: https://github.com/walnut-burgundy/computer-science/issues/51
- Computer Science #53: https://github.com/walnut-burgundy/computer-science/issues/53
- Computer Science #54: https://github.com/walnut-burgundy/computer-science/issues/54

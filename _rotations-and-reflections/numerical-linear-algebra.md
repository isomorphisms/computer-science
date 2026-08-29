# Numerical linear algebra: rotation/reflection choices

This is the canonical note for the pointwise alignment operation and its numerical algorithm families.

## State the semantic request before choosing a matrix

For

```text
d = x - y in R^n,
```

the common request is to choose an orthogonal transform `Q` satisfying

```text
Q d = ||d|| e1,
```

possibly with the stronger requirement `det(Q) = +1`, and possibly with the requirement that the same `Q` be applied to accompanying data.

This is an **underdetermined alignment request**. It is not the same problem as factoring an already specified matrix `Q`. Many transforms can satisfy the request, and the planner may choose among them. If `Q` is already prescribed, its exact factorization properties must be respected instead.

## Degenerate cases are part of the contract

The operation is total only after these cases are explicit.

### `d = 0`

Zero has no distinguished direction. The canonical convention for this study is:

```text
status: zero_direction
aligned_value: 0
transform: I
```

This preserves the zero vector, is finite, has determinant `+1`, and gives accompanying data a deterministic no-op transform. Implementations must detect this case before normalization.

### `d / ||d|| = e1`

The identity already satisfies the request.

### `d / ||d|| = -e1`

The data determines only one line, not a 2-plane. When `n >= 2`, choose and record a unit vector `w` perpendicular to `e1`, rotate by `π` in `span(e1, w)`, and fix its orthogonal complement. This is a proper rotation and maps `-e1` to `e1`, but the choice of `w` is additional policy or data.

When `n = 1`, `SO(1)` contains only the identity, so no proper rotation maps a negative scalar to its positive norm. An orientation-reversing transform can do so if the semantic request permits it.

These are not numerical annoyances to hide. The antiparallel case is exactly where a supposedly data-defined rotation plane stops being data-defined.

## Givens / coordinate-plane rotations

A Givens rotation acts nontrivially on only two coordinates. In a chosen coordinate plane it has the form

```text
[ c  s]
[-s  c]
```

with `c^2 + s^2 = 1`, so it is orthogonal and has determinant `+1`.

A chain of Givens rotations can annihilate coordinates one at a time and align a nonzero `n`-vector with `e1`. This makes the sequence easy to inspect and provides a small cross-target conformance kernel, but the high-dimensional chain can expose ordering choices, zero/near-zero skips, dependency depth, branch behavior, and GPU divergence.

The standing tiny conformance cases from Computer Science #51 are:

```text
( 0.3, 0.4) -> (0.5, 0)
(-0.3, 0.4) -> (0.5, 0)
( 0.5, 0.0) -> (0.5, 0)
( 0.0, 0.0) -> finite, no NaN/Inf, identity/no-op plan
```

Keep this 2D primitive distinct from the question of whether a long Givens chain is the best high-dimensional implementation.

## Householder reflection

A Householder reflector has the form

```text
H = I - 2 vv^T / (v^T v)
```

for nonzero `v`. It is orthogonal and has determinant `-1`.

Away from the identity case, a suitable reflector maps a nonzero vector to a vector of equal norm on the `e1` axis. Stable numerical routines commonly choose the sign of that target to avoid cancellation when constructing `v`. If the semantics requires the specifically positive target `||d|| e1`, the sign correction is part of the plan rather than something to omit from the contract.

Relative to coordinate-by-coordinate elimination, a reflector can replace many local decisions with regular dense arithmetic. That may be good or bad depending on dimension, sparsity, reuse, memory layout, CPU/GPU target, and precision.

## Two reflections and proper alignment

The product of two hyperplane reflections has determinant `+1`.

For the underdetermined vector-alignment request in dimension at least two, a reflection that performs the alignment can be composed with a second reflection that fixes the target axis. Thus a proper alignment does not force a Givens chain.

This statement is deliberately scoped. An arbitrary **prescribed** `Q in SO(n)` need not be a product of only two reflections. Its minimal reflection count is `rank(I - Q)`, which can be `4`, `6`, or larger. See [`cheap-invariants-and-parity.md`](cheap-invariants-and-parity.md).

Hatcher's construction uses the same elementary bridge between reflections and proper rotations: `rho(v) = r(v) r(e1)`.

## Direct rotation in the data-defined plane

For a nonzero direction not parallel or antiparallel to `e1`, the required action can be confined to

```text
span(d, e1),
```

with the orthogonal complement fixed. This gives a proper simple rotation.

The parallel case reduces to the identity. The antiparallel case requires the additional perpendicular direction described above; `span(d, e1)` is then only one-dimensional and cannot supply a proper rotation plane by itself.

A direct-plane representation and a two-reflection representation may describe the same simple rotation. They remain separate **implementation representations**, not necessarily separate mathematical families: one stores a plane/basis and angle-like data, while the other stores reflector normals and scalars. The planner should compare their actual application and storage costs without double-counting them as unrelated possibilities.

## The planner questions

Do not pick a winner from names alone. Candidate selection may depend on:

- whether the input is zero, parallel, antiparallel, or generic;
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

1. Which transforms satisfy the semantic request?
2. Which representations and algorithms correctly realize those transforms?
3. Which observable facts are sufficient to select among them for a workload and target?

## Reading trail

Lloyd N. Trefethen and David Bau III, *Numerical Linear Algebra* (SIAM, 1997) is the central numerical-linear-algebra reference already recorded in Computer Science #54. Relevant topics include orthogonal matrices, Householder triangularization, its numerical stability, and comparisons with Givens rotations.

- Trefethen's book page: https://people.maths.ox.ac.uk/trefethen/text.html
- Computer Science #51: https://github.com/walnut-burgundy/computer-science/issues/51
- Computer Science #53: https://github.com/walnut-burgundy/computer-science/issues/53
- Computer Science #54: https://github.com/walnut-burgundy/computer-science/issues/54

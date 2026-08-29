# `SO(n)`: rotations, reflections, and cohomology

This is the canonical topology note. Its subject is not how to factor one matrix, but whether a **family** of choices can be made continuously over a domain.

## Hatcher's reflection construction is directly relevant

In Section 3D of Allen Hatcher's *Algebraic Topology*, for a nonzero vector `v in R^n`, let `r(v)` denote reflection across the hyperplane orthogonal to `v`. Since a reflection has determinant `-1`, Hatcher considers

```text
rho(v) = r(v) r(e1),
```

which lies in `SO(n)`.

Rescaling `v` by any nonzero scalar leaves its reflecting hyperplane unchanged, so the actual parameter is the unoriented one-dimensional subspace `[v]`. This gives a map

```text
RP^(n-1) -> SO(n).
```

Hatcher multiplies these elementary two-reflection rotations to obtain a cellular map:

```text
RP^(n-1) x RP^(n-2) x ... x RP^1 -> SO(n).
```

Proposition 3D.1 identifies the resulting product maps as characteristic maps for the cells of `SO(n)`.

This is a concrete reason to keep reflections and products of reflections near numerical rotation factorizations. It does not say that every element of `SO(n)` is a product of only two reflections; the displayed map uses products of many elementary factors.

## The one-direction-at-a-time recursion

Hatcher evaluates a rotation at the last basis vector:

```text
p : SO(n) -> S^(n-1)
p(A) = A en.
```

The definition of `rho` uses `e1`, while this evaluation uses `en`. These are consistent: `r(e1)` fixes `en`, since `e1` and `en` are orthogonal.

For a rotation `beta` that does not fix `en`, Hatcher chooses the unique

```text
v_beta in RP^(n-1) \ RP^(n-2)
```

such that

```text
rho(v_beta) en = beta en.
```

Then

```text
alpha_beta = rho(v_beta)^(-1) beta
```

fixes `en`, so `alpha_beta` lies in `SO(n-1)`, and

```text
beta = rho(v_beta) alpha_beta.
```

This resembles one-direction-at-a-time numerical elimination:

```text
put one direction in place
freeze it
solve the remaining lower-dimensional problem
```

The resemblance is useful, but Hatcher's construction is a cell decomposition, not a claim that this is the numerically best factorization order.

## The bundle and its actual exceptions

Evaluation gives the principal bundle

```text
SO(n-1) -> SO(n) -> S^(n-1).
```

For `n >= 3`, Hatcher explicitly records product decompositions in the exceptional cases

```text
SO(4) homeomorphic to S^3 x SO(3)
SO(8) homeomorphic to S^7 x SO(7),
```

while in the other cases the space is only a twisted product. The `n = 2` case is the elementary identity `SO(2) homeomorphic to S^1` with trivial fiber.

Consequently, the safe planning statement is not

```text
SO(n) is never a global product.
```

It is:

```text
Do not assume a global product or section.
Record n and the exact theorem scope;
the bundle is exceptional at n = 2, 4, and 8.
```

## Pointwise existence is different from a continuous family

For an individual nonzero vector or matrix, a correct alignment/factorization can be chosen. That does not imply that one rule can choose such data continuously over the entire parameter space.

A section of

```text
SO(n) -> S^(n-1)
```

would choose, continuously for every direction, a full oriented orthonormal frame extending that direction. This is a parallelizability question for the sphere, not merely the question of whether one tangent vector field exists.

Practical symptoms of a missing global choice can include multiple charts, sign conventions, singular cases, redundant parameters, and fallback paths. Before attributing a branch in a numerical routine to topology, however, identify the exact family, domain, and continuity requirement. A pointwise kernel with no continuity requirement does not acquire a topological obstruction merely because its values lie in `SO(n)`.

## Euler characteristic is only a first obstruction

For spheres,

```text
chi(S^(2k)) = 2
chi(S^(2k+1)) = 0.
```

The nonzero Euler characteristic of an even-dimensional sphere rules out a nowhere-zero tangent vector field. Odd-dimensional spheres do admit at least one such field.

But one nonvanishing field is much weaker than a full global frame. Vanishing Euler characteristic therefore does **not** trivialize the `SO(n-1)` bundle. Among positive-dimensional spheres, only `S^1`, `S^3`, and `S^7` are parallelizable, matching the `n = 2, 4, 8` product cases above.

This prevents an invalid compiler rule such as “odd sphere, therefore one global smooth factorization.” Dimension parity can tell the planner which theorem case to inspect; it does not by itself choose an algorithm.

## Where cohomology can enter

Cohomology is not a scheduler that says which Givens rotation to perform next. Its plausible role is to support precisely scoped family-level questions:

- whether a particular bundle admits a section;
- whether a proposed global parametrization must have singularities or identifications;
- whether characteristic classes obstruct a stated continuous choice;
- whether two families lie in distinct topological classes;
- whether projective reflection parameters introduce genuine mod-2 structure.

No such result should prune an algorithm until the planner records:

```text
parameter domain
requested continuity or smoothness
the exact bundle/map/family
the theorem and its hypotheses
the dimension range
```

## Hatcher / Agosto / Perez computations

Allen Hatcher maintains a separate page, **The Cohomology of `SO(n)`**, with computer-generated Bockstein/cohomology diagrams for `SO(5)` through `SO(12)`.

Credits given by the source:

- **M. A. Agosto and J. J. Perez** — computer-generated pictures and the Mathematica program;
- **Allen Hatcher** — commentary.

The source says the integral cohomology has only order-2 torsion and that the Bockstein computation recovers the integral picture from mod-2 information. It also contrasts the rapid growth of the mod-2 cohomology with the quadratic dimension `dim SO(n) = n(n-1)/2`.

These diagrams demonstrate computable global structure. They do not establish that cohomology improves a rotation kernel. The next scientific step would be to identify one concrete family-level planning decision that a checked invariant settles.

## Source pointers

- Allen Hatcher, *Algebraic Topology*, Section 3D, "The Cohomology of `SO(n)`": https://pi.math.cornell.edu/~hatcher/AT/ATch3.4.pdf
- Hatcher's `SO(n)` picture page: https://pi.math.cornell.edu/~hatcher/SO/SO.html
- Combined Agosto/Perez diagrams with Hatcher commentary: https://pi.math.cornell.edu/~hatcher/SO/SO%28n%29.pdf
- Canonical source and redistribution note: [`software-and-sources.md`](software-and-sources.md)

The external figures are linked, not copied into this repository.

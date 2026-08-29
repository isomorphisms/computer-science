# Rotations and reflections

This branch collects the mathematical and numerical-linear-algebra material behind high-dimensional alignment, reflection/rotation factorizations, and the question of what can be decided before lowering a transform to branches, vectors, instructions, or shaders.

The motivating semantic operation is deliberately algorithm-neutral:

```text
d = x - y
align d with e1
preserve norm
say whether determinant +1 is required
optionally carry the same transform along to other values
```

Do not assume in advance that Givens, Householder, two reflections, or another construction wins. The point is to keep the semantic goal visible while collecting enough mathematics and target evidence to choose later.

## Start here

- [`discussion-notes.md`](discussion-notes.md) — the full readable record of the discussion: Givens, Householder, products of reflections, the `SO(n-1) -> SO(n) -> S^(n-1)` tower, sphere parity, coherent versus individual factorizations, cohomology, projective-space / `2`-torsion intuition, compiler-planning implications, software, sources, and the copyright boundary.

## Focused notes

- [`numerical-linear-algebra.md`](numerical-linear-algebra.md) — compact Givens / Householder / two-reflection / direct-plane summary and the existing compiler-planning questions.
- [`topology-and-cohomology.md`](topology-and-cohomology.md) — Allen Hatcher's reflection-built cell structure on `SO(n)`, the `SO(n-1) -> SO(n) -> S^(n-1)` viewpoint, and why cohomology may constrain coherent families of rotation/reflection plans without prescribing a finite sequence.
- [`software-and-sources.md`](software-and-sources.md) — Hatcher/Agosto/Perez sources, Trefethen & Bau, Macaulay2, Debian `cohomcalg`, and reuse/licensing notes.

## Existing Computer Science threads

- [#51 — Givens rotations as a cross-target shader conformance kernel](https://github.com/walnut-burgundy/computer-science/issues/51)
- [#53 — choose high-dimensional transform algorithms from evidence](https://github.com/walnut-burgundy/computer-science/issues/53)
- [#54 — ask mathematics before registers](https://github.com/walnut-burgundy/computer-science/issues/54)
- [Coxeter #5 — Householder reflections for high-dimensional alignment](https://github.com/isomorphismes/coxeter/issues/5)

## Copyright boundary for Hatcher material

The public electronic edition of Allen Hatcher's *Algebraic Topology* is free to download, but its copyright notice says that single paper/electronic copies may be made for noncommercial personal use and that all other rights are reserved. That is not a license to vendor the book, its pages, or its figures into this public repository.

The separate Hatcher `SO(n)` picture/commentary PDF credits the computer-generated pictures to **M. A. Agosto and J. J. Perez** and the commentary to **Allen Hatcher**. I found no separate redistribution license on that page or PDF. Therefore this branch links to and summarizes the material rather than copying the PDF or its figures.

If explicit redistribution permission is obtained later, direct figures can be added with their original credits and source metadata. Until then, keep figure pointers as links rather than repository copies.

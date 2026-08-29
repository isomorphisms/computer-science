# Rotations and reflections

This directory collects the mathematical and numerical-linear-algebra material behind high-dimensional alignment, reflection/rotation factorizations, and the question of what can be decided before lowering a transform to branches, vectors, instructions, or shaders.

The motivating semantic operation is deliberately algorithm-neutral:

```text
d = x - y
align d with e1
preserve norm
say whether determinant +1 is required
optionally carry the same transform along to other values
```

Do not assume in advance that Givens, Householder, two reflections, or another construction wins. The point is to keep the semantic goal visible while collecting enough mathematics and target evidence to choose later.

The request above is not yet a fully specified matrix. Keep three levels distinct:

1. a **pointwise alignment request**, which leaves many correct transforms available;
2. a **specified orthogonal transform** `Q`, whose determinant, fixed subspace, and exact reflection length are properties of that particular `Q`;
3. a **family of choices** varying over a parameter space, where continuity, charts, bundle sections, and topological obstructions become relevant.

Facts valid at one level must not be silently promoted to another. In particular, two reflections suffice for the underdetermined proper-alignment request in dimension at least two, but not for every prescribed element of `SO(n)`.

The alignment contract must also retain its degenerate cases: `d = 0` has no direction, and a direction antiparallel to `e1` does not determine its own rotation plane. [`numerical-linear-algebra.md`](numerical-linear-algebra.md) records the explicit conventions.

## Start here

- [`discussion-notes.md`](discussion-notes.md) — the readable narrative record of the discussion. It preserves the line of thought, but the focused notes below are canonical when precise claims or maintenance details differ.

## Focused notes

- [`numerical-linear-algebra.md`](numerical-linear-algebra.md) — compact Givens / Householder / two-reflection / direct-plane summary and the existing compiler-planning questions.
- [`topology-and-cohomology.md`](topology-and-cohomology.md) — Allen Hatcher's reflection-built cell structure on `SO(n)`, the `SO(n-1) -> SO(n) -> S^(n-1)` viewpoint, and why cohomology may constrain coherent families of rotation/reflection plans without prescribing a finite sequence.
- [`cheap-invariants-and-parity.md`](cheap-invariants-and-parity.md) — separate request, selected-transform, and family facts; keep one canonical field for equivalent invariants; derive parity; and justify the exact reflection-length formula.
- [`llm-mathematical-advisor.md`](llm-mathematical-advisor.md) — use an LLM as an advisory/orchestration layer over heterogeneous mathematical, symbolic, numerical, hardware, and empirical evidence without pretending every fact already has a compiler opcode.
- [`parallelism-from-factorizations.md`](parallelism-from-factorizations.md) — distinguish reordering permitted by commutation from concurrency justified by independent blocks, while also recording batch, reduction, and tree-composition opportunities.
- [`existing-project-connections.md`](existing-project-connections.md) — connects the planned semidirect/twisted-product keyboard vocabulary and the existing `isomorphismes/Cayley` mathematical toy-box project to this planning architecture without claiming the keyboard mapping is already implemented.
- [`software-and-sources.md`](software-and-sources.md) — distinguishes Hatcher's textbook from the separate two-page `SO(n)` handout and ten-page Agosto/Perez/Hatcher diagram collection; also records Trefethen & Bau, Macaulay2, Debian `cohomcalg`, direct source links, and reuse/licensing notes.

## Existing Computer Science threads

- [#51 — Givens rotations as a cross-target shader conformance kernel](https://github.com/walnut-burgundy/computer-science/issues/51)
- [#53 — choose high-dimensional transform algorithms from evidence](https://github.com/walnut-burgundy/computer-science/issues/53)
- [#54 — ask mathematics before registers](https://github.com/walnut-burgundy/computer-science/issues/54)
- [Coxeter #5 — Householder reflections for high-dimensional alignment](https://github.com/isomorphismes/coxeter/issues/5)

## Source and redistribution boundary

No Hatcher book pages, Agosto/Perez diagrams, or source PDFs are stored here. [`software-and-sources.md`](software-and-sources.md) is the canonical source and reuse note; the other files link to it instead of maintaining competing licensing summaries.

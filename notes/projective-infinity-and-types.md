# Projective infinity and the type system

## Motivation

Do not flatten mathematically meaningful structure into a handful of ad-hoc scalar sentinels merely because a conventional machine representation makes that easy.

The immediate example is projective infinity. For a single complex coordinate, the familiar identity

\[
\mathbf{CP}^1 \cong \mathbf C \cup \{\infty\}
\]

makes it easy to internalize the idea that compactification means adding one distinguished infinity. That is special to dimension one and to this compactification.

For compiler design, the important separation is:

- the type system should express the mathematical space and its invariants;
- the runtime representation should be chosen for efficient lowering;
- the ISA does not need a special notion of projective infinity.

This is different from treating IEEE `Inf` as the meaning of infinity.

## One-point compactification is not projective compactification

The one-point compactification of \(\mathbf C^n\) adds one point:

\[
(\mathbf C^n)^+ \cong S^{2n}.
\]

The standard projective compactification embeds

\[
\mathbf C^n \hookrightarrow \mathbf{CP}^n,
\qquad
(z_1,\ldots,z_n) \mapsto [1:z_1:\cdots:z_n].
\]

Its complement is the hyperplane

\[
\{[0:z_1:\cdots:z_n]\} \cong \mathbf{CP}^{n-1}.
\]

Hence, as a stratification,

\[
\mathbf{CP}^n = \mathbf C^n \sqcup \mathbf{CP}^{n-1}_{\infty}.
\]

For \(n=1\), the boundary is \(\mathbf{CP}^0\), a single point. That low-dimensional case hides what happens in higher dimension.

For \(\mathbf{CP}^2\), the points at infinity form an entire \(\mathbf{CP}^1\). Parallel affine lines with the same direction meet the same point of that line at infinity; changing direction changes the point at infinity.

For example, in the affine chart \([1:x:y]\), the family

\[
(x,y)=(t,mt+b)
\]

has projective limit

\[
[0:1:m]
\]

as \(t\to\infty\). The intercept \(b\) disappears while the direction \(m\) survives.

## The recursive boundary tower

With the standard coordinate flag, the boundary can be iterated:

\[
\mathbf{CP}^n
= \mathbf C^n \sqcup \mathbf{CP}^{n-1}
= \mathbf C^n \sqcup \mathbf C^{n-1} \sqcup \mathbf{CP}^{n-2}
= \cdots
= \mathbf C^n \sqcup \mathbf C^{n-1} \sqcup \cdots \sqcup \mathbf C^0.
\]

Equivalently there is a filtration

\[
\mathbf{CP}^0 \subset \mathbf{CP}^1 \subset \cdots \subset \mathbf{CP}^n.
\]

This is the familiar cell decomposition with one complex cell in each dimension \(0,\ldots,n\).

The recursive shape is useful for a dimension-indexed type system. It should not be confused with saying that \(\mathbf{CP}^n\) is topologically a coproduct of these pieces. The strata are glued: finite points can converge to the hyperplane at infinity. The particular nested coordinate flag also depends on a choice of homogeneous coordinates.

This is "stack-shaped" or recursive in the ordinary programming sense; it is not, by itself, a claim about stacks in the algebraic-geometric sense.

## Type-level interpretation

A compiler with dependent or indexed types can expose the geometry directly:

```text
CP       : ℕ → Type
Affine   : ℕ → Type
Infinity : (n : ℕ) → Type
```

with the mathematical relationship

```text
Infinity (n + 1)  ≃  CP n
```

for the standard projective compactification.

A value of `CP 2` which lies on the standard hyperplane at infinity therefore carries a `CP 1` worth of directional information. A value of `CP 3` at infinity carries a `CP 2`, and so on.

This suggests refinement/view operations rather than making `CP n` literally a recursive sum type. Morally:

```text
viewStandardChart : CP n →
  either (point in C^n)
         (proof/value in CP^(n-1) at infinity)
```

but the implementation should preserve the fact that these pieces belong to one glued topological space.

The type system can also distinguish:

- a projective point;
- a point proved to lie in a chosen affine chart;
- a point proved to lie on a chosen hyperplane at infinity;
- the dimension of each of those spaces;
- the chosen chart or coordinate flag when that choice matters.

## Low-level representation

The natural semantic representation is homogeneous coordinates:

\[
[z_0:z_1:\cdots:z_n],
\]

with

\[
(z_0,\ldots,z_n) \sim (\lambda z_0,\ldots,\lambda z_n)
\qquad
(\lambda\in\mathbf C^\times).
\]

The standard affine chart is `z₀ ≠ 0`. The standard hyperplane at infinity is `z₀ = 0`.

At the register/backend level this can remain ordinary data: \(n+1\) complex coordinates, a normalization convention or chosen chart when useful, and generated predicates/branches. Projective infinity does not require an IEEE-style sentinel and does not need to be understood independently by the ISA.

A compiler is free to use a chart tag, normalized coordinates, packed vectors, polar complex representation, or some other layout as an optimization. Those are representations of the projective type, not definitions of its semantics.

## Do not give CP^n fake arithmetic

Projectivization quotients out nonzero scalar multiplication. Ordinary addition and multiplication on coordinate tuples therefore do not automatically descend to well-defined operations on projective equivalence classes.

In particular, `CP n` should not be declared a vector space or field merely because it is represented using complex coordinates.

Operations should be admitted because they are mathematically well-defined on projective points. Important examples include projective transformations induced by invertible linear maps on homogeneous coordinates:

\[
[z] \mapsto [Az],
\]

with the effective transformation group \(\operatorname{PGL}(n+1,\mathbf C)\).

This is also a useful compiler/GPU boundary: the high-level type supplies quotient semantics and dimensional guarantees, while a backend can lower a projective transformation to dense or structured linear algebra on homogeneous coordinates.

## Special numerical states belong at the appropriate layer

R's `NA`, typed `NA`, `NULL`, `NaN`, and IEEE infinities are useful reminders that "not an ordinary finite scalar" covers several unrelated semantic cases.

For this project, most of those distinctions should not be invented at the register layer:

- absence belongs in an option/maybe/structural type;
- missing or unknown data belongs in an appropriate high-level data type;
- numerical indeterminacy can be represented deliberately and may also arise from hardware floating-point;
- projective infinity belongs to the geometry of the projective type.

The fact that hardware happens to expose IEEE `Inf` and `NaN` does not require the language to identify those bit patterns with every mathematical notion of infinity or undefinedness.

## Compiler consequences worth exploring

1. **Dimension-indexed storage.** `CP n` statically requires \(n+1\) homogeneous complex coordinates before representation optimization.
2. **Scaling invariance.** Tests and optimizations must respect `[z] = [λz]` for every nonzero scalar \(λ\).
3. **Chart transitions.** A value leaving one affine chart need not be treated as arithmetic overflow; another projective chart may be valid.
4. **Boundary refinements.** Proofs such as `z₀ = 0` or `z₀ ≠ 0` can refine the type/view and guide branching.
5. **Recursive dimensional structure.** The standard hyperplane at infinity of `CP (n+1)` is `CP n`, allowing dimension-directed code without erasing which infinity was reached.
6. **Representation independence.** Cartesian, polar, normalized homogeneous, SIMD-packed, and GPU layouts should implement the same projective semantics.
7. **Operation discipline.** The compiler should refuse convenient but ill-defined coordinatewise operations on quotient types unless an explicit chart-dependent operation was requested.

## Minimal tests

A first implementation should be able to state and test at least these facts:

- `[z]` and `[λz]` denote the same projective point for nonzero `λ`.
- `CP 1` has a single projective point at infinity in the standard affine compactification.
- the standard boundary of `CP 2` is `CP 1`, not one scalar infinity.
- `(t, mt+b)` and `(t, mt+c)` approach the same point `[0:1:m]` at infinity.
- changing `m` changes the point at infinity.
- changing projective chart preserves the projective point.
- a point crossing out of one affine chart can remain a perfectly valid projective value.
- operations exposed on `CP n` are invariant under homogeneous rescaling.

## General design principle

Mathematical types should retain the laws that make them mathematical objects. Complex numbers, projective spaces, quaternions, dates, and other structured domains should not be reduced to convenient storage tuples and then supplied with whatever operations happen to be easy to implement. Storage is an implementation choice; algebraic and geometric semantics belong in the type/interface contract.

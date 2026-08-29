# `SO(n)`: rotations, reflections, and cohomology

This note records the topology connection without pretending that cohomology itself outputs an instruction sequence.

## Hatcher's reflection construction is directly relevant

In Section 3D of Allen Hatcher's *Algebraic Topology*, for a nonzero vector `v in R^n`, let `r(v)` denote reflection across the hyperplane orthogonal to `v`. Since a reflection has determinant `-1`, Hatcher considers the composition

```text
rho(v) = r(v) r(e1)
```

which lies in `SO(n)`.

Because `v` and `-v` determine the same reflecting hyperplane, `rho(v)` depends only on the line spanned by `v`. This gives a map

```text
RP^(n-1) -> SO(n).
```

Hatcher then multiplies these elementary two-reflection rotations to obtain a cellular map

```text
RP^(n-1) x RP^(n-2) x ... x RP^1 -> SO(n),
```

and Proposition 3D.1 uses these products to construct a CW structure on `SO(n)`.

That is a strong reason to keep reflections and products of reflections in the same notebook as numerical rotation factorizations: the topology of `SO(n)` is being built from exactly such elementary pieces.

## The one-direction-at-a-time recursion

Hatcher also studies evaluation at the last basis vector:

```text
p : SO(n) -> S^(n-1)
p(A) = A en.
```

Given a rotation `beta` that does not fix `en`, one can choose an elementary `rho(v_beta)` that sends `en` to `beta en`. Then

```text
alpha_beta = rho(v_beta)^(-1) beta
```

fixes `en`, so `alpha_beta` lies in `SO(n-1)`, and

```text
beta = rho(v_beta) alpha_beta.
```

This is topologically very close to the numerical idea:

```text
put one direction in place
freeze it
solve the remaining lower-dimensional rotation problem
repeat
```

It is therefore reasonable to compare this tower with coordinate-by-coordinate Givens elimination, Householder alignment, and other `x -> ||x|| e1` constructions.

## `SO(n)` is generally twisted, not one global coordinate chart

The relevant bundle has the familiar form

```text
SO(n-1) -> SO(n) -> S^(n-1).
```

Hatcher notes that, apart from special cases, `SO(n)` is not simply a direct product `S^(n-1) x SO(n-1)` but a twisted product.

For planning, the useful distinction is:

```text
Can every individual matrix be factored?
```

versus

```text
Can one choose those factorizations continuously and coherently as the matrix varies?
```

The first is mainly linear algebra. The second is topology. A numerical routine that needs sign conventions, pivot/order choices, singular cases, or branch cuts may sometimes be exposing a genuine failure of one global continuous choice rather than merely a poor implementation.

## Where cohomology enters

Cohomology should not be treated as a scheduler that says which Givens rotation to perform next. Its plausible relevance is global:

- distinguish topological structure that cannot be removed by changing coordinates;
- detect obstructions to globally continuous choices of frames, decompositions, or sections;
- classify/equate families of choices;
- tell us when a single global parametrization must develop singularities or identifications;
- expose `Z/2` phenomena naturally associated with reflections/projective choices.

The parity of the spheres in the tower can matter to these global questions. For example, even and odd spheres have different Euler characteristics, and this affects the existence of certain global nonvanishing fields/sections. Do not turn that observation directly into a machine-code heuristic; treat it as a signal to ask which continuous choice the planner is implicitly trying to make.

## Hatcher / Agosto / Perez computations

Allen Hatcher maintains a separate page, **The Cohomology of `SO(n)`**, with computer-generated Bockstein/cohomology diagrams for `SO(5)` through `SO(12)`.

Credits given by the source:

- **M. A. Agosto and J. J. Perez** — computer-generated pictures / Mathematica program;
- **Allen Hatcher** — commentary.

The note emphasizes that the integral cohomology has `2`-torsion and that the mod-2 cohomology is easier to describe; the diagrams encode the Bockstein information needed to recover the integral picture. The source also remarks that the size of the mod-2 cohomology grows rapidly with `n` even though `dim SO(n) = n(n-1)/2` grows quadratically.

For this project, these diagrams are not evidence that cohomology will optimize a rotation kernel. They are evidence that the global space of rotations has substantial computable structure that should be checked before assuming every planning problem is merely a local sequence of plane rotations.

## Source pointers

- Allen Hatcher, *Algebraic Topology*, Section 3D, "The Cohomology of `SO(n)`": https://pi.math.cornell.edu/~hatcher/AT/ATch3.4.pdf
- Hatcher's `SO(n)` picture page: https://pi.math.cornell.edu/~hatcher/SO/SO.html
- Combined Agosto/Perez diagrams with Hatcher commentary: https://pi.math.cornell.edu/~hatcher/SO/SO%28n%29.pdf

### Figure pointers, not repository copies

The combined `SO(n)` PDF puts the `SO(7)` Bockstein diagram on its first page and the generated `SO(5)` through `SO(12)` diagrams on the following pages. Section 3D of the book contains the reflection construction and the small geometric picture used in the proof of the cell decomposition around book pages 294-296.

These figures are linked rather than copied here because the book's reuse notice does not authorize public redistribution of its pages/figures, and no separate redistribution license was found for the `SO(n)` diagram PDF.

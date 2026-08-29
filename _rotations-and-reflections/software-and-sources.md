# Software and source references

## Allen Hatcher / M. A. Agosto / J. J. Perez

Primary material for the topology side:

- Allen Hatcher, *Algebraic Topology*: https://pi.math.cornell.edu/~hatcher/AT/ATpage.html
- Section 3D material containing the reflection-built CW structure on `SO(n)`: https://pi.math.cornell.edu/~hatcher/AT/ATch3.4.pdf
- Hatcher's separate **The Cohomology of `SO(n)`** page: https://pi.math.cornell.edu/~hatcher/SO/SO.html
- Hatcher's short two-page explanatory handout: https://pi.math.cornell.edu/~hatcher/SO/comments.pdf
- Combined ten-page `SO(n)` picture/commentary PDF: https://pi.math.cornell.edu/~hatcher/SO/SO%28n%29.pdf
- One-page `SO(5), SO(6), SO(7)` diagram sheet: https://pi.math.cornell.edu/~hatcher/SO/SO%285%2C6%2C7%29.pdf

This **separate `SO(n)` material is the short printout/diagram collection we were remembering**, not merely pages from Hatcher's textbook.

The combined PDF explicitly says:

- **M. A. Agosto and J. J. Perez** — computer-generated pictures;
- **Allen Hatcher** — commentary.

Hatcher's `SO(n)` web page says Agosto and Perez wrote the **Mathematica program** used to compute/draw the Bockstein diagrams for `SO(5)` through `SO(12)`.

The two-page `comments.pdf` explains how to read the `SO(7)` diagram. The ten-page combined PDF starts with the same explanatory material and then includes the generated diagrams. The one-page `SO(5),SO(6),SO(7)` PDF is useful when we want a compact direct comparison without the full collection.

Hatcher also exposes individual/multi-group diagram PDFs and an `SO(12)` GIF from the `SO(n)` page. Prefer links to those originals while reuse permission is unresolved.

### What the diagram records

The dots are mod-2 cohomology basis classes by degree, while edges record nonzero Bockstein homomorphisms. Hatcher explains that the integral cohomology can be reconstructed from this information: nontorsion corresponds to `Ker beta / Im beta` and the order-2 torsion corresponds to `Im beta`.

The pictures for the larger groups are arranged so that Poincare duality appears as a 180-degree rotational symmetry of the diagram.

### Reuse / picture status

For the **book**, the copyright notice is explicit:

```text
Copyright © 2002 Cambridge University Press.
Single paper or electronic copies for noncommercial personal use may be made
without explicit permission. All other rights reserved.
```

That does **not** authorize copying book pages or book figures into this public Git repository.

The separate `SO(n)` page, `comments.pdf`, diagram PDFs, and combined Agosto/Perez/Hatcher PDF do not show a separate redistribution license that I could find. The fact that they are freely downloadable is not by itself permission to republish them.

Therefore this repository currently:

- links directly to the original Hatcher/Agosto/Perez diagrams;
- records precise page/source pointers;
- explicitly credits Agosto, Perez, and Hatcher;
- does **not** copy or rehost the original figures.

If explicit permission or a compatible license is found later, the direct images can be added with the original credits and source metadata. Until then, linking to the original one-page/ten-page PDFs gives immediate access to the actual pictures without guessing at redistribution rights.

## Trefethen & Bau

Lloyd N. Trefethen and David Bau III, *Numerical Linear Algebra*, SIAM, 1997.

- Author/book page: https://people.maths.ox.ac.uk/trefethen/text.html

Use it here for the numerical-linear-algebra side: orthogonal matrices, Householder triangularization and stability, Givens rotations, and comparisons between orthogonal transformation strategies.

## Macaulay2

Macaulay2 is a research system for algebraic geometry and commutative algebra, created by Daniel R. Grayson and Michael E. Stillman. It includes homological-algebra facilities and computations involving cohomology in its algebraic-geometric domain.

- Project: https://macaulay2.com/
- Debian stable package: https://packages.debian.org/trixie/macaulay2
- Introductory homology/Ext/Tor/cohomology material: https://macaulay2.com/doc/Macaulay2/share/doc/Macaulay2/BeginningMacaulay2/html/index.html

On Debian stable the package name is:

```sh
apt install macaulay2
```

For this project Macaulay2 is relevant as an example of the broader rule: before implementing symbolic/group/algebraic machinery ourselves, check mature computer-algebra systems and record exactly what question they can and cannot answer.

It is **not** the program Hatcher used for the Agosto/Perez `SO(n)` pictures; Hatcher says those were produced by a Mathematica program.

## CohomCalg on Debian

Debian also packages **cohomCalg**:

- Debian package/search material identifies it as `sheaf cohomology of line bundles on toric varieties`.
- Macaulay2 has a `CohomCalg` interface/package around that specialized engine.

Useful source pointers:

- Debian package search/source relation: https://packages.debian.org/source/stable/macaulay2
- Macaulay2 `CohomCalg` interface documentation: https://macaulay2.com/doc/Macaulay2/share/doc/Macaulay2/CohomCalg/html/index.html
- `cohomCalg` method documentation: https://macaulay2.com/doc/Macaulay2/share/doc/Macaulay2/CohomCalg/html/_cohom__Calg.html

On Debian where the package is available:

```sh
apt install cohomcalg
```

Important scope distinction: cohomCalg computes **sheaf cohomology of line bundles / toric divisors on toric varieties**. It is not a general singular-cohomology calculator for `SO(n)`. Macaulay2 supplies an interface to it, which is useful prior art for connecting a CAS to a specialized cohomology engine, but neither should be cited as having reproduced the Hatcher/Agosto/Perez `SO(n)` computation unless we actually build and verify such a calculation.

## Useful experiment boundary

A sensible later experiment is to separate three things:

1. Hatcher/Agosto/Perez as the checked mathematical target for small `SO(n)` cohomology/Bockstein data;
2. a CAS or small exact program that can independently reproduce some tractable part of that target;
3. the rotation/reflection planner, which consumes only structural facts shown to simplify a real planning decision.

Do not feed a large cohomology computation into the compiler merely because it exists. First identify a concrete selection/continuity/equivalence question that the computation settles.

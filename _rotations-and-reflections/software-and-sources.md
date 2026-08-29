# Software and source references

## Allen Hatcher / M. A. Agosto / J. J. Perez

Primary material for the topology side:

- Allen Hatcher, *Algebraic Topology*: https://pi.math.cornell.edu/~hatcher/AT/ATpage.html
- Section 3D material containing the reflection-built CW structure on `SO(n)`: https://pi.math.cornell.edu/~hatcher/AT/ATch3.4.pdf
- Hatcher's separate **The Cohomology of `SO(n)`** page: https://pi.math.cornell.edu/~hatcher/SO/SO.html
- Hatcher's short two-page explanatory handout: https://pi.math.cornell.edu/~hatcher/SO/comments.pdf
- Combined ten-page `SO(n)` picture/commentary PDF: https://pi.math.cornell.edu/~hatcher/SO/SO%28n%29.pdf
- One-page `SO(5), SO(6), SO(7)` diagram sheet: https://pi.math.cornell.edu/~hatcher/SO/SO%285%2C6%2C7%29.pdf

This **separate `SO(n)` material is a short printout/diagram collection**, not merely pages from Hatcher's textbook.

The combined PDF explicitly says:

- **M. A. Agosto and J. J. Perez** — computer-generated pictures;
- **Allen Hatcher** — commentary.

Hatcher's `SO(n)` web page says Agosto and Perez wrote the **Mathematica program** used to compute/draw the Bockstein diagrams for `SO(5)` through `SO(12)`.

The two-page `comments.pdf` explains how to read the `SO(7)` diagram. The ten-page combined PDF starts with the same explanatory material and then includes the generated diagrams. The one-page `SO(5),SO(6),SO(7)` PDF provides a compact direct comparison without the full collection.

Hatcher also exposes individual/multi-group diagram PDFs and an `SO(12)` GIF from the `SO(n)` page. Prefer links to those originals while reuse permission is unresolved.

### What the diagram records

The dots are mod-2 cohomology basis classes by degree, while edges record the nonzero mod-2 Bockstein `beta: H^i(-; Z/2) -> H^(i+1)(-; Z/2)`. Hatcher explains how this determines the integral additive picture: the Bockstein homology `Ker beta / Im beta` tracks the nontorsion portion, while `Im beta` tracks the order-2 torsion.

For the larger groups, a half-turn about the diagram's center displays Poincare duality.

### Reuse / picture status

For the **book**, the official copyright notice permits single paper or electronic copies for noncommercial personal use and otherwise reserves rights:

- Copyright notice: https://pi.math.cornell.edu/~hatcher/AT/ATcopyright.html

This project has not identified permission there to republish book pages or figures in a public Git repository.

As checked on 2026-08-29, the separate `SO(n)` page, `comments.pdf`, diagram PDFs, and combined Agosto/Perez/Hatcher PDF show their credits but no separate redistribution license on the source page or in the PDFs. Free download by itself is not being treated as redistribution permission.

Therefore this repository currently:

- links directly to the original Hatcher/Agosto/Perez diagrams;
- records precise page/source pointers;
- explicitly credits Agosto, Perez, and Hatcher;
- does **not** copy or rehost the original figures.

If explicit permission or a compatible license is found later, the direct images can be reconsidered with the original credits and source metadata. Until then, linking to the original one-page/ten-page PDFs gives immediate access to the actual pictures without guessing at redistribution rights.

## Trefethen & Bau

Lloyd N. Trefethen and David Bau III, *Numerical Linear Algebra*, SIAM, 1997.

- Author/book page: https://people.maths.ox.ac.uk/trefethen/text.html

Use it here for the numerical-linear-algebra side: orthogonal matrices, Householder triangularization and stability, Givens rotations, and comparisons between orthogonal transformation strategies.

## Macaulay2

Macaulay2 is a research system for algebraic geometry and commutative algebra, created by Daniel R. Grayson and Michael E. Stillman. It includes homological-algebra facilities and computations involving cohomology in its algebraic-geometric domain.

- Project: https://macaulay2.com/
- Debian 13 (`trixie`) package: https://packages.debian.org/trixie/macaulay2
- Introductory homology/Ext/Tor/cohomology material: https://macaulay2.com/doc/Macaulay2/share/doc/Macaulay2/BeginningMacaulay2/html/index.html

On Debian 13 (`trixie`) the package name is:

```sh
apt install macaulay2
```

For this project Macaulay2 is relevant as an example of the broader rule: before implementing symbolic/group/algebraic machinery locally, check mature computer-algebra systems and record exactly what question they can and cannot answer.

It is **not** the program Hatcher used for the Agosto/Perez `SO(n)` pictures; Hatcher says those were produced by a Mathematica program.

## CohomCalg on Debian

Debian also packages **cohomCalg**:

- Debian package/search material identifies it as `sheaf cohomology of line bundles on toric varieties`.
- Macaulay2 has a `CohomCalg` interface/package around that specialized engine.

Useful source pointers:

- Debian `cohomcalg` package: https://packages.debian.org/trixie/cohomcalg
- Debian 13 (`trixie`) Macaulay2 source package showing the integration/build relation: https://packages.debian.org/source/trixie/macaulay2
- Macaulay2 `CohomCalg` interface documentation: https://macaulay2.com/doc/Macaulay2/share/doc/Macaulay2/CohomCalg/html/index.html
- `cohomCalg` method documentation: https://macaulay2.com/doc/Macaulay2/share/doc/Macaulay2/CohomCalg/html/_cohom__Calg.html

On Debian where the package is available:

```sh
apt install cohomcalg
```

Important scope distinction: cohomCalg computes **sheaf cohomology of line bundles / toric divisors on toric varieties**. It is not a general singular-cohomology calculator for `SO(n)`. Macaulay2 supplies an interface to it, which is useful prior art for connecting a CAS to a specialized cohomology engine, but neither should be cited as having reproduced the Hatcher/Agosto/Perez `SO(n)` computation unless this project actually builds and verifies such a calculation.

## Useful experiment boundary

A sensible later experiment is to separate three things:

1. Hatcher/Agosto/Perez as the checked mathematical target for small `SO(n)` cohomology/Bockstein data;
2. a CAS or small exact program that can independently reproduce some tractable part of that target;
3. the rotation/reflection planner, which consumes only structural facts shown to simplify a real planning decision.

Do not feed a large cohomology computation into the compiler merely because it exists. First identify a concrete selection/continuity/equivalence question that the computation settles.

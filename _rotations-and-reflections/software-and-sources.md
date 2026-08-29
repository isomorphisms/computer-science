# Software and source references

## Allen Hatcher / M. A. Agosto / J. J. Perez

Primary material for the topology side:

- Allen Hatcher, *Algebraic Topology*: https://pi.math.cornell.edu/~hatcher/AT/ATpage.html
- Section 3D material containing the reflection-built CW structure on `SO(n)`: https://pi.math.cornell.edu/~hatcher/AT/ATch3.4.pdf
- Hatcher's `SO(n)` cohomology picture page: https://pi.math.cornell.edu/~hatcher/SO/SO.html
- Combined `SO(n)` picture/commentary PDF: https://pi.math.cornell.edu/~hatcher/SO/SO%28n%29.pdf

The combined PDF explicitly credits:

- **M. A. Agosto and J. J. Perez** — computer-generated pictures;
- **Allen Hatcher** — commentary.

Hatcher's web page says Agosto and Perez wrote the Mathematica program used for the `SO(5)` through `SO(12)` Bockstein/cohomology pictures. Keep those credits attached whenever the computations are discussed.

### Reuse status

Hatcher's electronic *Algebraic Topology* is free to download under a limited copyright notice: single paper/electronic copies may be made for noncommercial personal use; all other rights are reserved. This does **not** authorize placing the book or its figures into this public Git repository.

I found no separate public redistribution license on the `SO(n)` page or combined diagram PDF. In the absence of such a license, this repository should link to and summarize those materials, not vendor them. Direct images can be added later if explicit permission or a compatible license is obtained.

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

- Debian stable package: https://packages.debian.org/trixie/cohomcalg
- Macaulay2 `CohomCalg` interface documentation: https://macaulay2.com/doc/Macaulay2/share/doc/Macaulay2/CohomCalg/html/index.html
- `cohomCalg` method documentation: https://macaulay2.com/doc/Macaulay2/share/doc/Macaulay2/CohomCalg/html/_cohom__Calg.html

On Debian stable:

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

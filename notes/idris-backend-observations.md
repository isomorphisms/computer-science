# Initial Idris backend observations — 2026-08-26

This is the first ComputerScience interpretation of the deterministic AICI
backend matrix. The raw facts live in `../compiler-backends/initial-observations.tsv`;
this note is an interpretation and can be wrong.

## Revisions observed

- `idric-arm-thumb:idric-ir-first-slice` at
  `7e98881997993b4b7188115e686267546db41693`
- `idris-arm-backend:main` at
  `663fe5fbdd22249e2ccfecf0ba5e4a43b24a6f85`
- `idris-shader-backend:main` at
  `7876b79058d6f59711151a21697369c1cf72df2f`
- AICI did not yet contain the `compiler_backends` probe tree on its then-current
  `main` (`e342fc1e58bde3e7bcb82875b66f721725a600e8`); its introduction is therefore
  itself a change the watcher should log after the AICI work lands.

## Discrete feature surface

| feature | Idriç ARM/Thumb | older Idris ARM | Idris GLSL ES |
| --- | ---: | ---: | ---: |
| add | 1 | 1 | 1 |
| multiply | 1 | 1 | 1 |
| subtract | 0 | 1 | 1 |
| divide | 0 | 1 | 1 |
| square root | 0 | 1 | 1 |
| indexed/fixed-array load form | 0 | 1 | 1 |
| comparison form | 0 | 0 | 1 |
| selection form | 0 | 0 | 1 |
| typed vector form | 0 | 0 | 1 |
| target-artifact validation | 1 | 1 | 1 |
| explicit no-external-runtime check | 1 | 1 | 0 |

A `0` means only that the exact deterministic source/test marker selected by
AICI is absent at this revision. It does not prove the semantic capability is
impossible or buggy.

## What seems significant

The two ARM lines have nearly the same target constraints: ARMv7-A, Thumb-2,
VFPv3-D16 and the Android softfp boundary. The newer Idriç line currently has
only copies, Float32 add, and Float32 multiply, while the older Idris ARM line
also has subtract, divide, square root, unary operations, word constants, and
Float32 buffer loads.

Because these two backends target essentially the same machine architecture,
those missing operations in the newer Thumb line should not be explained as
architecture requirements. The simpler explanation is implementation maturity:
the Idriç line is deliberately rebuilding a small first slice from the older
backend's proven design. If subtract/divide/sqrt/load remain absent after the
first slice is meant to reach parity, that would become an omission worth
investigating rather than a target distinction.

The GLSL backend differs more structurally. Its checked IR carries booleans,
integers, vectors, fixed arrays, comparisons, and selection because a fragment
shader is a different execution model. Its target check validates generated
GLSL rather than ELF/ARM object attributes. The absence of the ARM-style
`--no-undefined` or `nm -u` check is therefore not presently evidence of a
missing shader safety check; there is no freestanding ELF leaf with the same
linkage question.

One potentially important semantic difference is not yet captured by this
matrix: the ARM lines deliberately expose Float32 operations and softfp ABI
details, while the shader source surface currently uses Idris `Double` and
lowers to GLSL `float`. A later common executable probe should record actual
precision, NaN, infinity, signed-zero, and rounding behavior rather than infer
equivalence from operation names.

The shader backend already has comparisons and selection while both ARM
backends do not. The older ARM backend's own next milestone calls for
comparisons, Boolean `if`, and constrained looping. That makes this a useful
cross-backend gap to watch: it may be ordinary staged work, but ComputerScience
should not silently assume all numerical backends already support the same
control structure.

## Current Thumb verification status

The latest `idric-arm-thumb` PR #1 workflow at this baseline bootstrapped the
pinned Idriç compiler successfully, then failed while installing the compiler
API/libraries. The actual Thumb compile/object verification step was skipped.
Therefore a green AICI observation means only that the deterministic
observation code ran and described the source tree. It must not be reported as
a successful native backend build.

## Provenance note

The Idriç ARM/Thumb repository explicitly identifies the older
`idris-arm-backend` as reference code for the broader arithmetic/buffer subset.
That supports saying the newer line was derived from or rebuilt from that
existing implementation.

It does **not** establish the stronger claim that the older implementation was
necessarily written by a person without LLM assistance. Git history attributes
the older backend commits to the repository owner's Git identity, but commit
authorship metadata does not reveal how the code was produced. There is no
external upstream-human attribution in the evidence inspected here that would
justify a stronger statement.

## Next empirical layer

The present probes intentionally measure source/test surfaces because they can
run everywhere and cannot be blocked by the current compiler-install problem.
The next useful common fixture is an affine scalar calculation such as
`a*x+b`, followed by subtraction/division/square-root fixtures as backends admit
them. For CPU and GPU targets the emitted representation will differ, but
ComputerScience can still compare:

1. accepted source-level operation,
2. emitted target operation or target-language form,
3. artifact validation,
4. actual numerical outputs over the same edge-case input table,
5. code size and, only when measured on a real target, execution cost.

That would move the comparison from “the implementation has this discrete
shape” toward “the implementations demonstrably preserve the same numerical
question.”

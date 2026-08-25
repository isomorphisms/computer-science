# SURFER vertical slice

SURFER is the first intended implementation trial for ComputerScience and the initial single-user research environment for developing the compiler architecture.

## Existing implementation evidence

- The [Algebraic Variety Explorer mobile app](https://github.com/isomorphisms/algebraic-variety-explorer-mobile) is a working CPU-only Java renderer built from Christian Stussak's jsurf code. It is the behavioral and visual oracle, not the architecture to reproduce.
- The [Idris-to-GLSL ES backend](https://github.com/isomorphisms/idris-shader-backend) is real compiler work. It preserves fixed shader-array lengths and element types and compiles a bounded SURFER-style polynomial root-search capability test. That test is not a complete renderer or a robust real-root isolator.
- Neither repository implements the ComputerScience planner. The catalogs and prose in this repository do not implement it either.

## Goal

Carry a compact semantic renderer specification through one shared typed rendering plan, then through CPU and GPU lowerings, to results comparable with the Java oracle. Preserve dimension computations and relationships, shapes, ragged structure, maps/reductions, algebraic identities, and relevant error bounds until a target-specific representation is deliberately selected.

## Shared boundary

The CPU and GPU routes need a common typed rendering contract above their split. `RayKernel` is a possible working name, not an implemented format. C must not become that boundary.

A ragged semantic value may lower to offsets, padding plus an identity, specialization, several kernels, or rejection on a particular target. The existing GLSL backend's fixed-array restriction is one target constraint, not permission to rewrite the source semantics as fixed arrays.

## Initial target paths

- CPU: the first evidence-producing route may use Idris/Idriç through RefC-generated C, Android NDK/Clang, and an ARMv7 native library. C is optional disposable terminal output on this route; it is not the semantic IR. The installed ABI and usable features must be measured. A direct Thumb-2/NEON backend remains a later research candidate, not the first gate.
- GPU: preserve the shared typed rendering plan into a typed shader representation and lower it to GLSL ES for the Android driver. Reuse the implemented backend where its checked subset fits; robust root isolation and Android host integration remain concrete gaps.
- Host: CPU-side code may supply formulas, assets, uniforms, frame state, and result comparison for the GPU route without forcing shader computation through C.

The first CPU milestone may land before full GPU integration, but the SURFER vertical slice is not complete until both routes can be expressed, executed, and checked.

The generic renderer must not absorb Homotopy-specific S/T behavior. That belongs in the Homotopy layer.

## Evidence required

- a small semantic input with computable dimension expressions and relationships, not merely dimensions written as fixed numerals;
- one shared typed plan that retains the facts needed by both target paths;
- at least one executable CPU path and one executable GPU path;
- assumptions and conservative calculations for computation and data movement;
- raw measurements tied to the exact device, ABI, software stack, and revision;
- comparison with the oracle, including image/error criteria;
- an inspectable record of selected and rejected choices and the reasons for them.

For the nearest-opaque rendering contract, compare hit/miss, first-hit distance, and normal within stated tolerances. Require no unexplained silhouette holes, broken edges, or flicker; state color and lighting tolerances rather than requiring byte-identical images.

Writing catalogs or these records is preparatory work. The slice is complete only when the target paths execute, the results are checked, and the selection trace is reproducible.

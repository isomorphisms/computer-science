# FP16 / PowerVR progress — 2026-08-26

This note tracks the specific question: how close is the Idriç/Idris GLSL ES
backend to carrying real F16 semantics into a PowerVR shader on the ARMv7
Android target?

The important distinction is between declaring an F16 policy and actually
carrying F16 through the compiler. FP16 is a first-class semantic target here,
not merely a late optimization to apply after an F32 implementation is done.

## What exists now

On `idris-shader-backend:float-semantics-f16-f32`:

- `FloatWidth` explicitly distinguishes `F16` from `F32`.
- F16 selects GLSL ES `mediump`; F32 selects `highp`.
- portable GLES does not claim that `mediump` is exact binary16.
- the PowerVR profile explicitly records native mediump FP16 and vector FP16.
- the backend test suite now checks width distinction, scalar lowering, vec2,
  vec3, vec4, unsupported vector widths, portable-vs-PowerVR capability claims,
  and that introducing F16 policy does not silently demote an existing F32
  shader.
- the complete shader-backend check is green with 41 tests at the current
  FP16 branch head used for this note.

This is useful compiler policy and regression coverage. It is not yet proof of
an F16 shader dataflow.

## What is still missing

The checked shader IR still has width-erasing forms such as `TFloat`, `TVec n`,
and `AFloat`. It does not yet carry `F16` or `F32` as part of scalar, vector, or
array types. There are no explicit F16->F32 or F32->F16 conversion operations in
that IR, and the normal emitter still renders the existing value types as
plain GLSL `float`/`vecN` under the existing F32 production path.

`Shader.Source` also still exposes the old `Double`-shaped scalar vocabulary.
Therefore the current branch should not be described as compiling an Idriç F16
program end to end.

The next compiler milestone should be visible as several independent changes,
not one vague "FP16 support" flag:

1. scalar width carried in checked IR;
2. vector width carried independently of lane count;
3. fixed-array element width carried;
4. explicit F16<->F32 conversions;
5. width-aware emission;
6. source/API surface able to request F16 deliberately;
7. generated F16 shader validated by the GLES toolchain;
8. real PowerVR device compile/link/render evidence;
9. framebuffer comparison against the chosen numerical oracle.

## Soap is not current proof

The Soap `edric-surface-player` branch contains the intended Edriç -> GLSL ES ->
Android plumbing, but its latest checked workflow does not establish the path.
The Edriç-built GLSL compiler step succeeded; compilation of the `.idric`
surfaces to GLSL ES then failed, so the APK build and emulator stages were
skipped. Soap also uses the existing `Double` -> GLSL float path rather than an
F16 source/dataflow contract.

Soap is therefore useful integration scaffolding, not evidence that the FP16
PowerVR path works.

## Parallel AICI observations and tests

AICI's `compiler-backend-observations` branch now has a dedicated
`fp16_probes.tsv` surface. It observes both positive policy/test markers and the
currently absent implementation milestones. In particular it separately tracks:

- semantic F16/F32 declaration;
- F16 mediump and F32 highp policy;
- portable exact-F16 refusal;
- PowerVR native-F16 profile;
- the matching backend tests;
- scalar/vector/array width in IR;
- explicit conversions;
- width-aware emitter plumbing;
- source-level F16 exposure;
- a PowerVR framebuffer oracle in CI.

AICI also has a passing baseline contract rather than merely printing that
matrix. `fp16_expected.tsv` lists the FP16/PowerVR facts that are established
now, and `fp16_baseline.py` fails if one disappears, becomes unreadable, or
changes unexpectedly. Its own tests exercise the passing case plus missing,
regressed, and unreadable observations. The current AICI compiler-backend
observer run passes the general matrix, the FP16 matrix, and this baseline gate.

The not-yet-implemented IR, conversion, emitter, source-F16, and device-oracle
rows deliberately remain observations rather than gates. When one becomes a
real established capability, it should be promoted into the expected baseline
instead of relying on prose to remember that milestone.

## ComputerScience watcher

The ComputerScience backend watcher now follows the active FP16 shader branch
and the active AICI observer branch every six hours. It combines the ordinary
cross-backend feature matrix with the FP16/PowerVR matrix, diffs that against
the last recorded state, and logs a change packet when anything moves.

The interpretation prompt explicitly treats FP16 as a semantic design target
and warns against confusing policy/source markers with end-to-end device
evidence. The deterministic TSV remains authoritative; LLM commentary is only
an interpretation of the change.

The historical baseline is intentionally left untouched. The first watcher run
with the expanded FP16 surface should therefore record the new observations and
recent backend changes rather than retroactively rewriting the baseline.

## Current reading

The work is in a good architectural position but before the decisive compiler
slice. The policy has been named, PowerVR has been separated from generic GLES,
and the test/watch surfaces now make the missing pieces explicit. The next
meaningful advance is not more precision prose: it is making width a real part
of the typed shader dataflow and then driving a deliberately F16 fixture through
to an actual PowerVR framebuffer.

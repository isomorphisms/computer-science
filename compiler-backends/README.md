# Idris compiler-backend observations

This directory is the ComputerScience-side log for AICI's deterministic
compiler-backend probes.

The split is deliberate:

- AICI owns the conventional, repeatable measurement code and the probe matrix.
- ComputerScience records the initial state, remembers the last state it saw,
  notices changes in either the AICI probe surface or the backend revisions,
  and asks an LLM to interpret a change packet.
- LLM interpretation is commentary over checkable evidence. It does not replace
  the TSV observations and it does not turn an observed difference into a build
  verdict.

`initial-observations.tsv` and `initial-revisions.tsv` are immutable historical
baselines from 2026-08-26. `last-seen-observations.tsv` and
`last-seen-revisions.tsv` begin at the same state and are advanced by the
watcher after it logs a change. The FP16/PowerVR observation rows were added
after that initial baseline, so the first watcher run with the expanded AICI
matrix will deliberately log their introduction as a change.

The watcher currently follows the active development refs:

- AICI `compiler-backend-observations`, including both the general backend
  matrix and the dedicated FP16/PowerVR matrix;
- `idric-arm-thumb:idric-ir-first-slice` while PR #1 is the active
  implementation line;
- `idris-arm-backend:main`;
- `idris-shader-backend:float-semantics-f16-f32` while the FP16 work is active.

When those active lines merge, move the watched refs to `main` at the same time
rather than silently continuing to watch an abandoned feature branch.

The FP16 rows intentionally include both things that already exist and missing
milestones. In particular, policy/test markers are kept separate from scalar,
vector, and array width in IR; explicit conversions; width-aware emission;
source-level F16 selection; and real PowerVR framebuffer evidence. A zero is an
observation, not an automatic failure.

## LLM pass

The workflow builds a deterministic packet containing the old and new TSVs,
revision changes, and source diffs for changed backend refs. It then attempts a
non-interactive GitHub Copilot CLI pass and puts that interpretation in a
GitHub issue together with the raw diffs.

The interpretation prompt explicitly treats FP16 as a first-class semantic
target rather than a post-F32 optimization, and tells the LLM not to confuse a
policy/source marker with end-to-end device evidence.

For a personal repository, the Copilot CLI may require a repository secret
named `COPILOT_GITHUB_TOKEN` containing an appropriately scoped fine-grained
token. If that credential is absent or inference fails, the watcher still logs
the deterministic change packet and explicitly records that no LLM
interpretation was produced. Observation never depends on the LLM succeeding.

The watcher intentionally consumes AICI's `compiler_backends/probe.py` rather
than keeping a second implementation.

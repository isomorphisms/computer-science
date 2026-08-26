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
watcher after it logs a change.

The watcher follows:

- AICI `main`, but only treats changes to its `compiler_backends` tree as probe
  changes;
- `idric-arm-thumb:idric-ir-first-slice` while PR #1 is the active
  implementation line;
- `idris-arm-backend:main`;
- `idris-shader-backend:main`.

When the Thumb implementation moves to `main`, this file and the watcher should
move the watched ref at the same time.

## LLM pass

The workflow builds a deterministic packet containing the old and new TSVs,
revision changes, and source diffs for changed backend refs. It then attempts a
non-interactive GitHub Copilot CLI pass and puts that interpretation in a
GitHub issue together with the raw diffs.

For a personal repository, the Copilot CLI may require a repository secret
named `COPILOT_GITHUB_TOKEN` containing an appropriately scoped fine-grained
token. If that credential is absent or inference fails, the watcher still logs
the deterministic change packet and explicitly records that no LLM
interpretation was produced. Observation never depends on the LLM succeeding.

Merge the AICI probe PR before enabling this watcher on `main`, because the
watcher intentionally consumes AICI's `compiler_backends/probe.py` rather than
keeping a second copy.

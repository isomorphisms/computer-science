# ComputerScience

ComputerScience is an experimental architectural-planning project between semantic specification and concrete execution. It is intended to help choose and compose implementations using target facts, calculations, measurements, constraints, and programmer preferences, then leave behind an explicit plan that ordinary target-specific compilation can follow.

This repository currently contains two related kinds of material:

- top-level subject and hardware references, presently including Android input, GPU, analytic-combinatorics, and electronics; CPU/ISA catalogs follow the same top-level reference convention when added;
- the nested [`architecture-search/`](architecture-search/) area for the experimental planner, its evidence model, and end-to-end examples.

The top-level catalogs are intentionally not being moved merely to make the tree look uniform. Several were requested as independently browsable references. The `architecture-search/` directory is the boundary for the planner experiment.

## Current status

The repository is still in the implementation and evidence-gathering stage. Most material is prose. There is no working planner, constraint solver, autotuner, or compiler here yet, and a catalog entry does not count as an implementation.

The first intended vertical slice is SURFER. Other possible trials, including IB/eyebrowser and Field Mouse, remain exploratory until their interfaces and acceptance criteria are specified.

## Source discipline

- Separate semantic operations from algorithm variants and platform primitives.
- Keep provenance, assumptions, uncertainty, target/ABI facts, and failure observations.
- Do not infer an installed ABI or usable instruction set from a processor name alone.
- Treat LLM output as a proposal unless it is supported by checkable evidence.
- Record selected and rejected alternatives so a result can be inspected and replayed.
- Prefer small programs with explicit inputs and outputs; use shell or Grease composition unless evidence justifies fusion or a long-lived process.

The reconciled design rationale is in [`notes/architectural-compilation.md`](notes/architectural-compilation.md).

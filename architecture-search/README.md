# Architecture search

Status: prose scaffolding for the ComputerScience planner. No executable planner or selection trace exists here yet.

This directory holds the experimental part of ComputerScience. Its intended job is to turn semantic intent, target facts, constraints, and preferences into an explicit implementation plan with an evidence trail.

```text
semantic goal and preserved computable structure
  -> candidate algorithms
  -> compatible target primitives
  -> conservative calculations
  -> target-specific measurements
  -> selected and rejected plans with reasons
  -> reproducible target-specific lowering
```

The planner should not force every program into one process. Its output may be several small terminating programs connected by a shell or Grease. It may recommend fusion, persistence, or a service when evidence supports that choice, while preserving the logical component boundaries in the plan.

## Layer boundary

- Idriç/Edric should preserve semantic intent, dimension computations and relationships, shapes, ragged structure, and algebraic meaning through the stack. A dimension need not be a compile-time numeral: it may become known after an input is validated or another value is computed.
- ComputerScience should own comparison of algorithms, target primitives, architectural adverbs, and evidence.
- CPU and GPU paths should share a typed semantic rendering plan above target-specific lowering.
- C is one optional disposable terminal language for a CPU path. It is neither required nor the universal semantic IR, and it must not mediate the GPU path.
- GPU work should retain a typed mathematical/shader IR until target lowering, initially to GLSL ES for Android.

This is a working boundary, not a final language design. The existing GLSL backend's fixed typed arrays and rejection of dynamic shader arrays are target facts; they do not justify erasing ragged semantics before lowering.

## Implementation order

1. Use SURFER to define one small shared rendering contract and typed plan above the CPU/GPU split.
2. Execute and measure concrete lowerings. The first CPU route may use RefC-generated C as disposable output for Android NDK/Clang; a direct Thumb-2/NEON backend remains later research. The existing GLSL ES backend is partial implementation evidence, not a complete SURFER GPU renderer.
3. Record one reproducible selection trace with calculations, measurements, rejected paths, and oracle comparison.
4. Implement the smallest planner/chooser justified by that trace. Do not mistake catalog growth for progress on this step.

## Directories

- [`primitives/`](primitives/) describes operations a concrete target makes available.
- [`algorithms/`](algorithms/) describes semantic algorithms and their variants independently of one target.
- [`calculations/`](calculations/) keeps derived estimates and their assumptions.
- [`measurements/`](measurements/) keeps raw observations, including failures.
- [`planner/`](planner/) describes search inputs, decisions, and reproducible output.
- [`examples/`](examples/) defines vertical slices and their acceptance criteria.

## Open, not settled

- the concrete representation of goals and architectural adverbs;
- the division between compile-time, install-time, and run-time choices;
- whether an LLM acts as researcher, advisor, critic, question-asker, or not at all;
- how typed plans cross the boundaries between independently terminating programs;
- how much search is analytic, empirical, solver-driven, or human-guided.

An LLM may propose facts or alternatives, but it cannot be the unrecorded source of a decision. Inputs, calculations, observations, and choices must remain inspectable without trusting a conversation transcript.

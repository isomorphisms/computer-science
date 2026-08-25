# Architecture search

Status: text scaffolding for an implementation that does not exist yet.

This directory holds the experimental part of ComputerScience. Its intended job is to turn semantic intent, target facts, constraints, and preferences into an explicit implementation plan with an evidence trail.

```text
semantic goal and static facts
  -> candidate algorithms
  -> compatible target primitives
  -> conservative calculations
  -> target-specific measurements
  -> selected and rejected plans with reasons
  -> reproducible target-specific lowering
```

The planner should not force every program into one process. Its output may be several small terminating programs connected by a shell or Grease. It may recommend fusion, persistence, or a service when evidence supports that choice, while preserving the logical component boundaries in the plan.

## Layer boundary

- Idriç/Edric should preserve semantic facts such as computable dimensions, shapes, and ragged structure through the stack.
- ComputerScience should own comparison of algorithms, target primitives, architectural adverbs, and evidence.
- C may be disposable terminal CPU output; it is not the universal semantic IR.
- GPU work should retain a typed mathematical/shader IR until target lowering, initially to GLSL ES for Android.

This is a working boundary, not a final language design.

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

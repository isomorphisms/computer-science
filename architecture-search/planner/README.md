# Planner

The planner is intended to compare compatible algorithm and primitive combinations for a stated goal. It has not been implemented. The top-level catalogs, the directory skeleton under `architecture-search/`, the existing Idriç compiler, and the separate GLSL backend are not substitutes for a ComputerScience planner or chooser.

Its input should eventually make explicit:

- semantic requirements and preserved computable structure, including dimension expressions and relationships that may be resolved at different stages;
- target and workload facts;
- hard constraints;
- preferences or architectural adverbs;
- acceptable uncertainty and which questions require human judgment.

Its output should name the selected components and boundaries, list important rejected alternatives, explain the evidence and consequences, and freeze enough information for a reproducible rebuild. Unknown facts must remain unknown; an LLM suggestion is not a measurement.

Analytic pruning and cached failures should precede expensive benchmarking where possible. Pareto comparison is meaningful only after the target, workload, metrics, and constraints are fixed.

Implementation should follow evidence rather than precede it: first produce and measure the shared SURFER CPU/GPU paths, then record one manual selection trace, then implement only the planner operations that trace demonstrates are needed. A catalog record remains data for a future planner until executable code consumes, checks, or selects it.

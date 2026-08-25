# Planner

The planner is intended to compare compatible algorithm and primitive combinations for a stated goal. It has not been implemented.

Its input should eventually make explicit:

- semantic requirements and static facts;
- target and workload facts;
- hard constraints;
- preferences or architectural adverbs;
- acceptable uncertainty and which questions require human judgment.

Its output should name the selected components and boundaries, list important rejected alternatives, explain the evidence and consequences, and freeze enough information for a reproducible rebuild. Unknown facts must remain unknown; an LLM suggestion is not a measurement.

Analytic pruning and cached failures should precede expensive benchmarking where possible. Pareto comparison is meaningful only after the target, workload, metrics, and constraints are fixed.

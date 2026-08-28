# Examples

Examples are end-to-end acceptance tests for the architecture-search idea, not merely demonstrations of individual algorithms.

Each example should state:

- the semantic input and externally meaningful result;
- facts that must survive lowering;
- candidate implementation paths and target boundaries;
- the oracle, comparison method, and acceptable error;
- measurements to collect;
- the selected and rejected choices that the planner must explain.

SURFER is the first intended vertical slice. IB/eyebrowser is a committed second slice developed separately; it should test process boundaries, renderer selection, durable/cache separation, and resource-constrained work rather than being folded into SURFER. Field Mouse remains a possible later trial, not committed scope.

The [`search-workloads/`](search-workloads/) suite is the shared comparison case for filesystem, text, record, and biological-sequence search. It keeps tool semantics, corpus geometry, emitted machine evidence, and user/profile tradeoffs explicit across compiler backends.

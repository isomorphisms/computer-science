# Measurements

Keep empirical observations from real executions on real machines.

The default is not to benchmark an exhaustive matrix. When a candidate is run anyway, cache the observation so later searches can reuse it. One execution can be treated as one row carrying the machine and software identity, workload or input, chosen implementation, path through the decision tree, requested qualities or constraints, and observed results.

Aggregates, percentiles, and models can be derived later. Preserve the underlying observations so a summary table does not become false precision or erase which machine and choices produced a result.

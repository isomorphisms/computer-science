# Shared search-workload comparison suite

> **Status:** experiment specification. This is the authoritative shared note
> for compiler/backend adoption issues; it does not yet provide a benchmark
> harness, corpus download, or performance result.

Search is a useful ComputerScience vertical slice because familiar commands
combine semantic choices, tree or record traversal, algorithms, I/O policy,
machine lowering, and visible output. The suite supplies stable reference
questions for new compiler ideas without pretending that every tool performs
the same operation.

Companion notes:

- [`workload-matrix.md`](workload-matrix.md) defines tool lanes, result shapes,
  corpus geometries, and comparison parameters.
- [`biological-data.md`](biological-data.md) defines the required biological
  proxy and exact boundary cases.
- [`evidence-model.md`](evidence-model.md) defines calculations, measurements,
  attribution, prediction, and partial-order reporting.
- [`backend-adoption-issue.md`](backend-adoption-issue.md) is the deliberately
  small issue copied into each CPU/compiler repository.
- [`../../../notes/the-silver-searcher-review.md`](../../../notes/the-silver-searcher-review.md)
  reviews `ag` as a decomposable search system.

## The comparison object

The suite never asks only "is this optimization fast?" It asks:

> For this exact semantic lane, command/result shape, corpus geometry, target,
> implementation, and observation context, what changed, why, and for whom?

A compiler or backend proposal should be evaluated through this sequence:

```text
semantic lane and oracle
  -> workload/corpus descriptor
  -> candidate implementation and analytical prediction
  -> exact output comparison
  -> emitted-artifact inspection
  -> system and target measurements
  -> attribution and paired differentials
  -> Pareto/profile decision, including non-wins
```

## Required separation of concerns

1. **Semantics:** bytes, lines, filesystem entries, structured records, or
   biological sequences; literal or regex; boundary and normalization rules.
2. **Enumeration:** directories, selected files, rows, records, index hits, or
   an already-resident buffer.
3. **Matching/selection:** scalar, skip-based, bit-parallel, automaton, SIMD,
   index-assisted, or another checked realization.
4. **Projection:** Boolean, exit status, filename, record, count, positions,
   context, transformation, or all matching text.
5. **Physical realization:** reads/maps, decomposition, queues, threads,
   registers, spills, branches, tables, vector instructions, and output calls.

A change at one layer must not silently change another. In particular, a
faster quiet membership test is not evidence about printing all matches, and
a raw-byte motif scan is not an oracle for parsed FASTA records.

## Minimum adoption test for a compiler backend

Once that backend's ordinary byte slices, bounded loops, integer results, and
observable output gates are available:

1. select one fixed-literal lane and state its exact result contract;
2. run the small biological fixtures, including overlap and record-boundary
   cases, against a portable oracle;
3. compare at least a simple scalar implementation and one candidate the new
   compiler idea is supposed to improve;
4. predict loads, comparisons, branches, table/setup size, and bytes emitted;
5. inspect generated assembly/object code for registers, spills, branches,
   tables, vectorization, calls, and code size;
6. measure exact target/workload/output combinations and retain raw receipts;
7. report paired deltas and regressions, not just a winning average;
8. say where the idea is dominated, incomparable, or not applicable.

Then repeat on a source-tree shape and at least one deliberately contrasting
shape. A Linux-kernel-like tree is useful but cannot stand in for all filesystems.

## Adverbs and selection

Architectural adverbs such as `small_code`, `low_memory`, `streaming`,
`no_persistent_index`, `low_latency`, `throughput`, `battery_sparing`, or
`inspectable` identify priorities and constraints. They do not name an
algorithm. Different implementations of an apparently similar adverb can
affect users and workload strata differently; those effects remain visible in
the evidence record.

There is usually no honest total ordering. One candidate may reduce scan time
while increasing setup, code size, memory, energy, or dense-output latency.
The planner should retain the Pareto frontier and choose only after a declared
profile supplies constraints or preferences.

## Non-goals

- crowning one universal grep algorithm;
- calling all filesystem, text, record, and sequence operations `grep`;
- treating a conversationally appealing backend idea as evidence;
- comparing against intentionally weak baselines;
- using one histogram or elapsed-time scalar as a causal explanation;
- requiring every backend to implement the suite before its native language
  and execution gates exist.

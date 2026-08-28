# Evidence, prediction, attribution, and partial orders

The suite distinguishes four statements that are often collapsed into "fast":

1. **calculation:** a derived estimate under explicit assumptions;
2. **observation:** a measured result on an exact setup;
3. **prediction:** an expected result for another setup or workload, with an
   uncertainty range and stated model;
4. **decision:** a choice under declared constraints and preferences/adverbs.

Only the second may be definitive for the measured run. It still does not
establish an unqualified property of the algorithm.

## Analytical sheet before measurement

For each candidate, estimate or bound what is knowable on paper:

- bytes/records/entries considered and pruned;
- preprocessing, table, index, and compilation work;
- comparisons, loads/stores, shifts, masks, branches, calls, and syscalls;
- expected skip distance or candidates admitted under stated data assumptions;
- live state and likely register pressure;
- code, table/index, and working-set size;
- input and output transfer volume;
- parallel critical path and coordination work;
- crossover points in pattern length, corpus size, density, or query reuse.

Use intervals when an input is uncertain. The calculation should say which
constant or interaction the measurement is intended to learn.

## Minimum measurement identity

A reusable observation retains:

- source and compiler/backend revisions, build mode, flags, and dependencies;
- generated assembly/object and code/data size;
- processor/device, enabled ISA facilities, frequency/power policy, memory;
- operating system, ABI, filesystem/mount, storage, and relevant scheduler state;
- cache state and whether an index, JIT, or compiled pattern was already warm;
- corpus identity/hash plus its geometry descriptor;
- exact query, semantic lane, result shape, ordering, offset units, and sink;
- repetitions, run order/randomization, failures, timeouts, and uncertainty;
- correctness result and observed metrics.

The claim should be phrased accordingly: "this implementation took X under
this record," not "this implementation is fast."

## Sensors

Choose sensors according to the proposed mechanism. Useful observations include:

- elapsed, CPU, startup, steady-state, and tail latency;
- throughput in declared bytes, bases, records, or entries;
- cycles and retired instructions;
- branches and mispredictions;
- cache/TLB events and page faults;
- bytes requested/read/decoded and syscall counts;
- allocations, resident/peak memory, code and table/index size;
- queue/lock/scheduling work and per-worker imbalance;
- output records/bytes and time blocked on the sink;
- energy or thermal behavior when target evidence supports it;
- enumeration transitions: visited, pruned, opened, decoded, matched, emitted.

A counter is useful only when it can discriminate a proposed explanation or
identify an affected stratum.

## Why a histogram is not enough

A latency histogram describes a marginal distribution. By itself it does not
say which corpus/query/user profile produced a tail, whether the same case
regressed relative to baseline, or which mechanism changed.

Retain paired observations keyed by workload identity and stratify them by
relevant coordinates such as file/record size, density, pattern length,
output mode, cold/warm state, and target. Report:

- per-case and per-stratum deltas against the same baseline;
- joint metrics needed to distinguish I/O, matching, and output effects;
- counts and weights of affected cases/users, not only an aggregate mean;
- tail regressions and non-wins;
- an explicit causal hypothesis linked to discriminating sensors;
- residuals or cases the hypothesis does not explain.

This makes the result answer "why and for whom?" rather than only "what was
the shape of one metric?"

## Predictors and selectors

Candidate predictor inputs include pattern length/count, alphabet, record/file
distribution, match density, output mode, query reuse, compression, resident
index state, target facilities, and resource constraints. A predictor may be
an analytic bound, fitted model, rule, or inspected runtime selector.

Validate a predictor out of sample across named corpus/target strata. Report
calibration, uncertainty, abstentions, and decision regret against available
candidates. The selector's own setup, code size, branching, and failure policy
are part of the implementation.

## Partial order, not universal ranking

Compare candidates as vectors such as:

```text
(correctness domain,
 startup,
 steady-state latency/throughput,
 tail latency,
 memory,
 code/table/index size,
 energy,
 output behavior,
 inspectability,
 portability)
```

A candidate dominates another only within a declared context when it is no
worse on every required dimension and better on at least one. Otherwise keep
both on the Pareto frontier or mark their applicability domains as different.

An adverb/profile supplies constraints or preferences over this vector. It may
select differently for an ARM phone, a server, a media workstation, a
streaming sequencer, or a reproducible tiny compiler test. Record the selected
and rejected candidates with reasons; do not erase the frontier after choice.


# LLM mathematical advisory layer

The rotations/reflections study gives the Computer Science planner a use for an LLM broader than selecting among already-named numerical routines: connect heterogeneous, explicitly scoped evidence before a concrete algorithm is chosen.

The LLM is an advisor and proposal generator. It is not the component authorized to convert an unverified claim into a hard constraint or delete a correct candidate.

## Separate the objects under discussion

Every advisory pass must first identify which level a claim concerns:

```text
pointwise request
    choose some Q satisfying an alignment contract

specified transform
    analyze or factor this particular Q

family of choices
    choose Q(p) as p varies over a stated parameter domain
```

Examples of scope errors to reject:

- “two reflections suffice for this proper alignment request” does not imply that every prescribed `Q in SO(n)` has reflection length two;
- “this bundle has no global section” does not prevent choosing a transform for one input;
- “these factors commute” permits reordering but does not prove same-input parallel independence;
- “the sphere has Euler characteristic zero” does not supply a full global frame.

## Evidence ledger

The planner should preserve heterogeneous inputs whose eventual computational role may still be unknown:

- mathematical theorems and their hypotheses;
- symbolic/CAS results;
- numerical-linear-algebra knowledge;
- hardware and ABI facts;
- measured benchmark evidence;
- semantic requirements and user preferences;
- unresolved hypotheses and LLM suggestions.

Each entry needs its own status and provenance:

```text
claim
object_scope: request | input | transform | family | target | measurement
parameter/dimension scope
source / derivation / provenance
verification status
theorem | semantic requirement | measured fact | heuristic | suggestion
what decision it could affect
what would falsify or supersede it
```

A theorem, a measurement, a user preference, and an LLM hunch must never enter the ledger with the same status.

## Planning shape

```text
semantic request
  -> scoped evidence ledger
  -> LLM advisory pass
       identify possibly relevant constraints
       propose candidate transforms/representations
       flag apparent incompatibilities for checking
       identify missing information
       suggest explicit local charts or case splits
  -> deterministic checks / mathematical oracles
       confirm hard constraints
       reject only checked incompatibilities
  -> target-aware selection and measurement
  -> IR / branches / vectors / shaders / instructions
  -> execution evidence returned to the ledger
```

The ordering matters. The LLM may notice a possible contradiction; the deterministic stage decides whether its hypotheses actually match and whether pruning is justified.

## A correctly scoped topology object

Hatcher's Section 3D gives the bundle

```text
SO(n-1) -> SO(n) -> S^(n-1).
```

The precise planning input is dimension-scoped:

```text
FamilyConstraint:
    family: sections of SO(n) -> S^(n-1)
    dimension: n >= 2
    continuity_requirement: global
    statement:
        product/trivial cases at n = 2, 4, 8;
        twisted in the other cases
    source: Hatcher, Algebraic Topology, Section 3D
    status: cited mathematical statement
```

The planning consequence is conditional:

```text
if a candidate actually requires such a global section
and n is outside the exceptional cases,
then require multiple charts, a restricted domain,
redundancy, or an explicit discontinuity.
```

It is incorrect to store the blanket sentence “`SO(n)` is never globally a product,” and it is also incorrect to apply this family-level fact to a pointwise kernel that requests no continuous family.

## A correctly scoped semantic object

For the underdetermined alignment request:

```text
AlignmentConstraint:
    statement: proper transform required
    formal: det(Q) = +1
    scope: choose some Q satisfying Qd = ||d||e1
```

A deterministic consequence is that a single hyperplane reflection cannot be the final transform. In dimension at least two, two-reflection, direct-plane, and Givens representations remain possible, subject to the zero and antiparallel cases.

For an already prescribed proper `Q`, the conclusion changes: the exact minimum is `rank(I - Q)`, which is even. A nonidentity transform has minimal reflection length two exactly when that rank is two. The identity has minimal length zero, although redundant reflection pairs can of course represent it.

## Advisory synthesis that remains a proposal

An LLM can combine

```text
input vectors are sparse
only one aligned result is needed
determinant -1 is permitted
target penalizes divergent control flow
```

and propose comparing a Householder/reflection representation before a long data-dependent Givens chain.

That proposal is useful because it connects numerical and target information. It still needs:

- a correct zero/degeneracy path;
- a checked transform oracle;
- a numerical error policy;
- actual measurements on the target.

## Advisory roles

One model can perform several explicit passes:

- **mathematics advisor** — surface potentially relevant topology, group structure, and exact invariants;
- **numerical-linear-algebra advisor** — propose stable algorithm families and known tradeoffs;
- **target advisor** — connect candidates to CPU/GPU/ISA/shader facts;
- **critic** — search for scope changes, hidden continuity assumptions, degenerate cases, and false independence;
- **planner/judge** — produce a shortlist and the checks or measurements needed to discriminate among it.

None of these role labels confers authority. Their output remains structured claims for validation.

## Relationship to Cayley / the mathematical toy box

Cayley-style exploration can provide small examples of generators, relations, equivalent words, commuting factors, and independent block actions. Those facts must retain their exact computational meaning:

- a relation may shorten a word;
- commutation may permit reordering;
- a verified orthogonal block decomposition may permit concurrent execution;
- none of these follows automatically from the others.

The planner should consume such results as inspectable evidence rather than require every mathematical toy to become production compiler code.

## Keyboard notation

The intended keyboard design includes semidirect/twisted-product vocabulary near multiplicative `×`. [`existing-project-connections.md`](existing-project-connections.md) records the exact keyboard revision inspected and the absence of such a mapping there. Preserve the intention as a pending keyboard requirement; do not report the control as implemented without checking a newer revision.

A twisted bundle must not be described as a semidirect product merely because the keyboard offers related vocabulary.

## Immediate research rule

Before choosing an implementation, ask:

1. Is this an underdetermined request, a prescribed transform, or a family?
2. What is the exact input domain, including zero and degenerate cases?
3. Does any candidate require a continuous local or global choice?
4. Which theorem applies to that exact object and dimension?
5. What deterministic consequences follow?
6. Which algorithm/representation families survive those checks?
7. Which workload and target facts discriminate among them?
8. What can be measured or checked before execution?

This is a legitimate present-day use of an LLM: orchestrate incompletely unified mathematical, numerical, empirical, and architectural information into explicit candidate plans while leaving correctness and hard pruning to checkable evidence.

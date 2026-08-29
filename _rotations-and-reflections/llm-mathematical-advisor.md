# LLM mathematical advisory layer

The rotations/reflections discussion gives a concrete use for the Computer Science planner that is broader than selecting among already-named numerical routines.

The planner should be able to ingest **heterogeneous information whose computational role is not yet specified** and let an LLM use it as advisory evidence before a concrete algorithm is chosen.

That includes facts such as:

- `SO(n-1) -> SO(n) -> S^(n-1)` is generally a twisted bundle rather than a global direct product;
- a proposed family of decompositions may fail to admit one globally continuous choice;
- reflection choices are naturally projective (`v ~ -v`);
- determinant `+1` is a semantic constraint, not an implementation name;
- numerical-linear-algebra facts about Givens, Householder, direct 2-plane rotations, stability, sparsity, and reuse;
- target facts such as divergence, dependency depth, register pressure, memory traffic, precision, and compile time;
- user-supplied preferences or mathematical observations that have not yet been converted into a formal compiler rule.

The system does **not** need to know in advance how every such fact will lower to code. It needs to preserve the fact, provenance, confidence, and scope so that a planner can consult it later.

## Proposed planning shape

```text
semantic request
  -> evidence ledger
       mathematical facts / theorems
       symbolic/CAS results
       numerical-linear-algebra knowledge
       hardware / ABI facts
       measured benchmark evidence
       user constraints/preferences
       unresolved hypotheses
  -> LLM advisory / orchestration pass
       identify relevant global constraints
       propose candidate algorithm families
       rule out incompatible choices
       identify missing information
       suggest local charts / case splits when global uniformity is suspect
  -> deterministic checks / mathematical oracles where available
  -> target-aware algorithm selection
  -> IR / branches / vector operations / shaders / instructions
  -> execution evidence
  -> feed results back into the ledger
```

The LLM is not the source of truth. It is the component that can **connect facts that do not yet share a common executable representation**.

## Why this is useful now

Current language models are already capable of reading a statement such as

```text
SO(n) is not globally S^(n-1) x SO(n-1)
```

and drawing a useful planning consequence without needing a bespoke `twisted_bundle` optimization pass:

```text
do not assume one globally smooth coordinate/factorization rule;
expect multiple charts, sign choices, singular/degenerate cases,
or a local construction whose domain is explicit.
```

That consequence is not yet a machine-code algorithm. It is still valuable because it can prevent the planner from imposing a mathematically impossible global regularity requirement.

Similarly, an LLM can combine:

```text
input vectors are sparse
+ only one aligned result is needed
+ determinant -1 is allowed
+ target has expensive divergent control flow
```

with numerical-linear-algebra knowledge and propose that a Householder/reflection family deserves comparison before a long data-dependent Givens chain.

The proposal must still be checked and measured. The useful capability is **cross-domain synthesis before formal lowering**.

## Ensemble rather than one opaque verdict

The planner can treat the LLM layer as an ensemble of advisory roles even if one underlying model performs several passes:

- **mathematics advisor** — topology, group structure, algebraic constraints, exact invariants;
- **numerical-linear-algebra advisor** — stable/correct algorithm families and known tradeoffs;
- **target advisor** — CPU/GPU/ISA/shader consequences;
- **critic** — search for hidden assumptions, especially accidental global-continuity or data-shape assumptions;
- **planner/judge** — synthesize candidate plans and state what evidence would discriminate among them.

These roles should return structured claims rather than authority:

```text
claim
source / provenance
scope
confidence
hard constraint | measured fact | theorem | heuristic | suggestion
what decision it affects
what would falsify it
```

A theorem and an LLM hunch must never enter the ledger with the same status.

## Global constraints as advisory objects

We do not need a complete formal language for global topology before using it.

A first representation can be deliberately loose but typed enough to avoid confusion:

```text
GlobalConstraint:
  statement: "no single global trivialization assumed"
  domain: SO(n)
  source: Hatcher Section 3D / bundle structure
  status: mathematical fact
  planning_effect:
    - permit local charts / case splits
    - reject plans requiring one globally continuous parameterization unless proved
```

Another example:

```text
SemanticConstraint:
  statement: "proper rotation required"
  formal: det(Q) = +1
  planning_effect:
    - reject a single reflection as final transform
    - retain two-reflection and Givens/direct-rotation families
```

The first version can remain prose-plus-structured metadata. Formalization can be added only where it buys verification or automatic pruning.

## Relationship to Cayley / the mathematical toy box

Cayley-style group exploration belongs naturally on the mathematics side of this architecture. Small concrete group actions, Cayley graphs, reflection groups, and toy examples can act as an experimental playground for asking:

- what does a generator/factorization choice look like concretely?
- which relations collapse apparently different sequences?
- how do local moves compose globally?
- what information is worth passing from a mathematical explorer into the planner?

The planner should consume those results as evidence or reusable structural facts rather than require every mathematical toy to become production compiler code.

## Keyboard notation

If a twisted-product / semidirect-product symbol is already present on the mathematical keyboard, it is especially appropriate vocabulary for these notes. The current GitHub code search available here did not locate a textual `twisted product` / `semidirect` entry in `isomorphisms/programmers-keyboard`, so this specific keyboard mapping should be verified separately rather than asserted from memory.

## Immediate research rule

For rotations/reflections, the planner should now ask before choosing an implementation:

1. Is the request local (one matrix/vector) or a coherent family over a domain?
2. Does the proposed algorithm silently require a global continuous choice?
3. What topology/group structure constrains that choice?
4. What numerical algorithm families remain after hard semantic constraints?
5. Which workload/target facts discriminate among them?
6. Can an LLM synthesize the currently heterogeneous evidence into a testable shortlist?
7. Which parts of that shortlist can be checked deterministically before execution?

This is a legitimate present-day use of an LLM: not replacing proof or benchmarking, but **orchestrating incompletely formalized mathematical, numerical, empirical, and architectural information into explicit candidate plans whose assumptions remain inspectable**.

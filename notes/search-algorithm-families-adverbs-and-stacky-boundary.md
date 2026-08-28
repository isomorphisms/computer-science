# Search algorithm families, compiler adverbs, and the stacky boundary

> **Status:** cross-project design note. This uses message search as a concrete
> test for architectural compilation and for the claim that the resulting
> family is, and is not, stack-like. It does not claim that search algorithms
> form a mathematical stack.

Related notes:

- [`architectural-compilation.md`](architectural-compilation.md)
- [`stacks-moduli-deformation-and-mapping-class-groups.md`](stacks-moduli-deformation-and-mapping-class-groups.md)
- [`albers-single-pixel-jacobian-stacky-boundary.md`](albers-single-pixel-jacobian-stacky-boundary.md)

Concrete application plan:
[`utilities-android-phone-user/search-text-messages`](https://github.com/isomorphisms/utilities-android-phone-user/tree/search-text-messages/messages/plans).

## The compiler-design example

Text search is a clean example of the larger goal because one programmer-level
operation may admit many realizations:

```text
contains one exact literal
  -> scalar scan
  -> Two-Way or Boyer–Moore-family scan
  -> Shift-And register state
  -> word/SIMD candidate filtering
  -> trigram index plus exact verification
  -> FPGA equality masks
```

The compiler/planner should preserve the semantic operation, collect relevant
facts, compare candidates, and expose the choice and its consequences. It
should not require the programmer to spell a character loop and then hope a
late backend recognizes what the loop meant.

But several things commonly called search do **not** share that semantic
operation:

- regex matching;
- token/phrase retrieval;
- edit-distance/fuzzy retrieval;
- BM25-style ranking;
- embedding similarity;
- structured row filtering.

An architectural compiler first separates meanings, then compares algorithms
inside each meaning. A huge undifferentiated `search` verb would destroy the
very facts the planner needs.

## Where "adverbs" fit

For a fixed verb such as exact literal membership, provisional adverbs or
constraints can describe realization:

- `low_memory`;
- `no_persistent_index`;
- `streaming`;
- `compiled_pattern`;
- `bounded_setup`;
- `small_code`;
- `battery_sparing`;
- `inspectable`;
- `runtime_free`.

They do not directly name Shift-And, Two-Way, or a trigram index. They narrow
the candidate family or help order its Pareto frontier.

Several apparent adverbs are actually semantic and must stay with the verb's
contract:

- case-sensitive versus Unicode case-folded;
- verbatim versus normalized text;
- exact versus approximate;
- membership versus first/all/count;
- overlapping versus non-overlapping occurrences;
- newest-first versus relevance-ranked results.

The dividing test is observational: if a caller can distinguish the choice in
the promised result/effect contract, it is not merely a hidden realization
choice. This is the same warning already visible in immediate versus buffered
I/O: batching is semantics-preserving only under a byte-stream observation
contract that does not promise intermediate visibility or per-write failures.

## Static and dynamic choice

Search also prevents "compiler choice" from being equated with one compile-time
decision.

- A fixed pattern can have masks, automata, or skip tables built at compile
  time.
- A phone user's query is dynamic, so setup and selection may occur once per
  query.
- An index can be selected only when it is present, current, and compatible
  with the query's normalization policy.
- A hybrid may use an index or SIMD filter to generate candidates and an exact
  scalar routine to verify them.
- A runtime selector is itself a small program whose code and policy should be
  inspectable.

The architectural result can therefore be one implementation, several
implementations plus a selector, or a pipeline of complementary algorithms.

## In what sense this is stack-like

There is a reasonable stacky analogy if we keep it narrow.

One semantic operation has multiple presentations over different contexts:

- ARMv7 without or with useful NEON;
- an FPGA with text resident beside the circuit;
- a tiny changing corpus with no index;
- a stable large corpus with a current trigram index;
- a compile-time pattern versus a dynamic pattern.

Implementations can overlap in applicability. On those overlaps we can retain
explicit equivalence evidence: the same match positions for every input in a
stated domain, or differential/conformance results for a bounded corpus. A
planner can restrict a general plan to a more specific capability context and
can sometimes combine compatible local plans into a hybrid global plan.

This resembles the motivating vocabulary of a family over contexts, multiple
presentations, arrows witnessing sameness, restriction, and possible gluing.
It is more informative than pretending that one implementation is the
canonical object.

## In what sense it is not yet a stack

A portfolio of algorithms is ordinarily just an indexed family or catalog.
Multiple ways to compute the same Boolean do not create stack structure by
themselves.

To make a literal stack claim we would need, at minimum:

1. a specified base category/site of contexts;
2. a coverage notion saying which local contexts cover another;
3. objects over each context, plausibly implementations or implementation
   plans;
4. invertible arrows with a precise equivalence contract;
5. restriction/pullback along context maps;
6. effective descent: compatible local objects and arrows glue coherently to a
   global object, uniquely up to the appropriate equivalence.

The current search plan supplies none of that as a theorem. Benchmark
comparisons are not isomorphisms. Two algorithms agreeing on a finite corpus is
evidence, not necessarily an arrow witnessing total semantic equivalence.
Approximate search and exact search are different objects/operations, not two
presentations to glue. A dispatcher that chooses among `if` branches is not a
descent construction merely because it combines cases.

The right current statement is therefore:

> The search portfolio is stack-like as a prompt to retain families,
> presentations, and equivalence evidence. Its implemented mathematical
> structure should remain an ordinary typed/costed catalog until a concrete
> restriction-and-gluing problem requires more.

This also has nothing to do with the LIFO stack data structure.

## A test that could strengthen or kill the analogy

Use one exact search contract and three presentations:

- ARM Shift-And for word-sized patterns;
- a conventional portable scan;
- FPGA equality-mask search for blocks resident beside the circuit.

Specify:

- the common domain, including byte validity, offset and overlap rules;
- capability/context maps;
- evidence that each presentation preserves every match position;
- overlap contexts where more than one presentation applies;
- a hybrid plan that partitions or streams the corpus across contexts;
- coherence conditions ensuring boundary matches are neither lost nor counted
  twice.

Then ask whether the hybrid is adequately described by ordinary composition
and refinement. If it is, stacks add no needed machinery. If locally valid
presentations with nontrivial equivalence arrows must be glued and distinct
gluings remain meaningfully different, the stack analogy becomes a candidate
for a formal model rather than a metaphor.

The negative result is useful: it tells the compiler project to keep the
catalog, witnesses, and planner without importing mathematical structure that
the program does not use.

## Practical compiler rule

For now preserve this pipeline:

```text
semantic verb and result contract
  -> observations and constraints/adverbs
  -> candidate algorithm/representation family
  -> checked equivalence and cost evidence
  -> static choice, selector, or composed pipeline
  -> target lowering
```

Do not quotient away which implementation was selected or why. Also do not
call the retained family a stack until arrows, restriction, and descent do real
work in an executable example.

# Existing project connections

## Mathematical keyboard: intended semidirect / twisted-product vocabulary

The keyboard design discussion intends **semidirect product** to share the physical neighborhood or control family of Cartesian/multiplicative `×`, with the orientation/glyph (`⋊` versus `⋉`) still to be settled.

At inspected revision [`b52ffb3`](https://github.com/isomorphisms/programmers-keyboard/commit/b52ffb3dc9979990867d7c12b19603d04a8855dd), the `isomorphisms/programmers-keyboard` tree does not contain a textual `semidirect`, `twisted product`, `⋊`, or `⋉` mapping. Therefore this is a design requirement to preserve, not an implemented keyboard fact. The keyboard repository must be updated separately before another project treats the mapping as available.

That vocabulary is directly relevant to the present `SO(n)` discussion. When a space is a twisted bundle rather than a global direct product, the planner and explanatory UI should have concise notation available for “there is product-like structure here, but not a naive globally independent product.”

Do not conflate every twisted bundle with a semidirect product of groups; the keyboard connection is one of mathematical vocabulary and conceptual structure, not an assertion that the bundle

```text
SO(n-1) -> SO(n) -> S^(n-1)
```

is itself a semidirect product.

Keyboard repository:

https://github.com/isomorphisms/programmers-keyboard

## Cayley in the mathematical toy box

The existing Cayley project is:

https://github.com/isomorphismes/Cayley

It is the natural mathematical-toy-box place for concrete experiments with:

- generators and relations;
- Cayley graphs;
- reflection-generated groups;
- alternative factorizations representing the same group element;
- commuting versus noncommuting factors;
- word length versus computational dependency depth;
- small examples of local moves composing to global transformations.

The Computer Science planner should not absorb all of Cayley as compiler machinery. Instead, Cayley can produce small, inspectable mathematical examples or structural facts that enter the planner's evidence ledger.

## Why the connection matters

The current architecture now has three complementary pieces:

```text
Cayley / mathematical toy box
    explore generators, relations, factorizations, group structure

Computer Science planner
    combine mathematical structure with numerical and target evidence

compiler/backend
    lower the selected plan to concrete operations and measure it
```

An LLM advisory layer can sit in the middle and propose connections among facts that have not yet been formalized into one common executable language. Deterministic checks remain responsible for dependency and correctness claims.

For example:

```text
Cayley / theorem / reference:
  these factors commute

planner consequence:
  permit a reorder, then check whether it improves the plan

backend question:
  which valid order maps best to this CPU/GPU target?
```

Or, with stronger information:

```text
Cayley / theorem / reference:
  the state and factors split into independent orthogonal blocks

planner consequence:
  expose the blocks as possible concurrent work

backend question:
  does block-parallel execution beat sequential application here?
```

Or:

```text
topology/reference:
  this candidate would induce a section of the stated bundle,
  and the dimension-scoped theorem rules out such a global section

planner consequence:
  retain multiple local cases/charts instead of forcing one smooth global rule

backend question:
  what is the cheapest explicit case representation on this target?
```

This is exactly the kind of information that is useful before it has been turned into a dedicated compiler pass.

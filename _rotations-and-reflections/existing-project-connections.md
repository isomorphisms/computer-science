# Existing project connections

## Mathematical keyboard: semidirect / twisted-product vocabulary

The keyboard design checkpoint already assigns **semidirect product** to the same physical control as Cartesian/multiplicative `×`. The remaining presentation choice was the orientation/glyph (`⋊` versus `⋉`), not whether semidirect-product notation belonged on the board.

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

An LLM advisory layer can sit in the middle and connect facts that have not yet been formalized into one common executable language.

For example:

```text
Cayley / theorem / reference:
  these factors commute or act on independent subspaces

planner consequence:
  expose them as one parallel layer

backend question:
  does that layer map well to this CPU/GPU target?
```

Or:

```text
topology/reference:
  no global product/trivialization should be assumed

planner consequence:
  retain multiple local cases/charts instead of forcing one smooth global rule

backend question:
  what is the cheapest explicit case representation on this target?
```

This is exactly the kind of information that is useful before it has been turned into a dedicated compiler pass.

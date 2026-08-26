# RHS vector equivalence: motion upstairs, same type downstairs

> **Status:** cross-project research/test note. This records a concrete stack/deformation-shaped pattern exposed by the RHS verification experiments. It does **not** claim that embedding spaces or programming-language types automatically form a stack.

Related general notes: [`stacks-moduli-deformation-and-mapping-class-groups.md`](stacks-moduli-deformation-and-mapping-class-groups.md).

## The concrete pattern

The useful RHS fixture has two kinds of structure at once:

1. a generator changes one **known semantic facet** of a name while holding the other generated facets fixed; and
2. an independent language/type oracle can still say that the corresponding program objects have the **same data type** — for example, both are `Double`.

Those are different notions of change and sameness.

A model can move substantially in representation space even while the data-type classification is unchanged.

That is the important shape:

**motion upstairs in a representation/presentation space while remaining over the same semantic point downstairs.**

## Keep three layers separate

### 1. Generated semantic fixture

Let the test generator build names from independently controlled facets. The exact historical details of Hungarian notation should not be trusted as an implicit oracle; the fixture should record explicitly what each generated component means.

For a schematic example, suppose one controlled edit is

`aBcd -> aBcD`.

The generator knows exactly which facet changed. It can repeat that same edit in other contexts, for example

`ABcd -> ABcD`.

The important fact is not the letters themselves. It is that the generator supplies the ground-truth label: **the same facet changed in both pairs**.

### 2. Model representation

For a model embedding map

`E : name -> R^n`,

define the finite difference associated with facet `f` in context `c` by

`delta_f(c) = E(c with f_1) - E(c with f_0)`.

For the schematic `D/d` edit:

`delta_D(aBc_) = E(aBcD) - E(aBcd)`

and

`delta_D(ABc_) = E(ABcD) - E(ABcd)`.

If the model represents that facet consistently, these two differences should agree approximately, or at least lie in a common low-dimensional direction/subspace, even though the surrounding facets differ.

The cleanest oracle is therefore **not** “cosine similarity above some arbitrary threshold means equivalent.” The exact semantic equivalence class of the pair-differences is supplied by the generator: two differences belong to the same class when they were produced by the same controlled facet edit. The model is scored on how well its vectors recover those known classes.

### 3. Data-type / semantic classification

Independently, let

`type(x)`

be the language-level type of the generated program object.

For a type-preserving naming edit we can have

`type(x) = Double`

and

`type(x') = Double`

while

`E(name(x')) - E(name(x)) != 0`.

So “these are both doubles” is a coarse semantic equivalence that need not identify their names, tokens, embeddings, or the path/edit relating them.

## The fiber picture

A useful computational picture is

`pi : generated objects -> semantic/type classes`.

The fiber

`pi^-1(Double)`

contains many different concrete presentations whose program objects all have type `Double`.

A controlled naming edit that preserves type moves **inside that fiber**. The embedding model sees a nonzero finite difference, but the coarse type map does not move.

This is closer to the geometric phrase **vertical variation** than to ordinary equality:

- the representation changes;
- the chosen semantic classifier does not;
- the test retains the known edit that connects the two presentations.

Because the names and types here are discrete, these differences are finite chords, not literal tangent vectors of a smooth manifold. Tangent/deformation language should therefore be treated as a structural analogy until a continuous or infinitesimal model is actually supplied.

## The parallelogram / interaction test

The user's schematic comparison

`aBcd -> aBcD`

versus

`ABcd -> ABcD`

has a particularly sharp test.

If the `D/d` facet is represented independently of the `A/a` facet, then

`E(ABcD) - E(ABcd)`

should be approximately the same as

`E(aBcD) - E(aBcd)`.

Equivalently, the mixed finite difference

`E(ABcD) - E(ABcd) - E(aBcD) + E(aBcd)`

should be near zero.

That quantity is an empirical **interaction term** between the two controlled facets. It is the discrete analogue of asking whether the mixed derivative vanishes.

This gives more information than pairwise cosine similarity:

- first differences test whether a facet has a reproducible direction;
- mixed second differences test whether two facets interfere with one another;
- larger generated cubes can test higher-order interactions.

So the synthetic naming fixture naturally gives a little discrete deformation geometry: vertices are generated names, edges are one-facet edits, squares test commutation/interaction, and higher-dimensional cubes test whether independently specified semantic facets stay independent in the model representation.

## Two different equivalence relations

The experiment should name these separately.

### Pair-difference equivalence

Two edge differences are semantically equivalent when the generator says they instantiate the same facet edit.

For example, all `d -> D` edges have one ground-truth facet label regardless of the other generated coordinates.

The embedding model is tested on whether those labeled edges become geometrically coherent.

### Type equivalence

Two generated program objects are type-equivalent when the language/type oracle gives the same type, for example

`type(x) = type(y) = Double`.

This equivalence is generally much coarser. Many different naming facets and many different embedding vectors may lie in the same type class.

The key point is that these equivalences live on different objects:

- one classifies **transformations/differences**;
- the other classifies **program objects by semantic type**.

Collapsing them into one equality relation would lose the experiment.

## Where the stacky idea actually begins

An ordinary quotient by type is not yet stack theory. If all we keep is

`x -> Double`,

we have simply forgotten the presentation variation.

The stack/groupoid-shaped possibility appears when we retain the **arrows** connecting equivalent presentations and care about their composition and variation:

- objects: generated names/program objects;
- arrows: controlled facet edits or other explicitly allowed presentation transformations;
- coarse map: forget the arrow/presentation information and retain only a semantic class such as `Double`;
- model geometry: assign vectors to objects and finite-difference vectors to arrows;
- relations among arrows: independent edits should form approximately commuting squares when the model has learned independent facets.

This is useful because it gives executable content to the phrase “do not throw away the witness of sameness.” The coarse type result says only that both endpoints are `Double`; the retained edge says **how the presentation changed while staying `Double`**.

Whether this deserves an actual stack rather than a groupoid/fibered-family model depends on later evidence. In particular, we would want a reason to care about families over changing contexts, pullback/reindexing, or coherent gluing. The present RHS fixture already justifies retaining arrows and fibers; it does not by itself establish descent.

## Executable measurements worth keeping

For each model and each generated facet:

1. generate many endpoint pairs differing in exactly that facet;
2. record the ground-truth facet label and the language-level type of both endpoints;
3. embed both endpoints and store the difference vector;
4. measure within-facet coherence and between-facet separation;
5. compute mixed second differences for every pair of independently generated facets;
6. check whether type-preserving edits remain inside the same known type fiber;
7. compare models on how faithfully their vector geometry reconstructs the generator's exact semantic cube.

A particularly strong model would not merely classify endpoint names correctly. Its edge vectors would recover the generator's semantic directions, and its squares would show small unwanted interaction where the fixture declares the facets independent.

## Why this is a better stacks/deformation example than a slogan

This fixture has all of the pieces exposed separately:

- a known family of presentations;
- explicitly controlled deformations/edits;
- a coarse semantic equivalence such as “both are `Double`”;
- retained arrows witnessing which controlled change occurred;
- a vector representation in which those arrows can be compared;
- executable square/cube relations testing whether the representation respects semantic composition.

The strongest current statement is therefore:

**RHS verification supplies a discrete testbed for representation-space deformation inside semantic equivalence fibers.**

That is already precise and testable. Calling the resulting object a stack should remain conditional on whether the additional stack machinery — especially families, reindexing, and descent — becomes computationally necessary.

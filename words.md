# Words, free semigroups, and free groups

The object we were trying to remember has several closely related versions. It is worth keeping them separate because the distinction is exactly the bridge between automata and group theory.

Take two symbols `a` and `b`, together with formal undo-symbols `A` and `B`, where we intend `A = a⁻¹` and `B = b⁻¹`.

## Before cancellation: words

Let the alphabet be

`Σ = {a, A, b, B}`.

- All finite words in `Σ`, including the empty word, form the **free monoid** `Σ*` under concatenation.
- All non-empty finite words form the **free semigroup** `Σ+`.
- If we remember that `a <-> A` and `b <-> B` are paired by an involution but do not yet cancel them, this is a **free monoid/semigroup with involution**.

This is the automata-theory level: an input is literally a word, and concatenating inputs means doing one thing after another.

## After cancellation: the free group

Now impose

`aA = Aa = bB = Bb = ε`,

where `ε` is the empty word. Repeatedly delete adjacent inverse pairs. The resulting reduced words form the **free group on two generators**, usually written `F₂`.

So the answer to “is it a semigroup or a group?” is: **both descriptions occur at different stages**.

- The raw histories are free-semigroup/free-monoid words.
- Once inverse cancellation is imposed, the equivalence classes/reduced words are the free group.
- In *Indra's Pearls*, the actual Möbius transformations are invertible, so the mathematical transformation object is a group. But a renderer that enumerates words is naturally doing automata/string traversal before evaluation in that group.

For two generators, a reduced-word generator is almost a tiny finite automaton: it only has to remember the previous letter so that it does not emit its inverse next. After `a`, forbid `A`; after `A`, forbid `a`; and similarly for `b/B`. This avoids immediate backtracking through the word tree.

## Why this resembles a universal cover

There is a useful geometric picture behind the memory. The Cayley graph of the free group `F₂` with generators `a,b` is an infinite 4-regular tree. Equivalently, a bouquet of two circles has this tree as its universal cover. A reduced word specifies a path from the root. This is probably why the “word with undo-letters” idea can feel simultaneously like group theory, automata theory, and a universal-cover construction.

## Operator-theory connection

Free semigroups also occur directly in operator theory. For `n` generators, words in the free semigroup `F_n⁺` index the standard basis of full Fock space and the left creation/free-shift operators. Families of isometries indexed in this way have a higher-rank analogue of the classical Wold decomposition.

The classical **Wold decomposition** says, roughly, that an isometry splits into a unilateral-shift part and a unitary part. Popescu's row-isometry version similarly separates a free-shift/left-regular part from a row-unitary (Cuntz-type) part. If the decomposition name we were trying to remember came from operator theory, **Wold / Wold–von Neumann decomposition** is the strongest candidate.

## Another possible decomposition memory: Grushko

There is also a group-theoretic name close to the free-product side of this story: the **Grushko decomposition theorem**. A finitely generated group can be decomposed as a free product of freely indecomposable non-cyclic factors together with a free-group factor, with the factors unique in the appropriate sense. If the half-remembered name was something like “Grushko ...”, this is the Wikipedia decomposition it points to.

This is different from Wold and different again from Krohn–Rhodes:

- **Wold**: decomposition of isometries / row isometries in operator theory.
- **Grushko**: free-product decomposition of finitely generated groups.
- **Krohn–Rhodes**: cascade/wreath-product decomposition of finite semigroups/automata.

## Sources

- Free monoid / free semigroup: https://en.wikipedia.org/wiki/Free_monoid
- Free semigroup with involution: https://en.wikipedia.org/wiki/Semigroup_with_involution
- Free group: https://en.wikipedia.org/wiki/Free_group
- Grushko theorem and decomposition: https://en.wikipedia.org/wiki/Grushko_theorem
- A modern paper summarizing Popescu's Wold decomposition for row isometries: https://arxiv.org/abs/2103.02070

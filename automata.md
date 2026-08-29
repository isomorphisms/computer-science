# Automata: surrounding context

“Automata” covers several related traditions. They share the idea that a small set of rules acts repeatedly on a state, but they are not all the same mathematical object.

## Finite-state / sequential machines

A finite-state machine has a finite set of states and an input alphabet. Each input letter changes the state; an input word composes those changes. This is the lane that leads directly to transformation semigroups and Krohn–Rhodes theory.

The semigroup point matters because a transition can forget information. If two states are both sent to the same state, that action has no inverse. Permutation actions are the reversible special case; resets are the obvious irreversible case.

The familiar Moore/Mealy distinction is also in this finite-state tradition: Moore outputs depend on the current state, while Mealy outputs depend on state and current input.

John Conway belongs here as well as in cellular automata. His 1971 book **Regular Algebra and Finite Machines**, based on a 1966 Cambridge course, is directly about regular events/languages and finite machines. So the Conway connection is not only the Game of Life.

## Cellular automata

The other famous branch is cellular automata: many cells evolve in parallel according to a uniform local rule.

Some cultural landmarks:

- Stanisław Ulam and John von Neumann's work on self-reproduction and discrete cellular models in the 1940s–50s.
- John Conway's **Game of Life**, introduced publicly in 1970 through Martin Gardner's *Scientific American* column. Its tiny local rule produces moving, reproducing, and computationally universal structures.
- Stephen Wolfram's systematic experiments with one-dimensional cellular automata in the 1980s and his four broad behavior classes.
- Wolfram's 2002 **A New Kind of Science**, a roughly thousand-page attempt to make systematic experimentation with very small programs into a general scientific method.
- **Rule 30** as the canonical simple-rule/apparent-complexity example and **Rule 110** as the canonical very-small universal-computation example. Matthew Cook proved Rule 110 universal.

There really is also a **Rule 37** in the elementary numbering scheme, and *A New Kind of Science* discusses a **second-order rule 37** among examples of class-4-like behavior. I have not found a standard bibliographic object literally called “Experiment 37”, so that phrase should be treated as a memory marker rather than silently turned into a citation. But “rule 37 in the huge Wolfram book” is a real nearby object.

The point of mentioning Wolfram here is cultural rather than accepting the full *New Kind of Science* program. A thousand-page book full of numbered tiny-rule experiments is a recognizable extreme expression of the idea that one can learn by enumerating simple rule systems and watching what grows out of them.

## Conway, Wolfram, and Krohn–Rhodes are adjacent, not identical

It is easy to collapse all of this into “automata”, but the differences are useful:

- **finite automata / Krohn–Rhodes:** a finite state is acted on by input words; transformation semigroups and cascades are central;
- **cellular automata / Life / Wolfram rules:** a spatial field of cells updates in parallel by a local rule;
- **Indra's Pearls:** words in a few invertible Möbius generators act on the Riemann sphere; the resulting orbit/limit set is geometric.

The common family resemblance is repeated composition of simple rules, plus the possibility that the global object is much richer than the rule description. That is enough to make them useful neighbors in a mathematical toy box without claiming a theorem that identifies them.

## The Wild Book as part of the same culture

John Rhodes pushed in another direction. The manuscript later published as *Applications of Automata Theory and Algebra* treats finite-state/semigroup decomposition as a possible analytical language for biology, physics, psychology, philosophy, and games. Later published work by Rhodes and collaborators actually applies the hierarchical automata viewpoint to the Krebs citric acid cycle.

That is worth recording as cultural context even if one remains skeptical of the grander claims. Conway's Life, Wolfram's enormous catalogue of small computational experiments, and Rhodes's attempt to decompose biological reaction systems all belong to a twentieth-century/current tradition of asking how far very small discrete rule systems can be used to expose structure in things that do not initially look like “computer programs.”

## Sources

- Stanford Encyclopedia of Philosophy, cellular automata: https://plato.stanford.edu/entries/cellular-automata/
- Conway, *Regular Algebra and Finite Machines* (Dover listing): https://store.doverpublications.com/products/9780486485836
- Conway's Game of Life: https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life
- MAA review of Wolfram's *A New Kind of Science*: https://old.maa.org/press/maa-reviews/a-new-kind-of-science
- Wolfram's Rule 110 section: https://www.wolframscience.com/nks/p676--the-rule-110-cellular-automaton/
- *A New Kind of Science*, chapter 11 PDF; the class-4 examples around p. 692 include second-order rule 37: https://files.wolframcdn.com/pub/www.wolframscience.com/nks/nks-ch11.pdf
- Krohn–Rhodes notes: `Krohn-Rhodes.md`
- Free words and inverses: `words.md`

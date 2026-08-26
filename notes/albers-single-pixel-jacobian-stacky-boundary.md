# Albers single-pixel Jacobian: stacky boundary test

> **Status:** cross-project research note. This is meant to constrain later language/model claims, not establish that color perception forms a stack. Review after the first two-pixel/contextual measurements.

Related general notes: [`stacks-moduli-deformation-and-mapping-class-groups.md`](stacks-moduli-deformation-and-mapping-class-groups.md).

Concrete experiment: <https://github.com/isomorphisms/albers/tree/notes/stacky-single-pixel-jacobian/single-pixel>

## The useful baseline

The Albers repository currently has a deliberately small numerical object:

`RGB8 -> CIELAB`

at one source pixel, with a centered `h = 4` finite-difference Jacobian. This provides a local `3 x 3` response matrix for nearby RGB code changes in a chosen coordinate presentation.

This is valuable precisely because it is **not yet a stacky object**. It gives us a baseline against which richer claims can be tested.

The source is a finite RGB lattice, so the matrix is a finite-difference local linear surrogate. Calling it a tangent space or deformation theory without further qualification would already be stronger than the computation warrants.

## Separate four things that are easy to blur

1. **Coordinates/presentation.** RGB8 and CIELAB are ways of describing a stimulus or response.
2. **Local sensitivity.** The Jacobian estimates how Lab coordinates respond to nearby RGB-code changes.
3. **Equivalence.** A statement that two presentations/stimuli count as the same for some purpose.
4. **Stack/groupoid structure.** Retaining the arrows/witnesses, automorphisms, variation in families, and possibly gluing data associated with those equivalences.

Only (4) is specifically stack-like. A Jacobian does not manufacture (3) or (4).

## Why Albers may nevertheless be a good stack test

Albers' color-interaction experiments make **context** unavoidable. A patch cannot always be assigned a perceptual effect independently of its surround. Computationally, this suggests replacing the idea of one global perceptual quotient with a family indexed by context.

Possible data, stated cautiously:

- a base of contexts: neighboring colors, arrangement, adaptation/display conditions;
- concrete stimuli over each context;
- measured or experimentally stipulated arrows saying when two stimuli count as perceptually equivalent for the task;
- automorphisms/stabilizers when nontrivial transformations fix an object under the chosen notion of equivalence;
- a coarse projection that forgets those arrows when only a class label is wanted.

But a context-indexed metric may already model the observations. Stack machinery is justified only if retaining equivalence witnesses and their behavior over changing contexts buys something that the simpler model cannot express.

## Tiny symmetry fixture

A two-pixel state `(left, right)` has an obvious formal swap. Whether that swap is an equivalence is part of the problem statement, not a mathematical freebie.

If a particular abstraction deliberately forgets left/right labeling, the swap is an allowed arrow. Then `(c,c)` has a nontrivial stabilizer under the swap while `(c1,c2)` with `c1 != c2` generally does not. That is a clean example of stabilizer type changing on a special locus.

If the actual perceptual experiment cares about spatial left/right placement, then the swap should not be quotiented out. This is exactly why the equivalence relation must be specified before any quotient/moduli language is used.

## The computational question that would justify richer bookkeeping

Try to construct an executable fixture with all three properties:

1. two states have the same **coarse** perceptual classification;
2. their retained equivalence/context/stabilizer data are different;
3. the richer data predicts a different local deformation or response under a subsequent perturbation.

If this exists, throwing away arrows really loses computationally relevant information. If it does not, an ordinary quotient, family, or context-dependent metric may be enough.

This is a sharper test than asking whether the vocabulary sounds applicable.

## Relation to deformation language

The finite-difference Jacobian measures local numerical response. Classical deformation theory asks a different question: which infinitesimal variations correspond to actual nearby objects/families, which extend, and which are obstructed.

For this color experiment we should therefore keep the words separate:

- finite RGB probe -> local sensitivity estimate;
- allowable contextual variation -> candidate deformation data only after the family/problem is defined;
- equivalence/gauge direction -> only after an explicit transformation is declared inessential;
- obstruction -> only if a locally admissible variation fails to extend for a structural reason, not merely because it exits the RGB gamut or fails a threshold test.

## Questions for a stronger review

1. Is the proposed `context -> groupoid of stimuli` picture mathematically coherent, or is a context-indexed metric the honest model?
2. What should count as an actual arrow/witness of perceptual equivalence rather than a scalar similarity score?
3. Can an empirically meaningful stabilizer jump be found, rather than the artificial unlabeled-two-pixel swap fixture?
4. Is there any genuine descent/gluing phenomenon across local context descriptions?
5. Can a coarse perceptual quotient identify states whose local response data differ in a way the retained arrows explain?
6. Are we accidentally treating CIELAB as more perceptually canonical than it is?
7. Is the language of stacks adding constraints/tests, or merely renaming a family of measurements?

The desired outcome of review is allowed to be negative. If ordinary families, metrics, and explicit equivalence relations suffice, that is a useful result and should keep stack machinery out of the implementation.

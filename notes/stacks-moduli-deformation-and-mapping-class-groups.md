# Stacks, moduli, deformation, and mapping class groups

> **Status:** research notes and vocabulary exploration, not a language specification.
>
> **Question motivating the note:** can a programming language describe not only a value or computation, but also a *family* of acceptable variants, the transformations that count as sameness, the transformations that count as genuine change, and the way small changes propagate? Stacks and deformation theory supply serious mathematical vocabulary for parts of this question. They do **not** by themselves supply a complete programming-language semantics, metric sensitivity theory, or renderer equivalence criterion.

These notes are independently written from public sources. They deliberately distinguish established mathematics from speculative programming-language analogies.

## 1. Source and copyright discipline

The fact that a PDF can be downloaded does not imply that it is openly licensed.

| Source | Access / licence status used here | How these notes use it |
| --- | --- | --- |
| Kai Behrend, *Introduction to Algebraic Stacks* | Behrend publicly hosts a course PDF at UBC. The chapter was subsequently published by Cambridge University Press in *Moduli Spaces* (2014). I did not find an open-content licence for Behrend's chapter. | Independent summary and source links only. No figures or extended prose copied. |
| *Moduli Spaces*, LMS Lecture Note Series 411 | Publisher-owned book. Several component chapters also have public arXiv/author versions. | Independent summaries based on public abstracts, author copies, and publisher metadata. |
| The Stacks Project | GNU Free Documentation License 1.2 or later, no invariant sections / cover texts. | Mostly tag references and independent paraphrase, rather than importing its prose. |
| Coquand–Mannaa–Ruch, *Stack Semantics of Type Theory* | Public arXiv manuscript; institutional repository lists the accepted manuscript with licence unspecified. | Short independent summary and citation. |
| Yair Minsky, *A Brief Introduction to Mapping Class Groups* | Public author-hosted/academic PDF. | Independent primer and citation. |
| Farb–Margalit, *A Primer on Mapping Class Groups* | Publisher-owned book; Benson Farb's author page links a PDF, although availability of old links may vary. | Independent synopsis from public metadata and related public lecture notes. |
| Kollár, *Families of Varieties of General Type* | Cambridge book; Kollár also hosts drafts/final-form material on his Princeton page. | Independent reading notes and link to the author's page. |
| arXiv papers below | Publicly accessible; copyright terms vary by paper/version. | Independent summaries, not copied text. |

This is an access/provenance record, not legal advice.

### Primary links

- Behrend course page: <https://personal.math.ubc.ca/~behrend/math615A/>
- Behrend public PDF: <https://personal.math.ubc.ca/~behrend/math615A/stacksintro.pdf>
- Cambridge *Moduli Spaces*: <https://www.cambridge.org/core/books/moduli-spaces/>
- Stacks Project: <https://stacks.math.columbia.edu/>
- Stacks Project licence: <https://github.com/stacks/stacks-project/blob/master/COPYING>
- Coquand–Mannaa–Ruch, arXiv:1701.02571: <https://arxiv.org/abs/1701.02571>
- Minsky mapping-class notes: <https://gauss.math.yale.edu/~yhm3/research/PCMI.pdf>

The speech-to-text phrase “marginalized faces” did not correspond to a standard term I could identify in this literature. I have treated it as referring to the requested material on **moduli spaces** (especially moduli of Riemann surfaces), rather than inventing a mathematical object with that name.

---

## 2. Kai Behrend: *Introduction to Algebraic Stacks*

Behrend's notes are unusually useful because they do not begin by throwing the full machinery of algebraic geometry at the reader. They begin with triangles. The triangle example is not decorative; it is the mechanism by which nearly every important stacky idea is made necessary.

The notes have three broad movements:

1. topological stacks through families of triangles;
2. the general language of fibered categories, prestacks, stacks, torsors, quotient stacks, Morita equivalence, Deligne–Mumford stacks, orbifolds, and fundamental groups;
3. algebraic stacks and an application to Riemann–Roch for stacky curves.

### 2.1 Why triangles are a moduli problem

A moduli problem asks for a space whose points represent mathematical objects up to some chosen notion of equivalence. For triangles, the exact problem depends on choices: labelled or unlabelled vertices, oriented or unoriented triangles, congruence or similarity, degenerate triangles permitted or forbidden, embedded triangles versus abstract metric data, and so on.

Even the easiest version immediately exposes a problem with “one point for one isomorphism class.” A generic scalene triangle has no nontrivial permutation symmetry preserving it. An isosceles triangle does. An equilateral triangle has still more. Thus the amount of internal symmetry varies from point to point.

An ordinary quotient space can remember which orbit a triangle belongs to while forgetting the stabilizer that made that orbit special. A stack is designed not to throw that information away.

### 2.2 Families, not isolated points

The central shift is from classifying isolated objects to understanding how objects **vary in families over a base space**.

A family of triangles over a base `B` associates a triangle to each point of `B`, continuously or algebraically as appropriate, with enough structure that pulling the family back along a map `B' -> B` makes sense. A moduli theory must therefore describe not only objects but also:

- families of objects;
- isomorphisms between families;
- pullback along maps of bases;
- local descriptions and their gluing.

This is already suggestive for programming: a “parameterized program” is not adequately described by the set of outputs it can produce. We may care about how outputs vary with parameters, how local parameterizations overlap, and which transformations witness equivalence.

### 2.3 The symmetry groupoid

For a family, Behrend packages the symmetries into a **groupoid**. A groupoid has objects and invertible arrows. The arrows are not discarded after determining which objects are equivalent.

Important special cases make the idea concrete:

- a set is a groupoid with only identity arrows;
- an equivalence relation determines a groupoid with arrows witnessing equivalence;
- a group is a one-object groupoid whose arrows are the group elements;
- a group action gives a transformation/action groupoid.

For a parameter space `X` with a group `G` acting on it, the action groupoid remembers both `x in X` and the arrows `x -> g.x`. The ordinary orbit space `X/G` remembers only the resulting equivalence classes.

This is the first precise replacement for the informal phrase **“axis of sameness.”** The standard mathematical data are not an axis but an equivalence groupoid, stabilizer groups, and—when differentiable/algebraic structure is present—the infinitesimal directions generated by a group action.

### 2.4 Fine versus coarse moduli

A **coarse moduli space** is roughly a space of isomorphism classes with the best reasonable global topology or geometry. It can answer “which class is this object in?” while losing information about automorphisms and often about how families glue.

A **fine moduli space** carries a universal family: every family in the moduli problem is obtained by pullback of that universal family, in the appropriate unique sense.

The triangle examples make the obstruction vivid. Scalene triangles, whose stabilizers are trivial in the relevant problem, can behave like an ordinary fine moduli problem. Isosceles and equilateral triangles have nontrivial symmetries, and a coarse space alone cannot encode all families. In Behrend's examples, even a coarse moduli space as simple as an interval or a point can underlie genuinely different families.

The programming-language moral, if one survives further testing, is: **a canonical output value can be too coarse to classify the computations/families that produced it.**

### 2.5 Scalene, isosceles, equilateral: symmetry strata

The transition

`scalene -> isosceles -> equilateral`

is a small model of a common phenomenon: stabilizer groups can jump on special loci. Generic points have one symmetry type; special subspaces have larger automorphism groups.

This is exactly the information that an ordinary quotient is tempted to crush. A stack preserves it.

For testing a future “stack-aware” computational abstraction, this triangle hierarchy is an excellent first fixture because the expected stabilizers can be stated independently of any implementation.

### 2.6 Oriented triangles

Adding orientation changes the moduli problem rather than merely adding a Boolean field. Some permutations preserve orientation and some reverse it; the acting symmetry group changes, and therefore so do stabilizers, quotient geometry, and fundamental group.

This is a useful warning for language design: **what counts as equivalence is part of the type/problem statement.** One cannot first construct “the” moduli space and decide afterward whether orientation was meaningful.

### 2.7 Versal families

A **versal family** is a local parameter family rich enough to model all local deformations/families in the specified problem, but it need not classify them uniquely.

That distinction matters:

- *universal* suggests a unique classifying pullback;
- *versal* says, roughly, “locally, every behavior of interest occurs through here,” without demanding uniqueness.

Behrend's triangle parameter spaces act as versal families. Their symmetry groupoids tell us when different parameter values describe isomorphic objects. A general family can be reconstructed by local parameter choices together with transition/symmetry data on overlaps.

For the renderer experiment, “versal” would be a meaningful word only after we define a class of admissible local variations. Then a parameterization could be tested for whether every such local variation factors through it. Until that exists, `versal` should remain research vocabulary, not a keyword.

### 2.8 Generalized moduli maps

An ordinary moduli map sends a base point to the moduli point representing its fiber. That is too weak when local choices differ by symmetry.

In the group-action model, the richer classifying data are essentially:

- a principal `G`-bundle/cover over the base; and
- a `G`-equivariant map into the parameter space.

Local sections produce ordinary-looking parameter maps, but changes of section are tracked by `G`. Thus the “map to moduli” itself has symmetry/gluing data.

This is a major conceptual step: classification is not necessarily a function into a set of classes. It may be a map into a stack.

### 2.9 Degenerate triangles and compactification

Allowing degenerations forces another design decision: what objects should live at the boundary of the parameter space? Behrend studies degenerate triangles and compactifications rather than pretending that a family simply stops existing when a triangle flattens.

This illustrates a general moduli principle: a useful moduli space is often obtained by adding controlled limiting objects. The art is not “include every malformed object,” but choose boundary objects for which families have good limiting behavior.

In programming terms, this suggests a distinction between an invalid state and a deliberately admitted boundary/limit state. That analogy is speculative, but compactification is worth remembering whenever a parameterized computation approaches a singular or degenerate case.

### 2.10 Weierstrass compactification and the `j`-line

Behrend relates oriented triangle data to cubic polynomials/Weierstrass forms and ultimately to the familiar moduli story around elliptic curves and the `j`-invariant. This is one reason the triangle example is deeper than a toy: it opens into a standard algebraic moduli problem.

The broad lesson is that a concrete geometric family can admit several presentations—triangles, configurations of points, roots of cubics, quotient descriptions—while the underlying moduli problem remains the organizing object.

### 2.11 Stacks as descent/gluing

A **prestack** knows objects over bases and isomorphisms between them with pullback. A **stack** adds an effective descent condition: compatible local objects and local isomorphisms can be glued to a global object, and the gluing is coherent.

This is more than “a quotient with automorphisms.” Quotient stacks are important examples, but descent is the structural idea that makes stacks work across arbitrary covers.

Potential computational analogy: if a semantic object is specified by local descriptions, then equality of the global object should depend on coherent transition data, not on selecting one privileged presentation.

### 2.12 Torsors and quotient stacks

For a group action `G` on `X`, the quotient stack `[X/G]` remembers more than the coarse quotient. An object of `[X/G]` over a base can be described by a principal `G`-bundle over the base together with a `G`-equivariant map to `X`.

This is the cleanest mathematical model for “quotient without deleting the witness of sameness.”

At a point `x`, the stabilizer subgroup consists of transformations that carry `x` to itself. In more general stack language, automorphisms of objects assemble into the **inertia stack**.

### 2.13 Change of versal family and Morita equivalence

A stack may have many groupoid presentations. Different atlases/versal families can describe the same stack. **Morita equivalence** is the appropriate notion saying that two groupoids present the same stacky object.

This may be one of the most interesting ideas for compiler architecture: two concrete implementation/presentation systems might be genuinely different while presenting the same higher semantic object. That analogy should be tested, not assumed, but it is stronger than comparing outputs by a single equality test.

### 2.14 Deligne–Mumford stacks and orbifolds

A Deligne–Mumford stack is, very roughly, a stack whose local presentation is especially well behaved (algebraically: it admits an étale atlas; Behrend develops a parallel topological picture). Orbifolds are closely related differential/topological examples: locally quotient-like spaces with finite isotropy/stabilizer data retained.

The triangle examples often behave orbifold-like because special triangles have finite symmetry groups.

### 2.15 Fundamental groups of stacks

Because a stack retains isotropy, its fundamental group can differ dramatically from the fundamental group of its coarse moduli space.

Behrend computes examples in which the stack of triangles carries nontrivial fundamental-group information even when a coarse quotient is very simple. In particular, the nondegenerate unoriented triangle stack records an `S3` fundamental-group phenomenon, while the oriented version changes the group.

That is another direct demonstration that “take the quotient set/space” can erase real topology.

### 2.16 Algebraic stacks and stacky curves

In the algebraic part Behrend replaces topological spaces/groupoids by schemes and algebraic groupoids, defines algebraic stacks through suitable presentations, and develops stacky curves and Riemann–Roch.

A one-dimensional smooth separated Deligne–Mumford stack with an irreducible coarse curve can be thought of as a curve carrying stack structure at points with stabilizers. Riemann–Roch must then account for this local isotropy rather than pretending the curve is ordinary everywhere.

### 2.17 What Behrend gives us—and what he does not

Behrend gives unusually good language for:

- objects varying in families;
- equivalence with explicit symmetry;
- stabilizers/automorphisms that change across the parameter space;
- coarse versus fine classification;
- local parameter families and versality;
- gluing local descriptions;
- quotienting without discarding symmetry;
- changing presentations without changing the represented stack;
- controlled boundary objects/compactification.

Behrend's chapter does **not** by itself give us:

- a metric notion of renderer similarity;
- Lipschitz or numerical sensitivity bounds;
- a language design for dependent types;
- a general theorem that compiler implementations should form an algebraic stack;
- a replacement for statistical experimentation;
- a ready-made notion of visual equivalence.

Those require additional structures.

---

## 3. The rest of the 2014 Cambridge book *Moduli Spaces*

The book is edited by Leticia Brambila-Paz, Oscar García-Prada, Peter Newstead, and Richard P. Thomas (LMS Lecture Note Series 411). It contains six chapters. Behrend is chapter 1; the other five show how broad “moduli” really is.

### Chapter 1 — Kai Behrend, *Introduction to algebraic stacks*, pp. 1–131

Detailed above. The core theme is that moduli problems with automorphisms are naturally stacky: families and their isomorphisms matter, not only the set of isomorphism classes.

### Chapter 2 — W.-Y. Chuang, D.-E. Diaconescu, G. Pan, *BPS states and the P = W conjecture*, pp. 132–150

Public version: <https://arxiv.org/abs/1202.2039>

This chapter connects the topology/cohomology of character and Hitchin moduli spaces to BPS states in a local Calabi–Yau threefold. It is motivated by work around the Hausel–Rodriguez-Villegas conjectures and the `P=W` phenomenon of de Cataldo–Hausel–Migliorini.

For this note, the important lesson is not the physics detail but that **one moduli space can carry several filtrations/interpretations whose equality is deep rather than tautological**. Geometry, topology, and physical state counting can be different presentations of related structure.

### Chapter 3 — Peter B. Gothen, *Representations of surface groups and Higgs bundles*, pp. 151–178

Public version: <https://arxiv.org/abs/1209.0568>

The chapter introduces surface-group representations, character varieties, flat bundles, Higgs bundles, harmonic metrics, and the nonabelian-Hodge bridge between representation moduli and Higgs-bundle moduli.

A character variety is itself a quotient/moduli construction: representations of a surface fundamental group into a Lie group, modulo conjugation. Higgs bundles provide another geometric incarnation of closely related data. The chapter is therefore a concrete example of “same underlying mathematical phenomenon, radically different coordinates/presentations.”

This chapter also ties directly to mapping class groups: mapping classes act naturally on surface-group representations/character varieties because they act on the surface fundamental group.

### Chapter 4 — Daniel Huybrechts, *Introduction to stability conditions*, pp. 179–229

Public version: <https://arxiv.org/abs/1111.1745>

Huybrechts develops Bridgeland/Douglas stability conditions on abelian and triangulated categories, with special attention to derived categories of coherent sheaves, curves, and K3 surfaces.

The word **stability** is crucial across moduli theory. Often a naive classification problem is too badly behaved, and one restricts to stable/semistable objects so that a meaningful moduli construction exists. Stability is therefore not simply a numerical “robustness score”; it is often a structural criterion controlling which objects admit manageable quotients and degenerations.

Programming-language analogy to investigate: admissible variants may need a stability condition, not merely a tolerance interval. But this is only an analogy until a computational stability notion is defined.

### Chapter 5 — Dominic Joyce, *An introduction to d-manifolds and derived differential geometry*, pp. 230–281

Public version: <https://arxiv.org/abs/1206.4207>

Author page: <https://people.maths.ox.ac.uk/joyce/dmanifolds.html>

Joyce explains derived smooth-geometric objects (`d`-manifolds and `d`-orbifolds) designed to retain information when intersections/moduli problems are not transverse in the ordinary sense. Such spaces can carry virtual classes and connect to Kuranishi spaces, polyfolds, and perfect obstruction theories.

For the current design question, the important word is **obstruction**. A first-order variation can look locally allowable yet fail to extend to an actual nearby object. Derived/deformation-theoretic structures remember this failed transversality rather than flattening everything into an ordinary dimension count.

### Chapter 6 — R. Pandharipande and R. P. Thomas, *13/2 ways of counting curves*, pp. 282–333

Public version: <https://arxiv.org/abs/1111.1552>

This survey compares multiple compactifications/enumerative theories for curves in an algebraic variety: stable maps, Hilbert-scheme/Donaldson–Thomas approaches, stable pairs, unramified maps, stable quotients, and related theories. A recurring common structure is a two-term deformation/obstruction theory used to construct a virtual fundamental class, especially for threefolds.

This is an excellent warning against asking for **the** representation of a family. Different compactifications can preserve different aspects of the geometry and produce different enumerative theories, even when they start from the same open locus of smooth curves.

---

## 4. The Stacks Project: where to look next

The Stacks Project is much larger and more systematic than any one set of lecture notes. For the present question, the useful path is not “read it all in order.” The relevant clusters are:

### 4.1 Algebraic stacks

Start with the chapters around:

- **Algebraic Stacks**: <https://stacks.math.columbia.edu/tag/026N>
- **Examples of Stacks**: <https://stacks.math.columbia.edu/tag/04UW>
- **Properties of Algebraic Stacks** and **Morphisms of Algebraic Stacks** from the Algebraic Stacks part of the project.

A modern algebraic stack is a stack in groupoids satisfying representability/atlas conditions. The exact hypotheses matter, but the design idea stays stable: objects vary over bases, isomorphisms are retained, and local presentations can be glued.

### 4.2 Inertia: “sameness that remains at a point”

The **inertia stack** packages automorphisms of objects. In a quotient stack `[X/G]`, inertia sees fixed-point/stabilizer data that the coarse quotient loses.

For the current project this is a more standard mathematical replacement for “axes of sameness” than inventing a new primitive too early.

Useful starting point: search the Stacks Project for “inertia stack” and quotient-stack examples; one explicit section is <https://stacks.math.columbia.edu/tag/06PA>.

### 4.3 Deformation theory

The Stacks Project has a full deformation-theory part. Relevant chapters include:

- Formal Deformation Theory;
- Deformation Theory;
- The Cotangent Complex;
- Deformation Problems.

Entry point: <https://stacks.math.columbia.edu/>

The recurring pattern is:

1. define a deformation problem over small/thickened bases;
2. identify infinitesimal automorphisms;
3. identify first-order deformation classes;
4. identify obstruction classes controlling extension.

This is much closer to the “controlled transformation” idea than stacks alone.

### 4.4 Cotangent and tangent complexes

For an ordinary smooth space, the tangent space describes first-order directions. In moduli problems with automorphisms and obstructions, one vector space is often not enough. A tangent/cotangent **complex** can place automorphism, deformation, and obstruction information in different degrees.

For a smooth quotient stack `[X/G]`, a useful heuristic local model is a two-term complex built from

`Lie(G) -> T_x X`,

where the map is the infinitesimal action. Degree conventions vary, so the slogan matters more here than a fixed indexing convention:

- the group-action term records infinitesimal symmetry directions;
- the tangent term records infinitesimal movement in the presentation;
- the resulting complex distinguishes symmetry from genuine deformation rather than simply subtracting dimensions.

This is a mathematically serious version of “variation with some directions declared equivalent.”

Cotangent-complex entry points:

- rings: <https://stacks.math.columbia.edu/tag/08P5>
- morphisms of schemes and further geometric versions can be followed from the Cotangent Complex chapter.

### 4.5 Infinitesimal deformations of algebraic stacks

The Stacks Project also treats infinitesimal deformations of algebraic stacks themselves. This matters because the thing varying need not be merely a point *inside* a fixed stack; the stack/presentation may itself vary.

Useful sections include the “Infinitesimal deformations” material in the geometry-of-stacks chapters (for example tags around <https://stacks.math.columbia.edu/tag/0DZR> and neighboring sections; verify exact hypotheses when using a theorem).

### 4.6 Artin's axioms / representability

A recurring meta-question in moduli theory is: when does a moduli functor/problem actually come from an algebraic stack? Artin-style representability criteria use deformation theory, effectivity, limit behavior, automorphisms, and other local/global conditions.

For programming-language work, this is a useful antidote to hand-waving: declaring “this family should be a stack” is not enough. One should eventually state the data and prove whichever representability/closure properties are actually needed.

### 4.7 Moduli of curves

The Stacks Project's moduli-of-curves material is a natural bridge from Behrend's triangles to mapping class groups and Teichmüller theory. Stable curves show how compactification, automorphisms, deformation, and coarse-vs-stacky moduli all interact in a canonical classical example.

---

## 5. Mapping class group primer

### 5.1 Definition

Let `S = S_{g,n}` be an oriented surface of genus `g` with `n` marked points/punctures (boundary conventions must be stated separately). Its orientation-preserving mapping class group is

`Mod(S) = Homeo^+(S) / Homeo_0(S)`,

or equivalently orientation-preserving diffeomorphisms modulo isotopy in the standard smooth setting.

An element of `Mod(S)` is therefore not a particular map of the surface but a **deformation class of maps**: maps that can be continuously deformed into one another through admissible homeomorphisms/diffeomorphisms count as the same mapping class.

This is almost a perfect toy example for the current vocabulary problem. There is a large space/group of concrete transformations, and then a carefully chosen relation—**isotopy**—that declares many of those transformations equivalent.

Public introduction: Yair Minsky, *A Brief Introduction to Mapping Class Groups*, <https://gauss.math.yale.edu/~yhm3/research/PCMI.pdf>.

### 5.2 Why the group matters

Mapping class groups arise naturally in:

- gluing 3-manifolds along surfaces;
- classifying surface bundles;
- symmetries of Teichmüller space;
- moduli of conformal/hyperbolic structures;
- dynamics of surface maps;
- character varieties and surface-group representations.

The group is simultaneously topological, geometric, algebraic, and dynamical.

### 5.3 Curves and geometric intersection

Essential simple closed curves on `S`, considered up to isotopy, are basic probes of a mapping class. Two curve classes have a **geometric intersection number**: the minimum number of intersection points among representatives.

The mapping class group acts on isotopy classes of curves while preserving intersection data.

This gives a useful general pattern: instead of comparing complicated transformations directly, study how they act on a structured family of probes.

### 5.4 Dehn twists

A **Dehn twist** about an essential simple closed curve cuts a collar neighborhood, twists one side through a full turn, and reglues. Its mapping class depends only on the isotopy class of the curve.

Dehn twists are basic infinite-order elements and generate mapping class groups in standard finite-generation theorems.

Two relations worth remembering:

- twists about disjoint curves commute;
- twists about curves intersecting once satisfy the braid relation.

This is a concrete demonstration that a global transformation group can be built from local geometric moves plus relations.

### 5.5 Action on homology and the Torelli group

A mapping class acts on first homology `H_1(S; Z)` and preserves the algebraic intersection pairing. For a closed oriented genus-`g` surface this gives a homomorphism

`Mod(S_g) -> Sp(2g, Z)`.

The kernel is the **Torelli group**. Thus homology gives a coarse linear shadow of a much richer transformation group. Two mapping classes can have exactly the same action on homology while being genuinely different.

That is another useful “coarse invariant versus full object” example.

### 5.6 Dehn–Nielsen–Baer

The Dehn–Nielsen–Baer theorem relates mapping class groups to outer automorphisms of the surface fundamental group, with suitable orientation/peripheral conditions. This converts a geometric deformation problem into an algebraic automorphism problem.

Again: the same structure admits different presentations.

### 5.7 Birman exact sequence and point-pushing

When a marked point/puncture is added, moving that point around a loop produces a mapping class. The **Birman exact sequence** formalizes the relation between mapping class groups with and without the marked point and the fundamental group of the surface/configuration space.

For design purposes, it is an instructive example of how adding one piece of “state” changes the symmetry group in a controlled exact way.

### 5.8 Nielsen–Thurston classification

Mapping classes fall into three broad dynamical types:

1. **periodic** — finite order in the mapping class group;
2. **reducible** — preserves an essential multicurve;
3. **pseudo-Anosov** — has invariant transverse measured foliations/laminations scaled by factors `lambda` and `1/lambda`, with `lambda > 1`.

Pseudo-Anosov maps show that a mapping class can have a canonical quantitative expansion/contraction rate. This is relevant to “controlled transformation,” but it is a very particular geometric/dynamical structure—not a generic sensitivity metric.

### 5.9 Teichmüller space and moduli

**Teichmüller space** `T_g` parameterizes marked conformal/hyperbolic structures on a surface. The mapping class group changes the marking, so it acts on `T_g`.

The ordinary quotient is the coarse moduli space of curves/Riemann surfaces:

`M_g ~= T_g / Mod_g`

at the level of the familiar orbifold/coarse picture.

But special Riemann surfaces have nontrivial automorphism groups. Therefore the quotient **stack**

`[T_g / Mod_g]`

retains stabilizers that the coarse quotient forgets. This is exactly the same structural lesson as Behrend's isosceles/equilateral triangles, now in a central moduli problem.

### 5.10 Braid groups

Braid groups occur as mapping class groups of punctured disks (with the usual boundary/puncture conventions), and mapping class groups of punctured spheres are closely related. This is useful because braid groups give an extremely concrete combinatorial model of “motions modulo deformation.”

A future computational test might therefore use braids before attempting full surfaces: different motion paths can represent the same braid after isotopy, while braid composition remains explicit.

### 5.11 Recommended mapping-class sources

- Benson Farb and Dan Margalit, *A Primer on Mapping Class Groups*, Princeton Mathematical Series 49 (2012). Author book page: <https://www.math.uchicago.edu/~farb/books.html>
- Yair Minsky, *A Brief Introduction to Mapping Class Groups*: <https://gauss.math.yale.edu/~yhm3/research/PCMI.pdf>
- Benson Farb, Richard Hain, Eduard Looijenga (eds.), *Moduli Spaces of Riemann Surfaces*, IAS/Park City Mathematics Series 20 (2013): <https://bookstore.ams.org/pcms-20>

The PCMI volume is especially useful because mapping class groups, Teichmüller theory, moduli spaces, Torelli groups, tautological classes, Mirzakhani's volume recursion, and related topics are presented as one connected landscape rather than isolated subjects.

---

## 6. Other books and sources worth keeping beside Behrend

### 6.1 Farb–Hain–Looijenga (eds.), *Moduli Spaces of Riemann Surfaces*

<https://bookstore.ams.org/pcms-20>

This is probably the best companion here for the topological side. It contains Minsky's mapping-class-group introduction and material on Teichmüller theory, the Mumford conjecture/Madsen–Weiss theorem, homological stability, Torelli and congruence subgroups, tautological algebras, Mirzakhani's recursion/volumes, Teichmüller curves, and arithmetic mapping class groups.

Read it when the central objects are surfaces and one wants to understand both the parameter space and the group acting on markings.

### 6.2 Farb–Margalit, *A Primer on Mapping Class Groups*

<https://www.math.uchicago.edu/~farb/books.html>

A systematic route through curves/surfaces/hyperbolic geometry, mapping class groups, Dehn twists, generators and relations, symplectic representation and Torelli, torsion, Dehn–Nielsen–Baer, braid groups, Teichmüller space, moduli space, and Nielsen–Thurston theory.

Read this when `Mod(S)` itself becomes the object of study rather than only the symmetry group acting on a moduli space.

### 6.3 Mumford–Fogarty–Kirwan, *Geometric Invariant Theory*

Publisher: <https://link.springer.com/book/9783540569633>

GIT studies algebraic group actions, invariants, stability, and construction of quotients/moduli spaces. Naively taking an orbit set often gives an algebraically bad quotient; GIT modifies the question using linearization and stable/semistable loci.

The third edition adds material on the **moment map**, making it particularly relevant to the parallel symplectic-geometry notes. The bridge between GIT quotients and symplectic reduction is one of the places where algebraic and symplectic moduli ideas meet concretely.

Read this when the problem is “we know the group action; how do we construct a usable quotient?”

### 6.4 P. E. Newstead, *Introduction to Moduli Problems and Orbit Spaces*

A classic shorter introduction to GIT and its use in constructing moduli of vector bundles on curves. Public bibliographic/description pages emphasize that it grew from TIFR lectures and became a standard entry point.

Useful public metadata: <https://books.google.com/books/about/Introduction_to_Moduli_Problems_and_Orbi.html?id=W7WYuAAACAAJ>

Read this before the full GIT book if the latter is too large a first jump.

### 6.5 Edoardo Sernesi, *Deformations of Algebraic Schemes*

Public institutional description: <https://iris.uniroma3.it/handle/11590/178466>

This is the most directly relevant classical book for the current word **deformation**. It develops local deformation theory, infinitesimal deformations, formal deformation theory, deformation functors, and tools for local study of Hilbert schemes and moduli problems.

The conceptual package to steal as mathematics—not yet as syntax—is:

- deformation functor;
- tangent space to the deformation problem;
- infinitesimal automorphisms;
- obstruction space/class;
- versality and smoothness.

Read this when the question changes from “what objects are equivalent?” to “which small changes actually extend to nearby objects?”

### 6.6 Daniel Huybrechts and Manfred Lehn, *The Geometry of Moduli Spaces of Sheaves*

Cambridge page: <https://www.cambridge.org/core/books/the-geometry-of-moduli-spaces-of-sheaves/E69325DA1892E9BA762E354C4C64E337>

The book develops families of sheaves, moduli spaces, and then the geometry of moduli on surfaces, including K3 surfaces, restriction theorems, line bundles, irreducibility/smoothness, symplectic structures, and birational geometry.

It is a good example where “family” is not an informal parameter list: flatness, stability, universal families, and geometric structure of the resulting moduli space matter.

### 6.7 János Kollár, *Families of Varieties of General Type*

Author page with public drafts/final-form material: <https://web.math.princeton.edu/~kollar/>

Cambridge overview: <https://www.cambridge.org/core/books/families-of-varieties-of-general-type/>

Kollár generalizes the moduli theory of stable curves toward higher-dimensional varieties of general type. The book treats one-parameter families, stable varieties/pairs, flatness conditions, moduli of stable pairs, hulls/husks, and minimal-model singularities.

This is especially relevant for **compactification and boundary behavior**. The correct higher-dimensional moduli problem depends on choosing the right singular limiting objects and controlling families over degenerations.

### 6.8 Dominic Joyce, derived differential geometry

Author page: <https://people.maths.ox.ac.uk/joyce/dmanifolds.html>

Joyce explicitly presents d-manifolds/d-orbifolds as derived enhancements capable of retaining obstruction/intersection information that ordinary smooth manifolds lose. This is a useful parallel track if the eventual computational examples are analytic/differential rather than algebraic.

---

## 7. Stack semantics already exists in dependent type theory

Thierry Coquand, Bassel Mannaa, and Fabian Ruch, **“Stack Semantics of Type Theory”** (LICS 2017):

- arXiv: <https://arxiv.org/abs/1701.02571>
- institutional record: <https://pure.itu.dk/en/publications/stack-semantics-of-type-theory/>

They construct a model of dependent type theory with a univalent universe and propositional truncation in which a type is interpreted as a **stack**, generalizing the groupoid model of type theory. They use the model to establish metatheoretic independence results, including failure of derivability of countable choice in the system considered.

This matters because it proves that the phrase “stack semantics of dependent types” has literal mathematical precedent. It does **not** prove any of the following:

- that the syntax of a practical language should contain an explicit `stack` keyword;
- that algebraic stacks are the right runtime data structure;
- that renderer tolerances are stack structure;
- that this model provides numerical sensitivity analysis;
- that Idriç should simply implement this paper.

The correct research question is narrower: **which distinctions preserved by groupoid/stack semantics are useful enough to expose in a programming language or compiler specification?**

Nearby intellectual territory includes the groupoid model of type theory, homotopy type theory, univalence, higher inductive types, and higher-categorical semantics. Those should be compared before committing to “algebraic stacks” as the unique foundation.

---

## 8. Vocabulary map for “sameness and variation”

| Informal design phrase | Established mathematical candidates | Warning |
| --- | --- | --- |
| same object | equality; isomorphism; equivalence; path/identity type | These are not interchangeable. |
| same up to allowed transformation | action groupoid; quotient stack; homotopy quotient | Must specify the transformation group/groupoid. |
| transformations that leave the object unchanged | stabilizer; automorphism group; isotropy; inertia | Can jump on special loci. |
| axis/direction of sameness | orbit/gauge direction; infinitesimal automorphism | “Axis of sameness” is not standard terminology. |
| genuine nearby change | deformation; tangent direction/class | Depends on the moduli/deformation problem. |
| all nearby changes are represented | versal family; smooth morphism in suitable deformation setting | Versal does not mean unique. |
| one canonical parameterization | universal/fine moduli when it exists | Often impossible because of automorphisms. |
| just the class label | coarse moduli space / coarse quotient | Loses stabilizers/family information. |
| local descriptions agree globally | descent; stack condition | Requires coherent gluing, not pairwise equality alone. |
| a variation looks possible but cannot continue | obstruction | Needs a deformation theory. |
| linearized variation | tangent space; tangent complex; derivative | Tangent complex is richer when automorphisms/obstructions occur. |
| how an input perturbation changes output | derivative/Jacobian; tangent map; sensitivity | Analytic/numerical structure, not supplied merely by stacks. |
| bounded propagation | operator norm; Lipschitz/Hölder bound; condition number | Requires metrics/norms. |
| behavior at singular/limit cases | compactification; degeneration; stable reduction | Choosing boundary objects is part of the model. |
| different coordinate systems for same semantic object | atlases/presentations; Morita-equivalent groupoids | Analogy to compiler presentations requires proof/design. |
| transformation modulo continuous deformation | mapping class / isotopy class | Surface-specific but excellent toy model. |
| well-behaved quotient subset | stable/semistable locus (GIT/Bridgeland etc.) | “Stable” has theory-specific definitions. |

The important design discipline is to avoid giving one keyword several of these meanings.

---

## 9. Separating stack structure from sensitivity structure

The original programming idea mixes two kinds of mathematics that should initially remain separate.

### 9.1 Equivalence/symmetry layer

Suppose `P` is a parameter/presentation space and a group or groupoid `G` acts by transformations we intend to count as equivalent. Then the quotient stack

`[P / G]`

retains the transformations witnessing equivalence. Stabilizers say which symmetries a particular point possesses.

This is the stack/moduli part.

### 9.2 Differential/sensitivity layer

Suppose a computation depends on a parameter `theta`:

`f_theta(x)`.

A first-order perturbation `delta theta` propagates through a derivative/tangent map

`delta theta -> D_theta f (delta theta)`.

A norm bound such as

`||D_theta f(delta theta)|| <= L ||delta theta||`

is an analytic statement about sensitivity. A finite-change Lipschitz condition is stronger/different and requires a metric or norm on both sides.

This is not automatically stack theory.

### 9.3 The interesting intersection

The interesting question is what happens when some perturbation directions are generated by equivalences. Then a tangent complex or quotient construction can distinguish:

- infinitesimal motion that merely changes presentation;
- infinitesimal motion that changes the represented object;
- potentially obstructed directions that do not integrate to actual families.

Only after that quotient/equivalence structure is clear should a metric be placed on the genuine deformation directions and sensitivity bounds asked for.

In other words:

`equivalence structure` + `deformation structure` + `metric/analytic structure`

are three layers. Conflating them would make the language less precise, not more.

---

## 10. Candidate programming-language experiments

Everything in this section is **speculative**. Each proposal should be tested against mathematics and against ordinary programming cases before entering a language surface.

### 10.1 Test A: triangle moduli as a conformance fixture

Represent a family of triangles and explicitly state:

- whether vertices are labelled;
- whether orientation matters;
- equivalence relation/group action;
- stabilizer of representative scalene, isosceles, equilateral cases;
- coarse quotient;
- richer groupoid/stack presentation.

Tests should fail if an implementation collapses the three symmetry types merely because their coarse parameter values exist in one continuous space.

### 10.2 Test B: renderer family

Start with one target drawing and a deliberately small family of transformations:

- translation;
- rotation;
- uniform scaling;
- palette change;
- line-width change;
- controlled geometric deformation;
- topology-changing deformation, if any.

For each transformation class, state whether it is:

- exact sameness;
- accepted equivalence;
- accepted deformation with a measured size;
- forbidden change.

Do **not** begin with “looks the same.” Define invariants or metrics separately. A shader/backend is deterministic once parameters are fixed; the family is where designed variation lives.

### 10.3 Test C: mapping-class toy

Use a punctured disk/braid or a low-genus surface.

Represent concrete transformations, then quotient by isotopy. Verify that:

- two distinct motion paths can represent the same mapping class;
- composition descends to mapping classes;
- a coarse invariant such as homology action can identify transformations that remain distinct in the full mapping class group.

This tests “sameness of transformations” rather than “sameness of values.”

### 10.4 Test D: two compiler/backend presentations

Suppose two compilers or shader backends implement the same intended semantic family using different intermediate representations.

A serious equivalence claim should identify:

- the semantic objects being represented;
- the maps from each presentation to them;
- transformations inside each presentation that count as inessential;
- invariants preserved by lowering;
- what evidence witnesses equivalence;
- where equivalence is only approximate/metric rather than exact.

If the appropriate structure really is Morita-like, that should emerge from this example. Do not decree it in advance.

### 10.5 Test E: deformation/obstruction fixture

Construct a tiny problem with a first-order perturbation that cannot be extended globally. The goal is to make **obstruction** operational rather than poetic.

A language feature is not justified until a test can distinguish:

- an admissible tangent direction;
- an integrable/extendable deformation;
- an obstructed infinitesimal proposal.

### 10.6 Test F: coarse versus rich output

Make the same computation expose two views:

1. coarse result: canonical class/value only;
2. rich result: representative + automorphisms/equivalence witnesses + deformation metadata.

Then identify operations that are valid on the rich result but impossible after coarse projection. This would directly test whether retaining stack-like information buys anything computationally.

---

## 11. Possible design principles, stated cautiously

1. **Do not quotient away the evidence that explains why two things count as the same unless the caller explicitly asks for the coarse quotient.**
2. **Treat the equivalence notion as part of the problem specification.** Orientation, labels, gauge choices, tolerances, and isotopies cannot be retrofitted after quotienting.
3. **Families are primary.** A parameterized object has structure that the set of its fibers does not capture.
4. **Automorphisms matter.** Two objects can occupy equally simple coarse positions while having different symmetry groups.
5. **Local completeness and uniqueness are different properties.** “Versal” and “universal” should never be used as decorative synonyms.
6. **Deformation is not sensitivity.** Deformation theory asks which nearby objects exist and how they extend; sensitivity additionally asks how large a response is under a chosen metric/norm.
7. **Obstructions deserve first-class tests before first-class syntax.**
8. **Different presentations may be genuinely equivalent without being textually or structurally identical.** Groupoid/stack Morita equivalence is a mathematical model worth comparing with compiler representations.
9. **Compactification is a design choice.** Boundary cases should be intentionally admitted, not accidentally inherited.
10. **Do not call the language “stacky” until examples force stack structure rather than merely group actions or ordinary dependent types.**

---

## 12. Reading order for this project

A practical sequence, chosen for conceptual leverage rather than historical order:

1. **Behrend's triangle sections** — learn exactly why the coarse quotient fails.
2. **Minsky's mapping-class notes** — study transformations modulo isotopy in an extremely concrete setting.
3. **Behrend on stacks/torsors/Morita** — understand how local presentations glue and change.
4. **Stacks Project deformation chapters** — separate automorphisms, deformations, and obstructions.
5. **Sernesi** — get classical deformation-functor/versal/obstruction vocabulary straight.
6. **Farb–Margalit / PCMI Riemann-surface volume** — connect mapping class groups, Teichmüller space, and moduli.
7. **Mumford–Fogarty–Kirwan GIT** — learn how stability and quotients are constructed, including the moment-map bridge.
8. **Coquand–Mannaa–Ruch** — compare actual stack semantics of dependent type theory with the programming-language speculation here.
9. **Joyce + Pandharipande–Thomas** — see why derived/obstruction structures and multiple compactifications become unavoidable in serious moduli problems.
10. **Kollár** — study controlled degeneration and stable higher-dimensional families.

---

## 13. Source index

### Behrend / Cambridge *Moduli Spaces*

- Kai Behrend, *Introduction to Algebraic Stacks*, public course PDF: <https://personal.math.ubc.ca/~behrend/math615A/stacksintro.pdf>
- Course page: <https://personal.math.ubc.ca/~behrend/math615A/>
- Cambridge chapter/book landing page: <https://www.cambridge.org/core/books/moduli-spaces/>
- W.-Y. Chuang, D.-E. Diaconescu, G. Pan, *BPS states and the P = W conjecture*: <https://arxiv.org/abs/1202.2039>
- Peter B. Gothen, *Representations of surface groups and Higgs bundles*: <https://arxiv.org/abs/1209.0568>
- Daniel Huybrechts, *Introduction to stability conditions*: <https://arxiv.org/abs/1111.1745>
- Dominic Joyce, *An introduction to d-manifolds and derived differential geometry*: <https://arxiv.org/abs/1206.4207>
- R. Pandharipande, R. P. Thomas, *13/2 ways of counting curves*: <https://arxiv.org/abs/1111.1552>

### Stacks / type theory

- The Stacks Project: <https://stacks.math.columbia.edu/>
- Stacks Project licence: <https://github.com/stacks/stacks-project/blob/master/COPYING>
- Thierry Coquand, Bassel Mannaa, Fabian Ruch, *Stack Semantics of Type Theory*: <https://arxiv.org/abs/1701.02571>
- Institutional record: <https://pure.itu.dk/en/publications/stack-semantics-of-type-theory/>

### Mapping class groups / Riemann surfaces

- Yair Minsky, *A Brief Introduction to Mapping Class Groups*: <https://gauss.math.yale.edu/~yhm3/research/PCMI.pdf>
- Benson Farb and Dan Margalit, *A Primer on Mapping Class Groups*, author book page: <https://www.math.uchicago.edu/~farb/books.html>
- Benson Farb, Richard Hain, Eduard Looijenga (eds.), *Moduli Spaces of Riemann Surfaces*: <https://bookstore.ams.org/pcms-20>

### Other moduli/deformation references

- David Mumford, John Fogarty, Frances Kirwan, *Geometric Invariant Theory*: <https://link.springer.com/book/9783540569633>
- P. E. Newstead, *Introduction to Moduli Problems and Orbit Spaces*: <https://books.google.com/books/about/Introduction_to_Moduli_Problems_and_Orbi.html?id=W7WYuAAACAAJ>
- Edoardo Sernesi, *Deformations of Algebraic Schemes*: <https://iris.uniroma3.it/handle/11590/178466>
- Daniel Huybrechts, Manfred Lehn, *The Geometry of Moduli Spaces of Sheaves*: <https://www.cambridge.org/core/books/the-geometry-of-moduli-spaces-of-sheaves/E69325DA1892E9BA762E354C4C64E337>
- János Kollár, author page with *Families of Varieties of General Type* drafts/final-form material: <https://web.math.princeton.edu/~kollar/>
- Dominic Joyce, d-manifolds/d-orbifolds page: <https://people.maths.ox.ac.uk/joyce/dmanifolds.html>

---

## 14. Immediate research questions before language design

These are deliberately questions, not proposed features.

1. For a renderer, what exact group/groupoid of transformations should count as equivalence rather than deformation?
2. Can we exhibit a case where retaining an automorphism/stabilizer changes a compiler decision that a coarse value would not support?
3. What is the smallest computational example with a genuine obstruction theory rather than just a failed constraint check?
4. Do dependent identity types / HoTT already provide the needed equivalence bookkeeping without importing algebraic-stack machinery?
5. When parameters have norms or metrics, can sensitivity be defined on a quotient/moduli object invariantly rather than on arbitrary coordinates?
6. Which “gauge directions” should be factored out before computing a condition number?
7. Is a versal-family analogy useful for API/backend parameter coverage, and can it be expressed as a testable lifting property?
8. What is the computational analogue, if any, of changing atlas/presentation by Morita equivalence?
9. When a parameter family degenerates, what should count as a stable boundary object rather than an error?
10. Can one build a tiny example where coarse equality says “same,” but stacky data correctly predicts different local deformation behavior?

If these questions do not produce compelling examples, stacks should remain explanatory mathematics rather than language machinery.

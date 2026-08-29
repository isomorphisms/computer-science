# Rotations, reflections, and the topology of `SO(n)` — discussion notes

This note records the line of thought behind the rotations/reflections study so it can be read later as a mathematical and compiler-design discussion rather than reconstructed from chat history.

**Maintenance status:** this is a narrative synthesis. The focused notes linked from [`README.md`](README.md) are canonical for exact semantics, theorem scope, planner metadata, dependencies, sources, and redistribution boundaries. If this narrative and a focused note diverge, repair or defer to the focused note rather than treating both as independent authorities.

## 1. The starting intuition

The motivating question was whether the cohomology of `SO(n)` might have anything to say about how one should think about moving from one orientation or direction to another by reflections or by “one-by-one” rotations.

The careful answer is:

- **yes, there is a real connection**;
- **no, cohomology is not itself an algorithm that tells us which elementary rotation to execute next**;
- the topology becomes relevant when we ask whether whole *families* of decompositions can be chosen continuously, globally, uniquely, or without singular cases.

That distinction is worth preserving because it separates three layers that are easy to conflate.

### Factorization problem

Given an individual matrix or vector-alignment problem, produce a concrete factorization:

```text
A = G1 G2 ... Gk
```

where the `Gi` might be coordinate-plane Givens rotations, Householder reflectors, or products of reflections.

This is mainly numerical linear algebra.

### Planning problem

Given a semantic goal such as

```text
move this direction to e1
preserve norm
require determinant +1
possibly carry the transform along to other data
```

choose which correct factorization family is preferable for the workload and target.

This is the Computer Science / compiler-planning problem.

### Topological problem

Ask whether a rule

```text
A -> (R1(A), R2(A), ..., Rk(A))
```

can be chosen continuously and coherently as `A` varies through `SO(n)`.

This is where topology, cohomology, characteristic classes, bundle sections, and global obstructions naturally enter.

The important point is that ugly implementation cases — sign conventions, branch cuts, degenerate pivots, coordinate order, fallback paths — can sometimes be the computational shadow of a genuine global topological obstruction rather than merely evidence of poor software design.

## 2. The numerical-linear-algebra picture

There are several materially different ways to align a vector with a basis direction while preserving Euclidean norm.

### Givens rotations

A Givens rotation acts nontrivially in a single coordinate plane. In that plane it has the form

```text
[ c  s]
[-s  c]
```

with

```text
c^2 + s^2 = 1.
```

It is orthogonal and has determinant `+1`.

A chain of these can annihilate coordinates one at a time. This is a literal implementation of the intuitive phrase:

```text
rotate one coordinate/direction into place
freeze it
continue with the remaining dimensions
```

This decomposition is easy to inspect and gives good small conformance kernels, but in high dimension it can create real planning questions:

- which plane is selected next;
- whether zero or near-zero entries are skipped;
- whether ordering should be static or data-dependent;
- whether different vectors in a GPU batch take different paths;
- whether a plan can be generated once and replayed branchlessly;
- whether serial dependency depth becomes the main cost;
- whether a sparse problem makes local rotations attractive;
- whether dense arithmetic on a reflector would be cheaper.

### Householder reflection

A Householder reflector has the form

```text
H = I - 2 vv^T / (v^T v).
```

It is orthogonal and has determinant `-1`.

With a suitable `v`, one reflector can send a vector to a signed multiple of `e1`. Relative to a long Givens sequence, this can turn a large number of local choices into regular dense arithmetic.

That does not automatically make it faster. The answer depends on dimension, sparsity, batching, memory layout, precision, target architecture, branch/divergence cost, and whether the transform must later be reused.

### Two reflections give a proper rotation

A reflection has determinant `-1`, so a product of two reflections has determinant `+1`.

Therefore a semantic requirement for a proper transform does **not** force a Givens chain. For the underdetermined vector-alignment request in dimension at least two, a reflection-based construction can be paired with a second reflection fixing the target axis.

This does not say that an arbitrary prescribed element of `SO(n)` is a product of only two reflections. Its exact minimum can be larger.

This elementary determinant fact is exactly the bridge between “reflection” and “rotation” that keeps reappearing in the topology.

### Direct rotation in the data-defined 2-plane

If the only requirement is to move one nonzero direction to `e1`, and that direction is neither parallel nor antiparallel to `e1`, the necessary geometric action occurs in the 2-plane spanned by the two directions. The orthogonal complement can be left fixed.

The parallel case is the identity. In the antiparallel case the span collapses to one line, so a proper rotation in dimension at least two needs an additional chosen perpendicular direction. The zero vector has no direction; this study uses a tagged identity/no-op convention. These cases are specified in [`numerical-linear-algebra.md`](numerical-linear-algebra.md).

A direct-plane representation and a two-reflection representation can describe the same simple rotation. They are computational representations to compare, not automatically distinct mathematical families.

## 3. Hatcher's construction is unexpectedly close to the same idea

Allen Hatcher's Section 3D treatment of `SO(n)` is not merely abstractly about the same group. It literally builds `SO(n)` out of reflections.

For nonzero `v in R^n`, let `r(v)` denote reflection in the hyperplane perpendicular to `v`.

Hatcher considers

```text
rho(v) = r(v) r(e1).
```

Each reflection has determinant `-1`, so `rho(v)` has determinant `+1` and therefore lies in `SO(n)`.

Because `v` and `-v` determine the same reflecting hyperplane,

```text
r(v) = r(-v),
```

and therefore `rho(v)` depends only on the line through `v`.

That is why the natural parameter space is real projective space:

```text
RP^(n-1) -> SO(n).
```

Hatcher then multiplies these elementary rotations to obtain a cellular map

```text
RP^(n-1) x RP^(n-2) x ... x RP^1 -> SO(n),
```

and uses the resulting products as characteristic-map machinery for a CW decomposition of `SO(n)`.

This is a very concrete reason to keep projective geometry, reflections, and numerical factorizations in one study. The topology of the rotation group is being assembled using products of reflections.

## 4. The `SO(n-1) -> SO(n) -> S^(n-1)` tower

A second striking point is the standard evaluation map

```text
p : SO(n) -> S^(n-1)
p(A) = A en.
```

Hatcher defines `rho(v)` using `e1` but evaluates at `en`. This is consistent because `r(e1)` fixes `en`.

The fiber consists of rotations that fix `en`, which is naturally `SO(n-1)`. So we have the bundle

```text
SO(n-1) -> SO(n) -> S^(n-1).
```

This already encodes the idea:

```text
choose where one basis vector goes
then solve the remaining lower-dimensional rotation problem
```

Hatcher makes this explicit in the cell construction. If `beta` already fixes `en`, it lies in the `SO(n-1)` fiber. Otherwise, the cell parametrization supplies the relevant `v_beta`, and the elementary two-reflection rotation `rho(v_beta)` sends `en` to `beta en`. Then

```text
alpha_beta = rho(v_beta)^(-1) beta
```

fixes `en`, so

```text
alpha_beta in SO(n-1),
```

and

```text
beta = rho(v_beta) alpha_beta.
```

This is topologically very close to a one-direction-at-a-time numerical elimination:

```text
put one direction in place
freeze it
recurse on the orthogonal complement
```

The analogy is not superficial. The recursive reduction in dimension is built into the topology of the group.

## 5. What sphere parity does and does not say

Even- and odd-dimensional spheres are globally different in ways that can affect the existence of continuous choices.

A very simple invariant is Euler characteristic:

```text
chi(S^(2k))   = 2
chi(S^(2k+1)) = 0.
```

The nonzero Euler characteristic of an even-dimensional sphere rules out a nowhere-zero tangent field. Odd-dimensional spheres do admit at least one such field.

So when the rotation group is viewed through the tower

```text
SO(n-1) -> SO(n) -> S^(n-1),
```

it is reasonable to ask whether the topology of the sphere at each stage obstructs the exact continuous choice a planner wants.

But one nonvanishing tangent field is much weaker than a full frame. Vanishing Euler characteristic does not trivialize the bundle. The product/trivial cases occur at `n = 2, 4, 8`, corresponding to the parallelizable spheres `S^1`, `S^3`, and `S^7`; Hatcher describes the other cases as twisted products.

This does **not** mean:

```text
sphere parity -> choose Householder on odd n
```

or any other direct compiler heuristic.

It means the planner should distinguish local existence of a factorization from global continuity/coherence of a *rule for choosing* that factorization, and should record the exact dimension and theorem rather than only a parity bit.

## 6. The key distinction: individual factorization versus coherent family

For an individual orthogonal matrix, factorization results are abundant. By Cartan–Dieudonne, every orthogonal transformation is a product of reflections. The exact Euclidean minimum is `rank(I - Q) = codim Fix(Q)`, so even a proper matrix may need more than two reflections.

So the question

```text
Can I factor this particular A?
```

is usually easy in principle.

The deeper question is

```text
Can I choose such a factorization for every A
in a way that varies continuously with A?
```

That is a question about sections, trivializations, parametrizations, and global topology.

A global rule would amount to something like

```text
A -> (R1(A), R2(A), ..., Rk(A))
```

with the `Ri(A)` chosen from some preferred family of elementary transformations.

The `SO(n-1)` bundle constrains such a rule only when the proposed choice would actually induce a section or full frame of that bundle. A different factorization parameter space needs its own map and theorem; nontriviality cannot be transferred by analogy alone.

If the relevant bundle is nontrivial, no single global continuous choice may exist. Any practical implementation may then need one or more of:

- multiple coordinate charts;
- sign conventions;
- branch cuts;
- exceptional cases;
- discontinuous pivot/order choices;
- redundant parametrization;
- fallback factorizations.

That is exactly the kind of place where topology can explain why a seemingly avoidable complication keeps reappearing.

## 7. Why cohomology may matter

Cohomology is not a finite rotation scheduler. It is a global invariant that can detect and organize structure invisible to a purely local coordinate description.

Its plausible relevance to a rotation/reflection planner is therefore through questions such as:

- whether a globally continuous decomposition rule exists;
- whether a bundle admits a section;
- whether a frame can be chosen globally;
- whether two families of decompositions are topologically equivalent;
- whether unavoidable singularities or identifications exist;
- whether apparent sign ambiguities reflect genuine `Z/2` topology;
- whether a proposed global coordinate system on `SO(n)` is impossible;
- whether a planner is implicitly asking for more global regularity than the topology permits.

This is much stronger and more precise than saying merely that “`SO(n)` has holes.”

## 8. Why projective-space and `2`-torsion phenomena feel natural here

A reflecting hyperplane is determined by a normal line, not an oriented normal vector. Thus

```text
v ~ -v.
```

That quotient is exactly the projective-space relation.

Since Hatcher's cell construction uses

```text
RP^(n-1), RP^(n-2), ..., RP^1,
```

it is unsurprising that `Z/2` phenomena and `2`-torsion play a substantial role in the integral cohomology of `SO(n)`.

This is not a derivation of the entire cohomology ring, but it is a useful conceptual bridge:

```text
reflection choice
  -> unoriented normal line
  -> real projective space
  -> pervasive mod-2 structure
```

## 9. Hatcher / Agosto / Perez computations

Allen Hatcher maintains a page titled **The Cohomology of `SO(n)`** with computer-generated diagrams for `SO(5)` through `SO(12)`.

The source credits:

- **M. A. Agosto and J. J. Perez** for the computer-generated pictures / Mathematica computation;
- **Allen Hatcher** for the commentary.

The diagrams encode Bockstein information used to understand the integral cohomology from the mod-2 picture.

The useful compiler-design interpretation is modest:

- these calculations demonstrate that substantial global structure of `SO(n)` is explicitly computable;
- they do **not** by themselves tell us that cohomology should be fed into a shader compiler;
- the next scientific question is whether any of this structure eliminates or simplifies a real planning decision.

A good criterion is:

```text
Does a mathematical invariant let us remove a runtime choice,
rule out an algorithm family,
precompute a reusable plan,
or explain an unavoidable case split?
```

If not, it remains mathematically interesting background rather than compiler input.

## 10. What the Computer Science planner should preserve

The high-level program should express the semantic operation rather than prematurely name the implementation. It must also state the operation's domain and degenerate behavior.

For example:

```text
align this distinguished direction with e1
preserve norm
require / do not require determinant +1
apply / do not apply the same transform to accompanying values
if the vector is zero, return zero with an identity/no-op transform and a zero-direction status
if it is antiparallel to e1, record how the additional rotation-plane direction is chosen
```

The planner should then ask, in order:

1. What mathematics constrains the operation?
2. Which algorithm families are correct?
3. What workload facts distinguish them?
4. Which facts are static, symbolic, profile-derived, or runtime-only?
5. Can a plan be computed once and replayed?
6. Only then, what branch/vector/ISA/shader lowering is appropriate?

This separates semantics from algorithm choice and algorithm choice from machine lowering.

## 11. Candidate facts that may matter to algorithm selection

Do not assume values for these in advance. Measure or prove which are predictive.

- dimension;
- sparsity/density and other structure;
- whether a transform is reused across many values;
- whether the full transform must be retained;
- whether only the aligned result matters;
- determinant requirement;
- tolerance / numerical error budget;
- Float16 / Float32 / other precision actually available;
- batch size;
- memory layout and residency;
- CPU versus GPU target;
- branch predictability;
- GPU lane divergence;
- dependency depth;
- SIMD/subgroup/vector suitability;
- register pressure;
- spills;
- load/store traffic;
- code size;
- compile time;
- ability to precompute a branchless replay plan;
- whether the selection logic itself costs more than it saves.

## 12. Relationship to existing Computer Science issues

This study consolidates and extends several standing notes.

### #51 — Givens conformance kernel

Keep one small actual Givens rotation as a cross-target correctness kernel. Do not confuse that with choosing a long Givens chain as the default high-dimensional algorithm.

### #53 — evidence-based high-dimensional transform choice

Keep Householder, products of reflections, Givens, direct-plane rotations, and later structured algorithms live until the workload and target justify a choice.

### #54 — ask mathematics before registers

Insert a symbolic/mathematical planning stage before committing to branches, vectors, instructions, registers, or shader fragments.

This rotations/reflections study is a concrete test case for that principle.

## 13. Macaulay2 and cohomology software

Macaulay2 is relevant as mature computer-algebra infrastructure for algebraic geometry, commutative algebra, homological algebra, Ext/Tor, and related cohomology computations.

It is useful here mainly as prior art for the discipline:

```text
before implementing symbolic mathematics ourselves,
ask what mature systems can already compute exactly.
```

It should not be falsely credited with Hatcher's `SO(n)` pictures. Hatcher credits Agosto and Perez with a **Mathematica** program for those computations.

Debian also packages **cohomCalg**. Its domain is sheaf cohomology of line bundles / toric divisors on toric varieties. It is not a generic singular-cohomology engine for `SO(n)`.

The useful architectural example is that Macaulay2 can act as an interface to a specialized cohomology engine. That is an interesting model for a planner consuming symbolic answers with explicit provenance.

## 14. Sources and redistribution boundary

[`software-and-sources.md`](software-and-sources.md) is the canonical bibliography, attribution, and reuse note. In summary, this repository links to the Hatcher and Agosto/Perez material, credits its creators, and does not store their PDFs or figures. Keeping the detailed boundary in one file avoids contradictory licensing summaries.

## 15. Focused references

- Pointwise alignment semantics and numerical choices: [`numerical-linear-algebra.md`](numerical-linear-algebra.md)
- Topology and exact dimension scope: [`topology-and-cohomology.md`](topology-and-cohomology.md)
- Exact and derived planner facts: [`cheap-invariants-and-parity.md`](cheap-invariants-and-parity.md)
- Dependency and parallel-execution distinctions: [`parallelism-from-factorizations.md`](parallelism-from-factorizations.md)
- Full bibliography and source boundary: [`software-and-sources.md`](software-and-sources.md)

## 16. The working research question

The most useful compact statement of the whole thread is:

> Cohomology of `SO(n)` does not directly encode “perform rotation 7, then rotation 3.” Cohomological and obstruction-theoretic data can, however, help detect whether families of reflection/rotation decompositions can be chosen continuously or globally. Such obstructions may show up computationally as unavoidable sign choices, singular cases, branch cuts, or multiple planning charts.

That is enough to justify keeping the topology next to the numerical-linear-algebra planner without pretending that every cohomology class has an immediate machine-code interpretation.

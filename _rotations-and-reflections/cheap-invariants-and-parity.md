# Exact local facts, derived invariants, and family constraints

This is the canonical note for planner metadata. Its first rule is to keep three scopes separate:

1. **request facts** describe what transform is allowed to be chosen;
2. **transform facts** describe an already selected or prescribed `Q`;
3. **family facts** describe choices varying over a parameter space.

Mixing these scopes creates false deductions. A proper alignment request may admit a two-reflection solution even though a prescribed proper matrix can require many reflections, and a topological obstruction to one continuous family says nothing against factoring one matrix pointwise.

## 1. Request facts and observed input facts

For the alignment operation, record semantic constraints rather than an algorithm name:

```text
AlignmentRequest:
    dimension: n
    orientation_requirement: any | proper
    carry_transform_to_other_values: yes | no
    continuity_requirement: pointwise | local_family | global_family

AlignmentInputFacts:
    input_case:
        exact(zero | parallel | antiparallel | generic, provenance)
        | classified_within_tolerance(case, tolerance_policy, provenance)
        | unknown
    known_at: planning_time | runtime
```

The request describes required semantics; the input record describes the value being processed. Keeping them separate prevents a runtime observation such as `zero` from being mistaken for a property of every invocation of the operation. A floating-point “near zero” or “nearly parallel” classification is not silently promoted to an exact mathematical fact; its tolerance policy travels with it. The zero and antiparallel cases have explicit semantics in [`numerical-linear-algebra.md`](numerical-linear-algebra.md).

Neither record claims that a particular `Q` already exists or has been selected.

## 2. Canonical transform facts and derived views

For an orthogonal transform `Q`, one canonical partial record is enough:

```text
KnownTransformFacts:
    dimension: n
    determinant_sign:
        exact(+1 | -1, provenance) | unknown
    fixed_space_codimension:
        exact(value, provenance) | unknown
```

The following are **derived**, not independent stored fields:

```text
dimension_parity = n mod 2
orientation = proper iff the known determinant sign is +1
orientation = improper iff the known determinant sign is -1
minimal_reflection_count = the known fixed-space codimension
reflection_count_parity = even iff the known determinant sign is +1
reflection_count_parity = odd  iff the known determinant sign is -1
fixed_space_dimension = n - the known fixed-space codimension
```

Storing all of these independently would permit impossible states such as `proper` together with determinant `-1`. A cached derived value may be used for performance, but it must be checked against its canonical source rather than treated as another unconstrained fact.

The two canonical observations also overlap and must be reconciled when both are known:

```text
0 <= fixed_space_codimension <= n
determinant_sign = (-1)^fixed_space_codimension
```

Either observation may be available when the other is not, which is why both slots are useful in a partial record. If both are present and violate this relation, their provenance must be investigated rather than selecting whichever value is convenient. A tolerance-dependent numerical rank is an estimate, not an `exact` fixed-space codimension, unless it has been certified under a stated policy.

Provenance belongs on each nontrivial fact. One record-wide provenance field cannot describe a determinant certified from a chosen construction, a fixed space computed symbolically, and a cost learned by measurement.

## 3. Orientation and reflection parity

For an orthogonal transformation,

```text
det(Q) in {+1, -1}.
```

A hyperplane reflection has determinant `-1`. Therefore every pure-reflection factorization

```text
Q = R_k ... R_2 R_1
```

satisfies

```text
det(Q) = (-1)^k.
```

Thus every reflection factorization of a proper transform has even length, and every reflection factorization of an improper transform has odd length. This is an exact pruning rule.

It does **not** say that every proper transform uses exactly two reflections. It says only that its reflection count is even.

## 4. Exact reflection length

For a prescribed orthogonal map on Euclidean `R^n`, the minimal number of hyperplane reflections is

```text
rank(I - Q) = codim Fix(Q).
```

The equality can be checked directly rather than left as an unsupported strengthening of Cartan-Dieudonne.

### Lower bound

For linear maps `A` and `B`,

```text
I - AB = (I - A) + A(I - B),
```

so

```text
rank(I - AB) <= rank(I - A) + rank(I - B).
```

For a hyperplane reflection `R`, `rank(I - R) = 1`. Therefore, if `Q` is a product of `k` reflections,

```text
rank(I - Q) <= k.
```

### Matching upper bound

Let `F = Fix(Q)`. If `Q` is not the identity, choose `x` outside `F` and set

```text
v = Qx - x.
```

Because `Q` is orthogonal, reflection in the hyperplane perpendicular to `v` sends `Qx` to `x`. Also `v` is perpendicular to every vector in `F`, so this reflection fixes `F` pointwise. Left-composing by it therefore increases the fixed-space dimension by at least one.

Repeating reaches the identity after at most `codim F` reflections. Combined with the lower bound, the minimum is exactly

```text
codim F = rank(I - Q).
```

Since `codim F <= n`, the constructive upper bound also recovers the Cartan-Dieudonne statement that at most `n` hyperplane reflections are needed.

Do not compute a costly numerical rank merely to rediscover information already available from symbolic structure. The formula becomes useful metadata only when the fixed-space codimension is known reliably and cheaply enough.

## 5. Dimension parity is derived but not yet a selector

Hatcher's description of integral cohomology **modulo torsion** has different forms for

```text
n = 2k + 1
```

and

```text
n = 2k + 2.
```

This makes dimension parity relevant to theorem dispatch. It does not supply a lowering rule such as “odd `n`, start with a reflection.”

Likewise, the Euler characteristic distinguishes even- and odd-dimensional spheres, but vanishing Euler characteristic is not sufficient for a full global frame. The actual product exceptions in the bundle `SO(n-1) -> SO(n) -> S^(n-1)` occur at `n = 2, 4, 8`, not at every value of one parity.

Since `n mod 2` is derived trivially from `n`, it need not occupy an independent field unless a concrete consumer benefits from caching it.

## 6. Family constraints belong in a separate record

A warning about global charts is not a property of one matrix `Q`. It needs its own scope:

```text
FamilyConstraint:
    parameter_domain
    candidate_transform_family
    continuity_requirement
    statement
    theorem_scope
    source
    verification_status
```

For example, Hatcher's twisted-product statement is dimension-scoped. It must not be stored as a timeless Boolean `global_chart_warning` detached from `n`, the bundle, and the required kind of section.

## 7. Deterministic pruning before advisory synthesis

From exact request and transform facts, a deterministic pass can make conclusions such as:

```text
proper alignment requested
    -> reject a single reflection as the final transform

prescribed Q with fixed_space_codimension = 6
    -> reject every factorization with fewer than 6 reflections
    -> require even reflection parity

input_case resolves to zero
    -> use the identity/no-op convention
    -> do not normalize

dimension = 1 and input_case resolves to antiparallel and proper requested
    -> report the request unsatisfiable
```

An LLM may then propose which surviving representations deserve comparison. It must not turn an unverified topological analogy or heuristic into a hard rejection.

## References

- Cartan-Dieudonne theorem: every orthogonal transformation in dimension `n` is a product of at most `n` hyperplane reflections.
- Hatcher's `SO(n)` summary describes `H*(SO(n); Z)` modulo torsion separately for `n = 2k+1` and `n = 2k+2`: https://pi.math.cornell.edu/~hatcher/SO/comments.pdf
- The determinant identity `det(R_i) = -1` fixes reflection-count parity.
- The proof above establishes the sharper Euclidean reflection-length formula used by this note.

# Cheap invariants and parity for local planning

There are several very cheap facts a local optimizer can know before choosing a concrete rotation/reflection algorithm. They should be kept distinct because they answer different questions.

## 1. Ambient dimension parity: `n mod 2`

The parity of the dimension is essentially free metadata.

```text
n_even = (n & 1) == 0
```

This can be known statically whenever the dimension is part of the type/shape, or cached once when dimension is dynamic but reused.

The distinction is mathematically real. Hatcher's summary of the torsion-free integral cohomology has different forms for

```text
n = 2k + 1
```

and

```text
n = 2k + 2.
```

That is enough to justify keeping an `even_n / odd_n` discriminator available to an advisory/planning layer whenever a theorem, decomposition, orientation argument, or target specialization actually has different cases.

But **dimension parity alone does not justify a rule such as “odd n => start with a reflection.”** Any such lowering rule needs an additional theorem or measured algorithmic argument.

## 2. Orientation parity: determinant `+1` versus `-1`

For an orthogonal transformation `Q`,

```text
det(Q) in {+1, -1}.
```

This is a much stronger immediate algorithm-pruning fact than ambient dimension parity.

```text
det(Q) = +1  =>  Q in SO(n)   (proper/orientation-preserving)
det(Q) = -1  =>  Q in O(n) \ SO(n)  (improper/orientation-reversing)
```

A hyperplane reflection has determinant `-1`. Therefore any reflection factorization

```text
Q = R_k ... R_2 R_1
```

satisfies

```text
det(Q) = (-1)^k.
```

So the parity of the number of reflections is fixed immediately by orientation:

```text
Q in SO(n)              => every reflection factorization has even length parity
Q in O(n) \ SO(n)       => every reflection factorization has odd length parity
```

This gives a genuine tiny case switch that every planner/micro-optimizer can exploit:

```text
if proper_rotation_required:
    reject a single reflection as the final transform
    retain two-reflection / Givens / direct proper-rotation families
else if orientation_reversing_required:
    require odd reflection parity in a pure-reflection factorization
```

For a transformation already known to be a **rotation in `SO(n)`**, the reflection parity is not an extra classifier: it is always even.

## 3. Minimal reflection count can contain more information than parity

Cartan-Dieudonne says that every orthogonal transformation in `n` dimensions is a product of at most `n` hyperplane reflections.

In Euclidean space the sharper reflection-length statement is tied to the fixed subspace. The minimal number of hyperplane reflections required for an orthogonal map `Q` is

```text
rank(I - Q)
```

which is the codimension of the fixed-point subspace `ker(Q - I)`.

This is potentially useful planning metadata when `Q` is already materialized or its fixed-subspace structure is known symbolically:

```text
reflection_length_lower_and_exact = rank(I - Q)
reflection_parity = reflection_length mod 2 = orientation parity
```

Do not compute a costly matrix rank merely to rediscover a fact that is already known more cheaply. The planner should use the strongest cheap invariant already available from semantics, symbolic structure, or prior computation.

## 4. The local optimizer should receive facts, not rediscover mathematics

A useful cached planning record could contain:

```text
RotationFacts:
    dimension: n
    dimension_parity: even | odd
    orientation: proper | improper | unknown
    determinant_sign: +1 | -1 | unknown
    fixed_subspace_dimension: known integer | unknown
    minimal_reflection_count: known integer | unknown
    reflection_count_parity: even | odd | unknown
    global_chart_warning: none | local-only | unknown
    provenance: theorem / symbolic result / semantic requirement / measurement
```

Then every small optimizer can make immediate local choices without rerunning topology, group theory, or symbolic algebra.

The expensive/nonlocal reasoning belongs upstream. The cheap consequences should be cached and propagated downward.

## 5. What an LLM advisory pass can do with this

The LLM does not need to derive all of topology at instruction-selection time. It can consume a compact fact set such as

```text
n is even
Q is proper
global one-chart factorization is not assumed
reflection length <= n
batch reuses Q
GPU penalizes divergent per-vector ordering
```

and propose a shortlist:

```text
- compare two-reflection/direct-plane plan
- compare regular Householder-derived proper plan
- keep Givens as conformance/reference but do not assume a long adaptive chain
- prefer a reusable factor plan over per-vector rediscovery
```

A deterministic checker can then verify the hard claims and the backend can measure the proposed implementations.

## 6. Cheap case switches are useful even when the final theory is unfinished

This is the main architectural point.

We do **not** need a complete formal theory of every global constraint before using information that is already trivially classifiable.

The planner can begin with a hierarchy:

```text
cheap exact facts
    dimension parity
    determinant/orientation
    known shape
    known fixed directions

symbolic/global facts
    bundle/trivialization warnings
    invariant subspaces
    commuting factors
    known factorization bounds

heuristic/advisory synthesis
    LLM proposes candidate plans

checked lowering
    verify hard constraints
    benchmark target-specific candidates
```

That gives the local optimizer immediate usable cases while leaving room for more sophisticated global information to be added later.

## References

- Cartan-Dieudonne theorem: every orthogonal transformation in dimension `n` is a product of at most `n` reflections.
- Hatcher's `SO(n)` cohomology summary distinguishes the `n = 2k+1` and `n = 2k+2` torsion-free cases.
- The determinant identity `det(R_i) = -1` makes reflection-count parity an exact orientation invariant.

# Parallelism exposed by reflection factorizations

A Cartan-Dieudonne factorization is useful to the planner for more than existence.

If

```text
Q = R_k ... R_2 R_1
```

with each `R_i` a reflection, then the factorization exposes structure that can be analyzed for parallel execution.

## Important qualification

For one vector,

```text
Q x = R_k(...R_2(R_1 x)...)
```

is in general a dependency chain. Arbitrary reflections cannot simply be applied simultaneously and then combined as though they were independent.

So the theorem does **not** imply:

```text
k reflections -> k fully independent jobs
```

What it does imply is that the planner has explicit factors whose dependencies can be inspected.

## Places parallelism can appear

### 1. Inside one reflection

A Householder/reflection application

```text
H x = x - 2 v (v^T x) / (v^T v)
```

contains operations with obvious parallel structure:

- compute `v^T x` by a parallel reduction;
- scale/update coordinates independently once the scalar is known;
- reuse `v^T v` when the reflector is fixed.

So even a sequential chain of reflectors contains substantial data-level parallelism.

### 2. Across many vectors

If the same factorization is applied to a batch

```text
x_1, x_2, ..., x_m,
```

then each vector can traverse the same reflector sequence independently.

This is especially important for GPU planning: sequential factor depth for one vector may coexist with very high batch parallelism.

### 3. Commuting factors

If two factors commute,

```text
R_i R_j = R_j R_i,
```

then the planner gains freedom to reorder them. That can permit grouping into parallel layers or selecting an order better suited to memory/register constraints.

Commutation should be proved from the actual factors or their subspaces, not assumed merely because both are reflections.

### 4. Disjoint / orthogonal subspace actions

If factors act on independent orthogonal subspaces, the corresponding pieces can often be processed independently.

A planner should therefore try to identify block structure such as

```text
V = V_1 orthogonal_sum V_2 orthogonal_sum ...
```

where different factors are confined to different blocks.

That is a direct example of non-local mathematical information creating a concrete execution opportunity.

### 5. Tree composition when the full transform is needed

If the task requires materializing the full matrix `Q` rather than merely applying factors to one vector, products of already-known factors can be associated as a tree:

```text
(R_8 R_7) (R_6 R_5) (R_4 R_3) (R_2 R_1)
```

and partial products can be formed in parallel.

This trades extra matrix work/storage against shorter dependency depth, so it should be chosen from evidence rather than assumed preferable.

### 6. Parallel factor discovery when the mathematics permits it

Some decomposition algorithms produce later factors from the result of earlier ones, making factor discovery itself sequential.

But if a symbolic/group-theoretic result already gives independent blocks, commuting generators, or a known normal form, factor generation for independent pieces can also be parallelized.

This is precisely where a mathematical advisory layer can expose parallelism before low-level code generation begins.

## Planner consequence

A factorization should be represented as more than an ordered list.

Prefer something closer to a dependency graph:

```text
factor
subspace acted on
commutes_with
requires_after
reusable_across_batch
internal reduction shape
precision requirement
cost evidence by target
```

Then the planner can derive execution layers rather than blindly replay the textual order in which a theorem or CAS happened to present the factors.

## Connection to the LLM advisory layer

This is a strong example of why heterogeneous mathematical information is useful before a specific numerical algorithm is fixed.

An LLM advisor can notice:

```text
Cartan-Dieudonne factorization available
+ some factors act on independent subspaces
+ batch reuses the same Q
+ target favors wide regular work
```

and propose:

- parallelize the batch;
- layer commuting/disjoint factors;
- exploit parallel reductions inside each reflection;
- precompute reusable reflector scalars;
- compare sequential application with partial matrix composition if dependency depth dominates.

Those proposals are then checked deterministically against the factor metadata and measured on the target.

## Research question

The useful compiler question is no longer only

```text
How many reflections are required?
```

but also

```text
What dependency width/depth does this particular factorization expose?
```

Two mathematically equivalent factorizations can therefore have different computational value even when they use the same number of reflections.

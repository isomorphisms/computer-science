# Signed branch conditions as a partition of inequality

After a normal signed integer comparison such as

```asm
cmp r0, r1
```

the three mathematical cases are exactly

```text
r0 < r1
r0 = r1
r0 > r1
```

and the corresponding Arm conditions are

```text
BLT   signed less than
BEQ   equal
BGT   signed greater than
```

For values compared this way,

```text
BNE = BLT ∪ BGT
```

with `BLT` and `BGT` disjoint. Each is a proper subset of `BNE`.

Equivalently, for a totally ordered domain,

```text
x ≠ y  iff  x < y or x > y
```

This is a small but real mathematical assumption in the instruction story: it uses a total order. It does not transfer unchanged to domains such as the complex numbers, where there is no canonical order compatible with the usual field structure, so `z ≠ w` does not naturally split into `z < w` or `z > w`.

## Condition-code caveat

The statement above is about flags produced by an actual signed `CMP`, not about arbitrary `NZCV` bit patterns considered in isolation.

Arm defines the signed conditions using the flags roughly as

```text
EQ: Z = 1
NE: Z = 0
LT: N ≠ V
GT: Z = 0 and N = V
```

For a genuine comparison, the arithmetic constrains those flags so that exactly one of `<`, `=`, or `>` holds. If one treats `N`, `Z`, `C`, and `V` as arbitrary independent bits instead, `LT ⊂ NE` is not a valid set-theoretic statement.

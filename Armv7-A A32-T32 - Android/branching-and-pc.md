# Branching in Armv7-A / Thumb: `r15` is the Program Counter

The simplest useful mental model is:

> `r15` is the architectural **Program Counter (`PC`)**. Normal execution continues to the next instruction. A branch changes the next value of `PC`, so execution continues somewhere else.

That is the core idea. `PC` here means **Program Counter**, not process control.

```text
ordinary instruction
    PC -> next instruction

B label
    PC -> label

BEQ label
    if condition is true:  PC -> label
    otherwise:             PC -> next instruction

BL function
    r14 (LR) <- return address
    PC       -> function

BX r3
    PC -> address supplied by r3

BX lr
    PC -> return address in r14 (LR)
```

For T32/Thumb-2, ordinary fall-through may advance by **2 bytes or 4 bytes** because Thumb mixes 16-bit and 32-bit instruction encodings. Branch instructions use T32 encodings as well.

Conditional branches such as `BEQ` normally decide whether to replace the next `PC` using condition flags such as `Z`. Thumb also has instructions such as `CBZ`/`CBNZ` that branch from a register test directly.

## One caveat after the simple picture is understood

Do not over-literalize `r15` as a physical little counter circuit inside the core. A modern Arm core has instruction fetch, pipelines, branch prediction, queues, and other internal state, and architecturally reading `r15` has special A32/T32 semantics. But at the instruction-set level the useful model is still:

**branching = deciding what address becomes the next program counter.**

For compiler work, that is the primitive to keep in mind before adding labels, conditions, control-flow graphs, SSA blocks, or higher-level `if` expressions.

## The small family of control-flow operations

At the machine-code level, a surprisingly large amount of source-language control flow reduces to a few forms:

- **fall through**: use the next sequential instruction;
- **direct branch**: jump to a target encoded relative to the current code location, such as `B label`;
- **conditional branch**: choose fall-through or a target from condition state, such as `BEQ label`;
- **register/indirect branch**: take the destination from a register, such as `BX r3`;
- **call**: branch while preserving a return address, such as `BL function`;
- **return**: indirect branch back to the saved return address, commonly `BX lr`.

`if`, `while`, `for`, function calls, early returns, and many `switch` statements are arrangements of those operations rather than separate fundamental pieces of hardware.

There is no special architectural "loop register" corresponding to a source-language `for` loop. A normal counted loop is arithmetic or comparison plus a backward branch.

## Dense `switch`: jump tables and Thumb's `TBB` / `TBH`

A large dense switch does not require one register per case. The case destinations can live in a table in code/data memory.

Conceptually:

```text
index <- input - first_case
if index is out of range:
    branch default
else:
    target <- table[index]
    branch target
```

A compiler may instead use a linear chain of comparisons, a binary decision tree, bit/range tricks, or some combination. The number of cases is therefore not limited by the number of registers.

Thumb-2 has unusually direct support for compact jump tables:

```asm
tbb [pc, r0]          ; table entries are bytes
tbh [pc, r0, lsl #1] ; table entries are halfwords
```

`TBB` means **Table Branch Byte** and `TBH` means **Table Branch Halfword**. They read a small table entry, scale it as required by the instruction, and derive the branch destination relative to the program counter. They are a concrete example of a multi-way source-level `switch` becoming a computed change of the next instruction address.

The exact compiler sequence still needs a range check unless all possible selector values are valid table entries.

Reference: Arm's explanation of Thumb-2 branch and call sequences, including `TBB` and `TBH`: <https://developer.arm.com/community/arm-community-blogs/b/architectures-and-processors-blog/posts/branch-and-call-sequences-explained>.

## ASCII case as a useful anti-switch example

ASCII uppercase and lowercase Latin letters differ by hexadecimal `0x20`:

```text
'A' = 0x41 = 0100 0001
'a' = 0x61 = 0110 0001
                 ^
               0x20
```

So, after checking that a byte is actually an ASCII letter:

```text
letter XOR 0x20
```

toggles uppercase/lowercase. Equivalently, setting that bit makes an ASCII letter lowercase and clearing it makes an ASCII letter uppercase.

This is **bit 5 when bits are numbered from 0 at the least-significant bit**. It is the sixth bit from the right.

Do not apply the transformation blindly to arbitrary ASCII bytes: many non-letters also differ from other characters by `0x20`.

This makes the `A`-`Z` / `a`-`z` exercise useful in two different ways:

1. as a deliberate 52-case branch/jump-table test for a compiler backend;
2. as an optimization example showing that the mathematical structure of the data can eliminate almost all of those cases.

## Labels are not stored magical destinations

Assembly labels are names for addresses. After assembly/linking, a direct branch contains an encoded displacement or related target information; an indirect branch obtains the target from a register/table computation.

That distinction is worth keeping visible in the compiler backend:

```text
source label
    -> compiler/assembler symbol
    -> relocation or encoded displacement
    -> next PC value at execution
```

The label is for humans and tools. The processor ultimately needs enough bits to determine where instruction fetch continues.

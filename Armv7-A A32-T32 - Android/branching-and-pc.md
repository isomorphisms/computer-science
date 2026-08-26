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

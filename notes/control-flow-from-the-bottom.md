# Control flow from the bottom: instructions, opcodes, jumps, branches, loops, and switches

This note keeps several layers separate because programming terminology often hides the simple machine-level mechanism underneath it.

## Instruction, mnemonic, opcode

A **machine instruction** is an encoded unit of work the processor or virtual machine knows how to execute.

An **opcode** is the operation-code part of an instruction encoding: the bits that identify what operation is requested. Other bits may identify registers, immediates, addressing modes, widths, conditions, or other operands.

An assembly **mnemonic** is the human-readable name for an instruction or instruction family:

```text
human mnemonic       machine encoding contains
ADD                   opcode + operands
B                     branch opcode + target information
BX                    branch/exchange opcode + register operand
```

People sometimes use "opcode" loosely for the whole encoded instruction, but the more precise distinction is useful when reading an ISA manual or writing an assembler/compiler.

An opcode is not a place to jump to. It says what operation the current instruction performs. A branch instruction's opcode says, roughly, "change control flow"; its other encoded information or operands determine the destination.

## The simplest CPU control-flow picture

A CPU needs to know which instruction to execute next.

At the architectural level this is represented by a **program counter** or instruction pointer. Ordinary execution chooses the next sequential instruction. Control flow changes that choice.

```text
fall through       next <- sequential instruction
unconditional jump next <- target
conditional branch next <- condition ? target : sequential instruction
indirect branch    next <- address computed or loaded at run time
call               save return address; next <- function
return             next <- saved return address
```

This is the primitive underneath a great deal of syntax.

## `if`, `for`, `while`, and `switch` are patterns built from lower pieces

A source-language `if` is usually some combination of:

```text
compute/test condition
branch around one path
execute selected path
possibly join again
```

A counted loop is usually:

```text
initialize counter
loop_body:
    do work
    update counter
    test stopping condition
    branch back to loop_body if continuing
```

There does not need to be a hardware object called a `for` loop.

A `switch` is a request for multi-way selection. A compiler chooses an implementation according to the case values, target ISA, code-size goals, profiling information, and optimization level.

Common implementations include:

- a short linear chain of comparisons and branches;
- a binary decision tree;
- a jump table;
- range tests plus arithmetic or bit tricks;
- a data lookup when the "cases" are really values rather than distinct pieces of code;
- mixtures of the above.

The number of switch cases is **not** limited by the number of CPU registers. A jump table with hundreds or thousands of entries can live in ordinary code/data memory.

## Direct versus computed destinations

A useful dividing line is whether the destination is already fixed in the instruction stream.

### Direct branch

```text
B somewhere
```

The assembler/linker resolves `somewhere` to target information that can be encoded or relocated.

### Indirect/computed branch

```text
target <- table[index]
branch target
```

Now the destination is data computed or loaded while the program runs.

This is what makes jump tables, virtual-machine dispatch loops, threaded interpreters, function pointers, callbacks, and similar patterns possible.

A computed branch is therefore not a mysterious new kind of high-level construct. It is just "the next instruction address comes from data."

## Jump tables

A dense switch such as cases `0` through `99` can be represented by a table of destinations.

```text
if x > 99:
    branch default
else:
    target <- table[x]
    branch target
```

The lookup is often approximately constant-time with respect to the number of cases, at the cost of table space and an indirect branch.

Sparse cases such as `1, 1000, 9000000` usually make a full table wasteful, so a compiler may prefer comparisons or a hybrid representation.

On Thumb-2, `TBB` and `TBH` are concrete ISA support for compact table branching. See [`Armv7-A A32-T32 - Android/branching-and-pc.md`](../Armv7-A%20A32-T32%20-%20Android/branching-and-pc.md).

## ASCII case: 52 named cases can collapse to one bit operation

ASCII makes a good example of why source-level case count and machine-level work can be very different.

```text
'A' = 0x41
'a' = 0x61
0x61 XOR 0x41 = 0x20
```

For the Latin letters, uppercase and lowercase differ by `0x20`, which is bit 5 when the least-significant bit is numbered bit 0.

After verifying that the byte is a letter:

```text
x XOR 0x20
```

toggles case.

So an intentionally annoying 52-case `A`-`Z` / `a`-`z` switch is a good jump-table/compiler exercise, but a good optimizer can recognize that the actual transformation has much simpler structure.

This is a useful general lesson:

> A large syntactic case split does not imply that the best machine implementation contains the same large case split.

## Odd control-flow patterns are possible because `if` and `for` are not the primitives

Once control flow is understood as choosing the next instruction address, many patterns that look strange from a structured-language point of view become ordinary:

- computed `goto`;
- jump tables;
- interpreter dispatch tables;
- threaded interpreters where table entries lead directly to handlers;
- state machines whose state chooses the next block;
- tail calls that reuse a caller's return path;
- continuation/state-machine transformations;
- predication or value selection that removes a branch entirely.

Structured `if`, `switch`, `for`, and `while` are extremely useful conventions. They are not the complete set of possible control-flow graphs.

## The abstraction ladder

It is useful to keep all of these layers visible at once:

```text
source language
    if / switch / for / function call

compiler IR
    comparisons / basic blocks / edges / phi nodes / structured merges

assembly
    CMP / B / BEQ / BL / BX / TBB / ...

machine encoding
    opcode fields + register/immediate/target fields

architectural effect
    arithmetic/status changes and selection of the next instruction address

microarchitecture
    fetch / decode / prediction / pipelines / queues / execution units
```

The bottom layer is more complicated electrically than the architectural model, but the architectural model is the right level for understanding and building a compiler backend.

## CPU and GPU: same control-flow idea, different execution model

A CPU example such as Arm Thumb can expose a conventional architectural program counter (`r15` in AArch32).

Portable GPU shader programming should **not** be described as though every shader invocation simply owns an Arm-like `r15`. Many shader invocations execute in parallel, and hardware may execute groups of invocations together with masks, predication, reconvergence, or vendor-specific branch machinery.

At a portable GPU IR level, however, the same graph ideas reappear: basic blocks, unconditional branches, conditional branches, switches, loop merges, and selection merges. SPIR-V exposes these directly through operations such as `OpBranch`, `OpBranchConditional`, `OpSwitch`, `OpLoopMerge`, and `OpSelectionMerge`.

See [`gpu-shader-code/control-flow.md`](../gpu-shader-code/control-flow.md) for the GPU-specific distinction.

Reference: Khronos SPIR-V unified specification, control-flow graph and control-flow instructions: <https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html>.

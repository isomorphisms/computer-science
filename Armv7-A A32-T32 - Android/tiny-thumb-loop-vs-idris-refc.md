# Tiny Thumb loop versus Idris 2 RefC

Date: 2026-08-26

## Question

How much does a tiny native Thumb program grow when it does a lot more work, and what should be compared against Idris 2 RefC?

The motivating fixture is deliberately simple: write `X`, then backspace, 100,000 times.

## Exact Thumb size experiment

These are host-side cross-build measurements, not yet a phone execution measurement.

Toolchain used for the measurement:

- Clang 17 targeting `armv7-linux-gnueabihf`
- LLD with one loadable ELF segment
- no libc, no Idris runtime, no dynamic linker
- section table stripped after linking
- Linux/Arm EABI syscall interface (`write = 4`, `exit = 1`)

| Fixture | loaded code+data | minimal ELF file | writes | output bytes |
| --- | ---: | ---: | ---: | ---: |
| write one `X` | 17 bytes | 101 bytes | 1 | 1 |
| write `X\b` 100,000 times | 30 bytes | 114 bytes | 100,000 | 200,000 |

So the 100,000-iteration fixture adds only **13 bytes to the executable file** relative to the one-character fixture.

That is the important structural fact: a loop stores the *rule for repetition*, not 200,000 copies of the output bytes.

The 114-byte ELF consists of:

- 52-byte ELF header
- one 32-byte program header
- 30 bytes of Thumb instructions plus the two literal output bytes

### Thumb source

```asm
.syntax unified
.arch armv7-a
.thumb
.text
.global _start
.type _start, %function
.thumb_func
_start:
    movw    r4, #0x86a0      @ low 16 bits of 100000
    movt    r4, #0x0001      @ high 16 bits of 100000
    adr     r1, pair
    movs    r2, #2
    movs    r7, #4           @ Linux/Arm EABI write

loop:
    movs    r0, #1           @ stdout; write overwrites r0 with return value
    svc     #0
    subs    r4, r4, #1
    bne     loop

    movs    r0, #0
    movs    r7, #1           @ Linux/Arm EABI exit
    svc     #0

    .balign 2
pair:
    .ascii "X\b"
.size _start, .-_start
```

The loop body itself is only four 16-bit Thumb instructions: reload stdout into `r0`, trap into the kernel, decrement the counter, branch if nonzero.

`\b` moves the terminal cursor left; it does not erase the previous `X`. The next `X` overwrites the same terminal cell.

## Speed: this particular fixture intentionally mixes two costs

The 100,000-iteration Thumb program makes **100,000 kernel write syscalls**. That is useful as a stress fixture, but it is not automatically a fair speed comparison with RefC.

Idris 2's Prelude maps `putChar` to C `putchar`. A libc implementation can buffer many `putchar` calls and issue far fewer kernel writes. Therefore a direct-syscall Thumb loop can actually lose a wall-clock output benchmark even though its language/runtime overhead is far smaller.

For backend comparison, measure at least two destinations separately:

1. stdout redirected to `/dev/null` or a pipe, to reduce terminal rendering effects;
2. an actual terminal, because terminal/PTY/rendering work can dominate everything else.

Also include a CPU-only decrement/branch loop and a buffered-output variant. Otherwise the benchmark mostly answers "how expensive are 100,000 tiny writes?" rather than "how expensive is the backend?"

## What Idris 2 itself says about RefC

Idris 2 has an official benchmark harness whose README says it was created specifically because optimizing the reference-counting backend needed a consistent performance metric. It has been tested with Chez and RefC. Its normal inputs target roughly 5 seconds on Chez, fast inputs roughly 1 second, with a 15-second timeout. The repository supplies the harness and fixtures, but does not publish a canonical current RefC-versus-C/Thumb result table.

RefC's documentation describes it as a lightweight/minimal-dependency portable backend for memory-constrained systems, while explicitly saying performance is not as good as the Scheme backends.

Current sources also show that RefC is not simply "ordinary C source and nothing else":

- `support/refc/Makefile` builds `libidris2_refc.a` from the RefC runtime C sources.
- `Compiler.RefC.CC` links the generated object with `libidris2_support.a`, `-lidris2_refc`, `-lgmp`, and `-lm`, plus whatever normal startup/libc pieces the C compiler driver supplies.
- the Prelude maps `putChar` to C `putchar` and `putStr` to Idris support code.
- RefC values use an `Idris2_Value *` representation with tags and reference-counting machinery. Current RefC has several packing/unboxing optimizations, so do not assume every primitive operation performs a heap allocation without inspecting the generated C/assembly for that exact program.

Sources:

- https://github.com/idris-lang/Idris2/blob/main/benchmark/readme.md
- https://idris2.readthedocs.io/en/latest/backends/refc.html
- https://github.com/idris-lang/Idris2/blob/main/src/Compiler/RefC/CC.idr
- https://github.com/idris-lang/Idris2/blob/main/support/refc/Makefile
- https://github.com/idris-lang/Idris2/blob/main/support/refc/_datatypes.h
- https://github.com/idris-lang/Idris2/blob/main/libs/prelude/Prelude/IO.idr

## Important correction about libc size

Do not say that a normally dynamically linked executable contains the whole `libc.so` in every executable. It does not.

There are three different quantities:

1. **executable file bytes** — the program's own ELF file;
2. **runtime dependency closure** — shared objects that must exist for it to run;
3. **mapped memory** — private pages plus shared library pages while it is running.

With dynamic libc, the program file contains relocation/symbol/dynamic-loader information and depends on a separately installed libc. Many programs can share that same libc on disk and many read-only pages in RAM.

With static linking, the linker normally pulls required archive members rather than blindly copying every byte of libc, but startup code and selected runtime/library objects still create a substantial fixed floor compared with a 101-byte or 114-byte syscall-only ELF.

For RefC specifically, the Idris documentation says the default Idris support libraries are statically linked. The current RefC compiler also asks the C linker for GMP and libm; whether those and libc are dynamic or static depends on the target toolchain and flags.

## The composability consequence

The real question is therefore not "does every C/RefC binary literally contain all of libc?" The useful question is:

> What is the fixed runtime floor per independently executable program, and what dependencies must be present before the useful few dozen bytes of work can run?

For a runtime-free Thumb backend, the useful program can remain close to the machine instructions themselves. In this experiment, changing one output into 100,000 repeated outputs changed the ELF from 101 to 114 bytes.

For a runtime-backed backend, a tiny source program can be dominated by startup code, runtime representation, memory-management support, FFI bridges, and library linkage. That matters when composing many tiny independent executables even if the shared-library bytes are not duplicated in every file.

An alternative composition model is one shared runtime/service hosting many tiny programs. That amortizes the runtime floor but gives up the property that each tiny program is independently runtime-free.

## Measurement matrix to run next

Use the same semantic fixtures and report these axes separately:

| Fixture | Why |
| --- | --- |
| one `X` | fixed startup/runtime floor |
| `X\b` × 100,000, tiny writes | call/syscall overhead stress |
| same 200,000 output bytes, buffered | remove syscall-count confound |
| 100,000 or 100,000,000 counter loop, no I/O | integer/branch/backend overhead |
| allocation-heavy constructor loop | deliberately expose RefC reference counting |

For each target/backend record:

- executable bytes
- loadable segment bytes
- dynamic dependency list and total dependency-closure bytes
- stripped and unstripped sizes
- cold-start and warm-start elapsed time
- user and system CPU time
- maximum RSS
- kernel syscall counts by type
- allocation/free/reference-count counts when observable
- generated C and generated assembly for the exact revision

Compare at least:

- runtime-free Arm Thumb
- tiny C using raw syscalls
- C using libc
- Idris 2 RefC
- Idriç Thumb at the current supported source-ABI boundary

The benchmark result should preserve the distinction between *program representation cost*, *runtime/dependency cost*, and *work cost*.
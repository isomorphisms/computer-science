# Armv7-A / A32 / T32 instruction primitives

This folder is the 32-bit Arm execution path relevant to the current Termux environment. The observed environment reports `uname -m` as `armv7l`, `getconf LONG_BIT` as `32`, and Termux/Debian architecture `arm`.

This is **not** folded into `Arm A64 - Cortex-A55/`. A64, A32, and T32 are different instruction sets. A64 executes in AArch64 state. A32 and T32 execute in AArch32 state. A later core such as Cortex-A55 can execute AArch32 code while still being an Armv8-class core, so a 32-bit userspace string does not mean the silicon itself is an Armv7 core.

The scope here is the Armv7-A application-profile compatibility floor used by 32-bit Android/`armeabi-v7a`, plus the VFP/Advanced SIMD register path needed to explain floating-point execution on this phone. Armv8 AArch32 additions specific to Cortex-A55 should be layered separately rather than silently added to the Armv7 baseline.

## A32 versus T32

- **A32** is the 32-bit Arm instruction set formerly called the ARM instruction set. Its instructions are fixed 32-bit encodings.
- **T32** is the Thumb/Thumb-2 instruction set. It mixes 16-bit and 32-bit encodings.
- Both operate on the same 32-bit architectural core register state in AArch32.
- A program may contain both A32 and T32 code and cross between them using interworking branches such as `BX` and `BLX`.
- A mnemonic that exists in both sets is not therefore the same machine instruction encoding. Operand restrictions and conditional-execution rules can differ.
- T32 has instructions/constructs such as `IT`, `CBZ`, `CBNZ`, `TBB`, and `TBH` that are especially characteristic of Thumb-2. A32 instead has broad per-instruction conditional execution.

This is exactly why these instructions do not belong in the A64 catalog.

## Architectural state visible to ordinary code

### Core registers

A32/T32 expose sixteen 32-bit core registers:

| Register | Conventional role at a public AAPCS32 call boundary |
| --- | --- |
| `r0`-`r3` | arguments, results, scratch |
| `r4`-`r8` | callee-saved local-value registers |
| `r9` | platform-defined, or callee-saved when used as `v6` |
| `r10`-`r11` | callee-saved; `r11` may be a frame pointer |
| `r12` | intra-procedure-call scratch (`IP`) |
| `r13` | stack pointer (`SP`) |
| `r14` | link register (`LR`) |
| `r15` | program counter (`PC`) |

The condition/status state is in `CPSR`, including `N`, `Z`, `C`, and `V` condition flags and the `T` execution-state bit.

### VFP / Advanced SIMD registers

The AArch32 floating-point and Advanced SIMD register file is physically distinct from `r0`-`r15`.

- `s0`-`s31`: 32 single-precision, 32-bit views.
- `d0`-`d15`: 16 double-precision, 64-bit views overlapping the `s` registers.
- VFPv3 can add `d16`-`d31`.
- `q0`-`q15`: 128-bit Advanced SIMD/NEON views when the corresponding `d` registers are present.
- `FPSCR`: floating-point status/control state.

The important overlap is, for example, `d0 = {s0,s1}` and `q0 = {d0,d1}`. These are not aliases of the general-purpose registers.

At a procedure-call boundary, `s16`-`s31` / `d8`-`d15` / `q4`-`q7` are call-preserved. `s0`-`s15` / `d0`-`d7` / `q0`-`q3` are call-clobbered and form the VFP argument/result bank in the VFP calling-standard variant. The additional `d16`-`d31` / `q8`-`q15` registers, when present, are also call-clobbered.

## Three different meanings people collapse into "soft float"

There are three distinct compiler/ABI choices. The middle one is the Android `armeabi-v7a` case.

| Compiler choice | Floating arithmetic | Floating arguments/results at an ordinary public call |
| --- | --- | --- |
| `-mfloat-abi=soft` | compiler emits library calls instead of VFP arithmetic instructions | core registers / stack |
| `-mfloat-abi=softfp` | compiler may emit VFP/NEON hardware instructions | core registers / stack |
| `-mfloat-abi=hard` | compiler emits VFP/NEON hardware instructions | VFP registers for eligible non-variadic FP/vector values |

Therefore **softfp does not mean the addition happens in software**. It means the public call boundary uses the base/core-register PCS even though the callee can move those bits into floating-point registers and execute `VADD`, `VMUL`, and so on.

Android's 32-bit `armeabi-v7a` ABI uses `softfp` for historical ABI compatibility. The old Android `armeabi-v7a-hard` variant changed the function-call convention to the VFP variant; Android NDK support for that variant was later removed. None of this removes the floating-point hardware.

## Concrete call boundary: `float add(float a, float b)`

The examples below show the machine-level path. They are intentionally minimal and omit prologues that are unnecessary for a leaf routine.

### Android `armeabi-v7a`: softfp call boundary, hardware arithmetic

Caller-visible state:

```text
r0 = IEEE-754 bit pattern of a
r1 = IEEE-754 bit pattern of b
BL add
r0 = IEEE-754 bit pattern of result
```

A callee can then use the VFP hardware:

```asm
add:
    vmov     s0, r0
    vmov     s1, r1
    vadd.f32 s0, s0, s1
    vmov     r0, s0
    bx       lr
```

The arithmetic happens in the floating-point execution hardware selected by `VADD.F32`. The only general-register work here is transporting the argument/result bit patterns across the ABI boundary.

### Hard-float VFP call boundary

Caller-visible state:

```text
s0 = a
s1 = b
BL add
s0 = result
```

Leaf callee:

```asm
add:
    vadd.f32 s0, s0, s1
    bx       lr
```

The difference from `softfp` is the call boundary: no `r0/r1` <-> `s0/s1` transfer is needed.

### True software-float compilation

With `-mfloat-abi=soft`, a compiler does not emit the VFP arithmetic instruction for this operation. For a wrapper with exactly this signature, a typical ARM EABI implementation can tail-call the software helper:

```asm
add:
    b        __aeabi_fadd
```

Here `r0` and `r1` carry the two 32-bit floating bit patterns and `r0` carries the result. In a larger function the compiler can instead issue `BL __aeabi_fadd` while preserving its own return address in the normal function prologue.

That is the case where "software float" is actually a useful description.

## Concrete call boundary: `double add(double a, double b)`

Under the base/softfp PCS, an aligned 64-bit argument occupies an even-numbered core-register pair:

```text
r0:r1 = bit pattern of a
r2:r3 = bit pattern of b
BL add
r0:r1 = bit pattern of result
```

Hardware arithmetic inside the callee can be:

```asm
add:
    vmov     d0, r0, r1
    vmov     d1, r2, r3
    vadd.f64 d0, d0, d1
    vmov     r0, r1, d0
    bx       lr
```

Under the hard/VFP PCS:

```text
d0 = a
d1 = b
BL add
d0 = result
```

and the leaf routine is simply:

```asm
add:
    vadd.f64 d0, d0, d1
    bx       lr
```

For variadic functions, AAPCS32 uses the base convention rather than the VFP-register argument variant.

## What `VADD` means physically here

`VADD.F32 s0, s0, s1` is not "add in a floating-point register" in the sense that the register itself performs addition.

More precisely:

1. the instruction decoder recognizes a VFP/Advanced-SIMD floating-point add encoding;
2. source operands are read from the floating-point/SIMD register file (`s0`, `s1`);
3. the operation is issued to the core's floating-point execution datapath;
4. the result is written back to the destination floating-point register (`s0`).

By contrast, a normal integer `ADD r0, r0, r1` reads the core register file, uses the integer add datapath, and writes the core register file.

A softfp boundary can therefore look like:

```text
caller r0/r1
    -> VMOV into s0/s1
    -> floating-point execution unit executes VADD.F32
    -> VMOV result back to r0
    -> return
```

A hard-float boundary starts and ends directly in the `s`/`d` register bank.

## Instruction inventory scope

`instructions.txt` is a mnemonic-family inventory for the Armv7-A A32/T32 application path plus the VFPv3/Advanced-SIMD families relevant to a modern 32-bit Android Arm device. It is not an encoding table.

Important boundaries:

- Many mnemonics have several A32 and/or T32 encodings.
- Condition suffixes such as `EQ`, `NE`, `LT`, and `GE`, flag-setting `S`, and data-type suffixes such as `.F32` are not expanded into separate mnemonic lines.
- VFP and NEON are extension instruction families layered on the base integer ISA. The actual Cortex-A55 AArch32 implementation is richer than a pure Armv7-A baseline.
- Instructions such as `HVC`, `SMC`, coprocessor/system-register operations, and interrupt-control operations are privileged or environment-dependent and are not generally usable from an Android application.
- Integer divide (`SDIV`/`UDIV`) and some other features were optional extensions on Armv7 implementations even though later cores commonly implement them.
- The list is intentionally separate from A64. A64 mnemonics with similar names use different architectural registers and different encodings.

## Useful compiler views

These are different choices and should not be conflated:

```text
-march=armv7-a       choose the Armv7-A architectural instruction baseline
-marm                emit A32
-mthumb              emit T32
-mfpu=...            choose permitted VFP/NEON instruction subset
-mfloat-abi=soft     library floating arithmetic + base call PCS
-mfloat-abi=softfp   hardware FP permitted + base call PCS
-mfloat-abi=hard     hardware FP permitted + VFP call PCS
```

For Android `armeabi-v7a`, the function-call ABI is `softfp`: floating-point values cross normal public calls through core registers/stack even while arithmetic can use VFP/NEON hardware.

## Sources

- Arm, *Procedure Call Standard for the Arm Architecture (AAPCS32)*: https://github.com/ARM-software/abi-aa/blob/main/aapcs32/aapcs32.rst
- Android NDK, *Android ABIs*: https://developer.android.com/ndk/guides/abis
- Android NDK, *ARM Hard Float ABI Removal*: https://android.googlesource.com/platform/ndk/+/refs/tags/ndk-r28b/docs/HardFloatAbi.md
- GCC, *ARM Options* (`-mfloat-abi`): https://gcc.gnu.org/onlinedocs/gcc/ARM-Options.html
- Arm, *The Armv7-A Architecture*: https://developer.arm.com/-/media/Arm%20Developer%20Community/PDF/The%20Armv7-A%20Architecture.pdf
- Arm, *Introducing the Arm architecture*: https://developer.arm.com/-/media/Arm%20Developer%20Community/PDF/Learn%20the%20Architecture/Introducing%20the%20Arm%20architecture.pdf
- Arm, *NEON programming quick reference*: https://developer.arm.com/community/arm-community-blogs/b/operating-systems-blog/posts/arm-neon-programming-quick-reference

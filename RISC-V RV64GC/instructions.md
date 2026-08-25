# RISC-V RV64GC instruction primitives

RISC-V is deliberately modular, so there is no honest single list called “all RISC-V instructions” without naming an ISA string or profile. This folder uses **RV64GC** as a concrete 64-bit general-purpose baseline: `RV64I + M + A + F + D + Zicsr + Zifencei + C`. RISC-V defines `G` as `IMAFD_Zicsr_Zifencei`; adding `C` supplies the standard 16-bit compressed instruction encodings.

That makes this a useful comparison target for the phone’s A64 instruction set without pretending that every optional RISC-V extension—vectors, bit manipulation, cryptography, hypervisor, packed SIMD, and so on—is part of the same primitive machine.

## Architectural state

- integer registers `x0`–`x31`; `x0` always reads as zero
- program counter (not an ordinary integer register)
- floating-point registers `f0`–`f31` for F/D
- floating-point control/status CSR state
- implementation/privilege CSRs, accessed by the Zicsr instruction forms when permitted
- memory-ordering state and reservation sets used by atomics

ABI names such as `zero`, `ra`, `sp`, `a0`, and `s0` are register naming conventions, not extra registers. Likewise assembler pseudo-instructions such as `li`, `mv`, `not`, `neg`, `ret`, `call`, and `tail` are conveniences assembled into architectural instructions; they are not listed as independent primitives here.

## Encoding shape

RV64G uses the base 32-bit instruction encoding space. The `C` extension adds 16-bit encodings for common operations. This is one of the clearest contrasts with A64, whose ordinary instructions use fixed 32-bit encodings.

## Sources

- RISC-V unprivileged ISA, RV32/64G instruction listings: https://docs.riscv.org/reference/isa/v20260120/unpriv/rv-32-64g.html
- RISC-V ISA extension naming conventions: https://docs.riscv.org/reference/isa/unpriv/naming.html
- RISC-V opcode database: https://github.com/riscv/riscv-opcodes
- RISC-V compressed extension: https://docs.riscv.org/reference/isa/unpriv/c-st-ext.html

## Instruction list

### RV64I base

##### `LUI`

Base integer instruction.

##### `AUIPC`

Base integer instruction.

##### `JAL`

Jump-and-link control-flow instruction.

##### `JALR`

Jump-and-link control-flow instruction.

##### `BEQ`

Conditional branch instruction.

##### `BNE`

Conditional branch instruction.

##### `BLT`

Conditional branch instruction.

##### `BGE`

Conditional branch instruction.

##### `BLTU`

Conditional branch instruction.

##### `BGEU`

Conditional branch instruction.

##### `LB`

Integer load instruction.

##### `LH`

Integer load instruction.

##### `LW`

Integer load instruction.

##### `LBU`

Integer load instruction.

##### `LHU`

Integer load instruction.

##### `SB`

Integer store instruction.

##### `SH`

Integer store instruction.

##### `SW`

Integer store instruction.

##### `ADDI`

Base integer instruction.

##### `SLTI`

Base integer instruction.

##### `SLTIU`

Base integer instruction.

##### `XORI`

Base integer instruction.

##### `ORI`

Base integer instruction.

##### `ANDI`

Base integer instruction.

##### `SLLI`

Base integer instruction.

##### `SRLI`

Base integer instruction.

##### `SRAI`

Base integer instruction.

##### `ADD`

Base integer instruction.

##### `SUB`

Base integer instruction.

##### `SLL`

Base integer instruction.

##### `SLT`

Base integer instruction.

##### `SLTU`

Base integer instruction.

##### `XOR`

Base integer instruction.

##### `SRL`

Base integer instruction.

##### `SRA`

Base integer instruction.

##### `OR`

Base integer instruction.

##### `AND`

Base integer instruction.

##### `FENCE`

Memory-ordering fence.

##### `ECALL`

Environment/debug exception instruction.

##### `EBREAK`

Environment/debug exception instruction.

##### `LWU`

Integer load instruction.

##### `LD`

Integer load instruction.

##### `SD`

Integer store instruction.

##### `ADDIW`

Base integer instruction.

##### `SLLIW`

Base integer instruction.

##### `SRLIW`

Base integer instruction.

##### `SRAIW`

Base integer instruction.

##### `ADDW`

Base integer instruction.

##### `SUBW`

Base integer instruction.

##### `SLLW`

Base integer instruction.

##### `SRLW`

Base integer instruction.

##### `SRAW`

Base integer instruction.

### Zifencei

##### `FENCE.I`

Synchronizes instruction fetch with prior stores to instruction memory.

### Zicsr

##### `CSRRW`

Atomic control-and-status-register read/modify/write instruction.

##### `CSRRS`

Atomic control-and-status-register read/modify/write instruction.

##### `CSRRC`

Atomic control-and-status-register read/modify/write instruction.

##### `CSRRWI`

Atomic control-and-status-register read/modify/write instruction.

##### `CSRRSI`

Atomic control-and-status-register read/modify/write instruction.

##### `CSRRCI`

Atomic control-and-status-register read/modify/write instruction.

### M

##### `MUL`

Integer multiplication/division extension instruction.

##### `MULH`

Integer multiplication/division extension instruction.

##### `MULHSU`

Integer multiplication/division extension instruction.

##### `MULHU`

Integer multiplication/division extension instruction.

##### `DIV`

Integer multiplication/division extension instruction.

##### `DIVU`

Integer multiplication/division extension instruction.

##### `REM`

Integer multiplication/division extension instruction.

##### `REMU`

Integer multiplication/division extension instruction.

##### `MULW`

Integer multiplication/division extension instruction.

##### `DIVW`

Integer multiplication/division extension instruction.

##### `DIVUW`

Integer multiplication/division extension instruction.

##### `REMW`

Integer multiplication/division extension instruction.

##### `REMUW`

Integer multiplication/division extension instruction.

### A

##### `LR.W`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `SC.W`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOSWAP.W`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOADD.W`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOXOR.W`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOAND.W`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOOR.W`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOMIN.W`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOMAX.W`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOMINU.W`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOMAXU.W`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `LR.D`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `SC.D`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOSWAP.D`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOADD.D`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOXOR.D`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOAND.D`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOOR.D`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOMIN.D`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOMAX.D`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOMINU.D`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

##### `AMOMAXU.D`

Atomic memory instruction. `.W` operates on 32-bit words; `.D` on 64-bit doublewords; aq/rl bits select acquire/release ordering.

### F

##### `FLW`

Single-precision IEEE-754 floating-point instruction.

##### `FSW`

Single-precision IEEE-754 floating-point instruction.

##### `FMADD.S`

Single-precision IEEE-754 floating-point instruction.

##### `FMSUB.S`

Single-precision IEEE-754 floating-point instruction.

##### `FNMSUB.S`

Single-precision IEEE-754 floating-point instruction.

##### `FNMADD.S`

Single-precision IEEE-754 floating-point instruction.

##### `FADD.S`

Single-precision IEEE-754 floating-point instruction.

##### `FSUB.S`

Single-precision IEEE-754 floating-point instruction.

##### `FMUL.S`

Single-precision IEEE-754 floating-point instruction.

##### `FDIV.S`

Single-precision IEEE-754 floating-point instruction.

##### `FSQRT.S`

Single-precision IEEE-754 floating-point instruction.

##### `FSGNJ.S`

Single-precision IEEE-754 floating-point instruction.

##### `FSGNJN.S`

Single-precision IEEE-754 floating-point instruction.

##### `FSGNJX.S`

Single-precision IEEE-754 floating-point instruction.

##### `FMIN.S`

Single-precision IEEE-754 floating-point instruction.

##### `FMAX.S`

Single-precision IEEE-754 floating-point instruction.

##### `FCVT.W.S`

Single-precision IEEE-754 floating-point instruction.

##### `FCVT.WU.S`

Single-precision IEEE-754 floating-point instruction.

##### `FMV.X.W`

Single-precision IEEE-754 floating-point instruction.

##### `FEQ.S`

Single-precision IEEE-754 floating-point instruction.

##### `FLT.S`

Single-precision IEEE-754 floating-point instruction.

##### `FLE.S`

Single-precision IEEE-754 floating-point instruction.

##### `FCLASS.S`

Single-precision IEEE-754 floating-point instruction.

##### `FCVT.S.W`

Single-precision IEEE-754 floating-point instruction.

##### `FCVT.S.WU`

Single-precision IEEE-754 floating-point instruction.

##### `FMV.W.X`

Single-precision IEEE-754 floating-point instruction.

##### `FCVT.L.S`

Single-precision IEEE-754 floating-point instruction.

##### `FCVT.LU.S`

Single-precision IEEE-754 floating-point instruction.

##### `FCVT.S.L`

Single-precision IEEE-754 floating-point instruction.

##### `FCVT.S.LU`

Single-precision IEEE-754 floating-point instruction.

### D

##### `FLD`

Double-precision IEEE-754 floating-point instruction.

##### `FSD`

Double-precision IEEE-754 floating-point instruction.

##### `FMADD.D`

Double-precision IEEE-754 floating-point instruction.

##### `FMSUB.D`

Double-precision IEEE-754 floating-point instruction.

##### `FNMSUB.D`

Double-precision IEEE-754 floating-point instruction.

##### `FNMADD.D`

Double-precision IEEE-754 floating-point instruction.

##### `FADD.D`

Double-precision IEEE-754 floating-point instruction.

##### `FSUB.D`

Double-precision IEEE-754 floating-point instruction.

##### `FMUL.D`

Double-precision IEEE-754 floating-point instruction.

##### `FDIV.D`

Double-precision IEEE-754 floating-point instruction.

##### `FSQRT.D`

Double-precision IEEE-754 floating-point instruction.

##### `FSGNJ.D`

Double-precision IEEE-754 floating-point instruction.

##### `FSGNJN.D`

Double-precision IEEE-754 floating-point instruction.

##### `FSGNJX.D`

Double-precision IEEE-754 floating-point instruction.

##### `FMIN.D`

Double-precision IEEE-754 floating-point instruction.

##### `FMAX.D`

Double-precision IEEE-754 floating-point instruction.

##### `FCVT.S.D`

Double-precision IEEE-754 floating-point instruction.

##### `FCVT.D.S`

Double-precision IEEE-754 floating-point instruction.

##### `FEQ.D`

Double-precision IEEE-754 floating-point instruction.

##### `FLT.D`

Double-precision IEEE-754 floating-point instruction.

##### `FLE.D`

Double-precision IEEE-754 floating-point instruction.

##### `FCLASS.D`

Double-precision IEEE-754 floating-point instruction.

##### `FCVT.W.D`

Double-precision IEEE-754 floating-point instruction.

##### `FCVT.WU.D`

Double-precision IEEE-754 floating-point instruction.

##### `FCVT.D.W`

Double-precision IEEE-754 floating-point instruction.

##### `FCVT.D.WU`

Double-precision IEEE-754 floating-point instruction.

##### `FCVT.L.D`

Double-precision IEEE-754 floating-point instruction.

##### `FCVT.LU.D`

Double-precision IEEE-754 floating-point instruction.

##### `FMV.X.D`

Double-precision IEEE-754 floating-point instruction.

##### `FCVT.D.L`

Double-precision IEEE-754 floating-point instruction.

##### `FCVT.D.LU`

Double-precision IEEE-754 floating-point instruction.

##### `FMV.D.X`

Double-precision IEEE-754 floating-point instruction.

### C

##### `C.ADDI4SPN`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.LW`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.SW`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.NOP`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.ADDI`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.LI`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.ADDI16SP`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.LUI`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.ANDI`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.SUB`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.XOR`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.OR`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.AND`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.J`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.BEQZ`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.BNEZ`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.LWSP`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.JR`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.MV`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.EBREAK`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.JALR`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.ADD`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.SWSP`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.LD`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.SD`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.ADDIW`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.SRLI`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.SRAI`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.SUBW`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.ADDW`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.SLLI`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.LDSP`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.SDSP`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.FLD`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.FSD`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.FLDSP`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

##### `C.FSDSP`

16-bit compressed instruction encoding; expands the assembler-visible ISA without adding new architectural registers.

## Important boundary: pseudo-instructions

The assembler can present a larger vocabulary than the hardware decoder. For example, `ret` is normally a `JALR` form, and `mv` is normally an `ADDI` form. This repository should keep that derived/alias layer separate from the architectural opcode layer, just as gesture recognizers are kept separate from raw touchscreen measurements.

## Extensions not included here

Not included: `B`/Zb* bit manipulation, `V` vectors, scalar/vector cryptography, hypervisor instructions, packed SIMD, cache-block operations, newer atomics, and the many other standardized Z/S extensions. They are real RISC-V primitives, but they are not part of the specific **RV64GC** machine chosen for this first catalog.

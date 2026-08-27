# RISC-V compared with Arm Thumb

A useful difference for instruction-level work is how the two ISAs represent small conditions and predicates.

Arm Thumb often lets an arithmetic instruction both compute a value and leave a small fact behind in the condition flags. For example:

```asm
subs r0, r0, #1
bne loop
```

`SUBS` performs the subtraction and updates flags such as zero, negative, carry, and overflow; `BNE` then consumes the zero condition.

Base RISC-V deliberately has no general condition-code register. The same loop is normally written with the condition exposed directly in the branch:

```asm
addi a0, a0, -1
bne  a0, x0, loop
```

Likewise, a comparison can be part of a branch (`beq`, `bne`, `blt`, `bge`, `bltu`, `bgeu`) rather than a separate compare-then-flags sequence. When the comparison itself must survive as data, instructions such as `slt` and `sltu` materialize the predicate as an ordinary integer register containing exactly 0 or 1.

This makes RISC-V interesting for tiny predicates and one-bit state: Arm often has such facts temporarily implicit in condition flags, whereas RISC-V tends to make them either part of the branch operation or explicit ordinary register values.

A few rough Thumb → RISC-V correspondences are:

| Operation | Arm Thumb | RISC-V |
| --- | --- | --- |
| small constant | `MOVS` | `ADDI rd, x0, imm` |
| add | `ADDS` / `ADD` | `ADD` / `ADDI` |
| subtract | `SUBS` / `SUB` | `SUB` / `ADDI ..., -imm` |
| bitwise AND | `ANDS` / `AND` | `AND` / `ANDI` |
| shift left | `LSLS` / `LSL` | `SLL` / `SLLI` |
| load byte | `LDRB` | `LBU` / `LB` |
| load halfword | `LDRH` | `LHU` / `LH` |
| store halfword | `STRH` | `SH` |
| load word | `LDR` | `LW` |
| store word | `STR` | `SW` |
| compare + equal branch | `CMP` + `BEQ` | `BEQ rs1, rs2, label` |
| compare + less-than branch | `CMP` + `BLT` | `BLT rs1, rs2, label` |
| call | `BL` | `JAL` (usually via the `call` pseudo-instruction) |
| return | `BX lr` | `JALR x0, 0(ra)` (usually `ret`) |

For terminology, `RV64I` is an instruction-set architecture: the 64-bit base integer RISC-V ISA. `LP64` is not an ISA extension; it is an ABI/data-model convention in which `long` and pointers are 64 bits.

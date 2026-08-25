# Arm A64 / Cortex-A55 instruction primitives

This folder is the 64-bit Arm instruction-set slice relevant to the MIRO A1 phone. Public specifications list the MIRO A1 as an SC9863-based eight-core Cortex-A55 phone. Cortex-A55 implements Armv8-A with Armv8.1-A and Armv8.2-A extensions, plus the Armv8.3-A `LDAPR` instructions and Armv8.4-A dot-product instructions. The core supports A64, A32, and T32; this file deliberately catalogs **A64**, the 64-bit instruction set used by the `arm64-v8a` Android ABI.

The mnemonic inventory below is a first-pass machine-derived catalog from Arm’s open-source machine-readable A64 specification, filtered to the Cortex-A55/SC9863A feature envelope: floating point, Advanced SIMD/NEON, AES/PMULL, SHA-1, SHA-256, CRC32, LSE atomics, FP16, RDM, RCpc/`LDAPR`, dot product, RAS, and limited-ordering support. Official assembler aliases such as `CMP`, `MOV`, `DC`, and `TLBI` are retained.

## Architecture versus core

- **Cortex-A55** is a microarchitecture: pipeline, caches, execution units, latency, throughput, and implementation choices.
- **A64** is the 64-bit instruction set.
- **Armv8.x-A** names architecture revisions and feature sets.
- **SC9863/SC9863A** is the SoC family integrating Cortex-A55-class CPU cores and other components.

This distinction matters for the repository: the mnemonic/encoding is a machine-language primitive, while latency, throughput, cache behavior, and power are properties of a particular implementation of that primitive.

## State the instructions operate on

- integer registers `X0`–`X30` and low 32-bit views `W0`–`W30`
- stack pointer `SP`; zero-register spellings `XZR` / `WZR`
- 32 × 128-bit SIMD/FP registers `V0`–`V31`
- condition flags `N Z C V`
- floating-point control/status state (`FPCR`, `FPSR`)
- system registers and exception-level state
- memory, ordering, cache, TLB, and reservation state touched by architected memory/system operations

## Important boundaries

An instruction mnemonic can have multiple operand forms and encodings; a flat mnemonic list is therefore not the complete encoding space. Some mnemonics below are architecturally defined aliases of other encodings. Many system instructions are privileged and cannot be executed by an ordinary Android app. AArch64 assembly also has assembler conveniences beyond the architectural aliases; those belong in a separate assembler layer.

The Cortex-A55 also supports **A32** and **T32** in AArch32 state. They are not mixed into this file because they are separate instruction sets with their own encodings and large legacy surfaces.

Android's `arm64-v8a` ABI has an Armv8.0 instruction-set baseline. That ABI name does not promise the later Cortex-A55 features cataloged here. Code distributed for general `arm64-v8a` devices must either stay within the ABI baseline or select later instructions only after build-time device targeting or runtime feature detection; the entries here describe this core/device feature envelope, not the ABI minimum.

## Sources

- MIRO A1 specification: https://www.newegg.com/miro-a1-5-99-4g-black/p/23B-00MN-00003
- Cortex-A55 product/support page: https://developer.arm.com/compute-ip/cortex-a55
- Cortex-A55 Software Optimization Guide: https://documentation-service.arm.com/static/5f1fe66bbb903e39c84d7d75
- Arm AARCHMRS machine-readable architecture overview: https://developer.arm.com/community/arm-community-blogs/b/architectures-and-processors-blog/posts/getting-started-aarchmrs-features-json-python
- Linux arm64 HWCAP documentation: https://docs.kernel.org/arch/arm64/elf_hwcaps.html
- SC9863A observed CPU feature records: https://cknowledge.org/repo/web.php?action=index&module_uoa=wfe&native_action=show&native_module_uoa=platform.cpu

## Instruction mnemonics

### Branches and returns

Direct, indirect, link, compare-and-branch, test-and-branch, and return operations.

##### `B`

##### `BL`

##### `BLR`

##### `BR`

##### `CBNZ`

##### `CBZ`

##### `DRPS`

##### `ERET`

##### `RET`

##### `TBNZ`

##### `TBZ`

### Exceptions and debug

Instructions that deliberately enter exception/debug mechanisms. Access and effect can depend on exception level.

##### `BRK`

##### `DCPS1`

##### `DCPS2`

##### `DCPS3`

##### `HLT`

##### `HVC`

##### `SMC`

##### `SVC`

##### `UDF`

### Synchronization, events, and hints

Memory/order barriers, event/wait primitives, and architected hints.

##### `CLREX`

##### `CSDB`

##### `DMB`

##### `DSB`

##### `ESB`

##### `HINT`

##### `ISB`

##### `NOP`

##### `PSSBB`

##### `SEV`

##### `SEVL`

##### `SSBB`

##### `WFE`

##### `WFI`

##### `YIELD`

### System operations

System-register, address-translation, cache, instruction-cache, and TLB operations. Many forms are privileged.

##### `AT`

##### `DC`

##### `IC`

##### `MRS`

##### `MSR`

##### `SYS`

##### `SYSL`

##### `TLBI`

### Loads, stores, and memory atomics

Ordinary loads/stores, acquire/release forms, exclusive accesses, pair accesses, prefetch, RCpc load-acquire, and LSE atomic read-modify-write operations.

##### `CAS`

##### `CASA`

##### `CASAB`

##### `CASAH`

##### `CASAL`

##### `CASALB`

##### `CASALH`

##### `CASB`

##### `CASH`

##### `CASL`

##### `CASLB`

##### `CASLH`

##### `CASP`

##### `CASPA`

##### `CASPAL`

##### `CASPL`

##### `LD1`

##### `LD1R`

##### `LD2`

##### `LD2R`

##### `LD3`

##### `LD3R`

##### `LD4`

##### `LD4R`

##### `LDADD`

##### `LDADDA`

##### `LDADDAB`

##### `LDADDAH`

##### `LDADDAL`

##### `LDADDALB`

##### `LDADDALH`

##### `LDADDB`

##### `LDADDH`

##### `LDADDL`

##### `LDADDLB`

##### `LDADDLH`

##### `LDAPR`

##### `LDAPRB`

##### `LDAPRH`

##### `LDAR`

##### `LDARB`

##### `LDARH`

##### `LDAXP`

##### `LDAXR`

##### `LDAXRB`

##### `LDAXRH`

##### `LDCLR`

##### `LDCLRA`

##### `LDCLRAB`

##### `LDCLRAH`

##### `LDCLRAL`

##### `LDCLRALB`

##### `LDCLRALH`

##### `LDCLRB`

##### `LDCLRH`

##### `LDCLRL`

##### `LDCLRLB`

##### `LDCLRLH`

##### `LDEOR`

##### `LDEORA`

##### `LDEORAB`

##### `LDEORAH`

##### `LDEORAL`

##### `LDEORALB`

##### `LDEORALH`

##### `LDEORB`

##### `LDEORH`

##### `LDEORL`

##### `LDEORLB`

##### `LDEORLH`

##### `LDLAR`

##### `LDLARB`

##### `LDLARH`

##### `LDNP`

##### `LDP`

##### `LDPSW`

##### `LDR`

##### `LDRB`

##### `LDRH`

##### `LDRSB`

##### `LDRSH`

##### `LDRSW`

##### `LDSET`

##### `LDSETA`

##### `LDSETAB`

##### `LDSETAH`

##### `LDSETAL`

##### `LDSETALB`

##### `LDSETALH`

##### `LDSETB`

##### `LDSETH`

##### `LDSETL`

##### `LDSETLB`

##### `LDSETLH`

##### `LDSMAX`

##### `LDSMAXA`

##### `LDSMAXAB`

##### `LDSMAXAH`

##### `LDSMAXAL`

##### `LDSMAXALB`

##### `LDSMAXALH`

##### `LDSMAXB`

##### `LDSMAXH`

##### `LDSMAXL`

##### `LDSMAXLB`

##### `LDSMAXLH`

##### `LDSMIN`

##### `LDSMINA`

##### `LDSMINAB`

##### `LDSMINAH`

##### `LDSMINAL`

##### `LDSMINALB`

##### `LDSMINALH`

##### `LDSMINB`

##### `LDSMINH`

##### `LDSMINL`

##### `LDSMINLB`

##### `LDSMINLH`

##### `LDTR`

##### `LDTRB`

##### `LDTRH`

##### `LDTRSB`

##### `LDTRSH`

##### `LDTRSW`

##### `LDUMAX`

##### `LDUMAXA`

##### `LDUMAXAB`

##### `LDUMAXAH`

##### `LDUMAXAL`

##### `LDUMAXALB`

##### `LDUMAXALH`

##### `LDUMAXB`

##### `LDUMAXH`

##### `LDUMAXL`

##### `LDUMAXLB`

##### `LDUMAXLH`

##### `LDUMIN`

##### `LDUMINA`

##### `LDUMINAB`

##### `LDUMINAH`

##### `LDUMINAL`

##### `LDUMINALB`

##### `LDUMINALH`

##### `LDUMINB`

##### `LDUMINH`

##### `LDUMINL`

##### `LDUMINLB`

##### `LDUMINLH`

##### `LDUR`

##### `LDURB`

##### `LDURH`

##### `LDURSB`

##### `LDURSH`

##### `LDURSW`

##### `LDXP`

##### `LDXR`

##### `LDXRB`

##### `LDXRH`

##### `PRFM`

##### `PRFUM`

##### `ST1`

##### `ST2`

##### `ST3`

##### `ST4`

##### `STADD`

##### `STADDB`

##### `STADDH`

##### `STADDL`

##### `STADDLB`

##### `STADDLH`

##### `STCLR`

##### `STCLRB`

##### `STCLRH`

##### `STCLRL`

##### `STCLRLB`

##### `STCLRLH`

##### `STEOR`

##### `STEORB`

##### `STEORH`

##### `STEORL`

##### `STEORLB`

##### `STEORLH`

##### `STLLR`

##### `STLLRB`

##### `STLLRH`

##### `STLR`

##### `STLRB`

##### `STLRH`

##### `STLXP`

##### `STLXR`

##### `STLXRB`

##### `STLXRH`

##### `STNP`

##### `STP`

##### `STR`

##### `STRB`

##### `STRH`

##### `STSET`

##### `STSETB`

##### `STSETH`

##### `STSETL`

##### `STSETLB`

##### `STSETLH`

##### `STSMAX`

##### `STSMAXB`

##### `STSMAXH`

##### `STSMAXL`

##### `STSMAXLB`

##### `STSMAXLH`

##### `STSMIN`

##### `STSMINB`

##### `STSMINH`

##### `STSMINL`

##### `STSMINLB`

##### `STSMINLH`

##### `STTR`

##### `STTRB`

##### `STTRH`

##### `STUMAX`

##### `STUMAXB`

##### `STUMAXH`

##### `STUMAXL`

##### `STUMAXLB`

##### `STUMAXLH`

##### `STUMIN`

##### `STUMINB`

##### `STUMINH`

##### `STUMINL`

##### `STUMINLB`

##### `STUMINLH`

##### `STUR`

##### `STURB`

##### `STURH`

##### `STXP`

##### `STXR`

##### `STXRB`

##### `STXRH`

##### `SWP`

##### `SWPA`

##### `SWPAB`

##### `SWPAH`

##### `SWPAL`

##### `SWPALB`

##### `SWPALH`

##### `SWPB`

##### `SWPH`

##### `SWPL`

##### `SWPLB`

##### `SWPLH`

### Integer/scalar data processing

Integer arithmetic, logical operations, shifts, bitfield operations, comparisons, multiply/divide, moves, and conditional selection.

##### `ADC`

##### `ADCS`

##### `ADD`

##### `ADDS`

##### `ADR`

##### `ADRP`

##### `AND`

##### `ANDS`

##### `ASR`

##### `ASRV`

##### `BFI`

##### `BFM`

##### `BFXIL`

##### `BIC`

##### `BICS`

##### `CCMN`

##### `CCMP`

##### `CINC`

##### `CINV`

##### `CLS`

##### `CLZ`

##### `CMN`

##### `CMP`

##### `CNEG`

##### `CSEL`

##### `CSET`

##### `CSETM`

##### `CSINC`

##### `CSINV`

##### `CSNEG`

##### `EON`

##### `EOR`

##### `EXTR`

##### `LSL`

##### `LSLV`

##### `LSR`

##### `LSRV`

##### `MADD`

##### `MNEG`

##### `MOV`

##### `MOVK`

##### `MOVN`

##### `MOVZ`

##### `MSUB`

##### `MUL`

##### `MVN`

##### `NEGS`

##### `NGC`

##### `NGCS`

##### `ORN`

##### `ORR`

##### `RBIT`

##### `REV`

##### `REV16`

##### `REV32`

##### `REV64`

##### `ROR`

##### `RORV`

##### `SBC`

##### `SBCS`

##### `SBFIZ`

##### `SBFM`

##### `SBFX`

##### `SDIV`

##### `SMADDL`

##### `SMSUBL`

##### `SMULH`

##### `SUB`

##### `SUBS`

##### `TST`

##### `UBFIZ`

##### `UBFM`

##### `UBFX`

##### `UDIV`

##### `UMADDL`

##### `UMSUBL`

##### `UMULH`

### Advanced SIMD integer/vector data processing

NEON/Advanced SIMD integer, lane, widening/narrowing, saturating, permutation, table, and vector reduction operations.

##### `ABS`

##### `ADDHN`

##### `ADDP`

##### `ADDV`

##### `BIF`

##### `BIT`

##### `BSL`

##### `CMEQ`

##### `CMGE`

##### `CMGT`

##### `CMHI`

##### `CMHS`

##### `CMLE`

##### `CMLT`

##### `CMTST`

##### `CNT`

##### `DUP`

##### `EXT`

##### `INS`

##### `MLA`

##### `MLS`

##### `MOVI`

##### `MVNI`

##### `NEG`

##### `NOT`

##### `PMUL`

##### `RADDHN`

##### `RSUBHN`

##### `SABA`

##### `SABAL`

##### `SABD`

##### `SABDL`

##### `SADALP`

##### `SADDL`

##### `SADDLP`

##### `SADDLV`

##### `SADDW`

##### `SHADD`

##### `SHL`

##### `SHLL`

##### `SHRN`

##### `SHSUB`

##### `SMAX`

##### `SMAXP`

##### `SMAXV`

##### `SMIN`

##### `SMINP`

##### `SMINV`

##### `SMLAL`

##### `SMLSL`

##### `SMULL`

##### `SQABS`

##### `SQADD`

##### `SQDMLAL`

##### `SQDMLSL`

##### `SQDMULH`

##### `SQDMULL`

##### `SQNEG`

##### `SQRDMLAH`

##### `SQRDMLSH`

##### `SQRDMULH`

##### `SQRSHL`

##### `SQRSHRN`

##### `SQRSHRUN`

##### `SQSHL`

##### `SQSHLU`

##### `SQSHRN`

##### `SQSHRUN`

##### `SQSUB`

##### `SQXTN`

##### `SQXTUN`

##### `SRHADD`

##### `SRI`

##### `SRSHL`

##### `SRSHR`

##### `SRSRA`

##### `SSUBL`

##### `SSUBW`

##### `TBL`

##### `TBX`

##### `TRN1`

##### `TRN2`

##### `UABA`

##### `UABAL`

##### `UABD`

##### `UABDL`

##### `UADALP`

##### `UADDL`

##### `UADDLP`

##### `UADDLV`

##### `UADDW`

##### `UHADD`

##### `UHSUB`

##### `UMAX`

##### `UMAXP`

##### `UMAXV`

##### `UMIN`

##### `UMINP`

##### `UMINV`

##### `UMLAL`

##### `UMLSL`

##### `UMULL`

##### `UQADD`

##### `UQRSHL`

##### `UQRSHRN`

##### `UQSHL`

##### `UQSHRN`

##### `UQSUB`

##### `UQXTN`

##### `URECPE`

##### `URHADD`

##### `URSHL`

##### `URSHR`

##### `URSQRTE`

##### `URSRA`

##### `USUBL`

##### `USUBW`

##### `UZP1`

##### `UZP2`

##### `XTN`

##### `ZIP1`

##### `ZIP2`

### Floating point and SIMD floating point

Scalar and vector floating-point arithmetic, comparison, conversion, reciprocal/square-root estimate, rounding, and fused operations, including supported FP16 forms.

##### `FABD`

##### `FABS`

##### `FACGE`

##### `FACGT`

##### `FADD`

##### `FADDP`

##### `FCCMP`

##### `FCCMPE`

##### `FCMEQ`

##### `FCMGE`

##### `FCMGT`

##### `FCMLE`

##### `FCMLT`

##### `FCMP`

##### `FCMPE`

##### `FCSEL`

##### `FCVT`

##### `FCVTAS`

##### `FCVTAU`

##### `FCVTL`

##### `FCVTMS`

##### `FCVTMU`

##### `FCVTN`

##### `FCVTNS`

##### `FCVTNU`

##### `FCVTPS`

##### `FCVTPU`

##### `FCVTXN`

##### `FCVTZS`

##### `FCVTZU`

##### `FDIV`

##### `FMADD`

##### `FMAX`

##### `FMAXNM`

##### `FMAXNMP`

##### `FMAXNMV`

##### `FMAXP`

##### `FMAXV`

##### `FMIN`

##### `FMINNM`

##### `FMINNMP`

##### `FMINNMV`

##### `FMINP`

##### `FMINV`

##### `FMLA`

##### `FMLS`

##### `FMOV`

##### `FMSUB`

##### `FMUL`

##### `FMULX`

##### `FNEG`

##### `FNMADD`

##### `FNMSUB`

##### `FNMUL`

##### `FRECPE`

##### `FRECPS`

##### `FRECPX`

##### `FRINTA`

##### `FRINTI`

##### `FRINTM`

##### `FRINTN`

##### `FRINTP`

##### `FRINTX`

##### `FRINTZ`

##### `FRSQRTE`

##### `FRSQRTS`

##### `FSQRT`

##### `FSUB`

##### `SCVTF`

##### `UCVTF`

### Cryptography and CRC

AES, carry-less polynomial multiply, SHA-1/SHA-256, and CRC32/CRC32C acceleration.

##### `AESD`

##### `AESE`

##### `AESIMC`

##### `AESMC`

##### `CRC32B`

##### `CRC32CB`

##### `CRC32CH`

##### `CRC32CW`

##### `CRC32CX`

##### `CRC32H`

##### `CRC32W`

##### `CRC32X`

##### `PMULL`

##### `SHA1C`

##### `SHA1H`

##### `SHA1M`

##### `SHA1P`

##### `SHA1SU0`

##### `SHA1SU1`

##### `SHA256H`

##### `SHA256H2`

##### `SHA256SU0`

##### `SHA256SU1`

### Dot product

The Cortex-A55’s Armv8.4-derived signed and unsigned integer dot-product instructions.

##### `SDOT`

##### `UDOT`

### Other A64 mnemonics and aliases

Architectural mnemonics/aliases that do not fit the coarse presentation groups above. The Arm ARM is authoritative for exact operand forms and semantics.

##### `RSHRN`

##### `SLI`

##### `SMNEGL`

##### `SMOV`

##### `SSHL`

##### `SSHLL`

##### `SSHR`

##### `SSRA`

##### `SUBHN`

##### `SUQADD`

##### `SXTB`

##### `SXTH`

##### `SXTL`

##### `SXTW`

##### `UMNEGL`

##### `UMOV`

##### `USHL`

##### `USHLL`

##### `USHR`

##### `USQADD`

##### `USRA`

##### `UXTB`

##### `UXTH`

##### `UXTL`

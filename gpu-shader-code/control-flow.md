# GPU shader control flow: branches without pretending the GPU is an Arm CPU

The CPU note [`notes/control-flow-from-the-bottom.md`](../notes/control-flow-from-the-bottom.md) gives the common conceptual core: control flow determines which block of instructions executes next.

The important GPU correction is that the portable shader model should **not** be described as though each shader invocation simply has an Arm-style `r15` program counter.

## Portable shader control flow

GLSL and similar source languages expose familiar constructs:

```text
if
else
switch
for
while
break
continue
return
```

A portable intermediate representation such as SPIR-V makes the control-flow graph much more explicit. Relevant operations include:

- `OpLabel`: begin a basic block;
- `OpBranch`: unconditional edge to another block;
- `OpBranchConditional`: choose between two block targets from a Boolean condition;
- `OpSwitch`: multi-way branch from an integer selector;
- `OpSelectionMerge`: declare where a structured selection reconverges;
- `OpLoopMerge`: declare the merge and continue structure of a loop;
- `OpPhi`: choose an SSA value according to which predecessor block reached the current block.

This is already low enough to expose the graph structure clearly while remaining independent of a particular GPU vendor's native machine encoding.

Reference: Khronos SPIR-V unified specification: <https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html>.

## Why a GPU branch is not quite the same performance story as a CPU branch

Many GPU shader invocations execute in groups. If every invocation in a group makes the same branch decision, execution can remain coherent. If neighboring invocations choose different paths, the implementation may have to execute the paths under different masks and reconverge them later.

That phenomenon is usually called **divergence**.

So there are two distinct questions:

1. **semantic question:** which block should this invocation execute next?
2. **execution question:** how does the GPU efficiently carry many invocations through those possibly different paths?

The first is visible in portable IR. The second is partly vendor- and architecture-specific.

This is why replacing a branch with arithmetic or a value-select operation can sometimes help GPU code, but it is not a universal rule. The compiler and native architecture may already predicate, simplify, flatten, or otherwise transform the source control flow.

## `switch` on a GPU

A shader `switch` does not promise a native hardware jump table.

At the SPIR-V layer, `OpSwitch` represents a multi-way control-flow choice. A later compiler may lower that to:

- native branches;
- a jump/table-like mechanism;
- a tree or chain of comparisons;
- predicated/masked execution;
- value selection;
- some vendor-specific mixture.

Therefore a compiler backend should preserve the semantics first and only make native-layout claims when there is evidence from the actual target compiler or ISA.

## PowerVR belongs under the GPU story, but at a different layer

For these notes, **PowerVR is a particular GPU implementation**, not a second portable shader language.

The useful layering is:

```text
Idris / Idriç source
    -> backend-generated GLSL ES or other shader source
    -> portable/compiler IR such as SPIR-V where applicable
    -> vendor driver/compiler
    -> PowerVR-native instructions / scheduling / execution
```

The existing [`gpu-shader-code/primitives.md`](primitives.md) intentionally documents portable shader semantics. A future PowerVR-native instruction catalog should be based on concrete documentation, disassembly, compiler output, or device evidence rather than naming GLSL/SPIR-V operations as though they were PowerVR opcodes.

## Opcode terminology on the GPU side

`OpBranch` in SPIR-V has an operation code in the SPIR-V binary format, but that is a **SPIR-V opcode**, not necessarily the opcode of a PowerVR machine instruction.

Likewise:

```text
GLSL `if`
    != one guaranteed SPIR-V instruction
    != one guaranteed PowerVR instruction
```

Each lowering layer is allowed to transform the representation while preserving observable behavior.

This distinction matters when reading generated shader code or benchmarking a backend: source syntax, intermediate operations, and native GPU instructions are three different things.

## The shared lesson with Arm Thumb

Arm Thumb makes the control-flow mechanism unusually tangible because the architecture exposes `r15` as the program counter and instructions such as `B`, `BX`, `BL`, `TBB`, and `TBH` manipulate where execution continues.

GPU shader programming presents a more parallel execution model, but the same graph-level vocabulary survives:

```text
basic block
condition
edge
multi-way selection
loop back-edge
merge/reconvergence
return/termination
```

That is the useful common ground for a compiler: represent control flow explicitly, then let each target lower it according to its actual machine model.

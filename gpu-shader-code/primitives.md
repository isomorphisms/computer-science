# GPU shader code primitives

## Scope

“GPU shader code” is not one standardized language or API. Here **primitive** means the lowest useful *portable semantic* unit shared by modern shader systems. Below this layer, a driver/compiler lowers code to a vendor GPU instruction set, which is hardware-specific and often not the interface an application controls directly. SPIR-V is a more concrete portable intermediate representation than GLSL source, and probably deserves its own folder later.

Primary references: [Khronos SPIR-V Registry](https://registry.khronos.org/SPIR-V/), [SPIR-V unified specification](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html), [GLSL overview](https://docs.vulkan.org/glsl/latest/chapters/overview.html), and [Vulkan shader specification](https://docs.vulkan.org/spec/latest/chapters/shaders.html).

## shader invocation

An invocation is one logical execution of shader code for one vertex, fragment, compute work item, mesh item, ray stage, or other execution-model unit. Many invocations run in parallel, and their relative execution order is generally not something ordinary shader code may assume. See [SPIR-V execution model](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#_entry_point_and_execution_model) and [Vulkan shaders](https://docs.vulkan.org/spec/latest/chapters/shaders.html).

## shader stage / execution model

A shader is compiled for an execution model such as vertex, tessellation control, tessellation evaluation, geometry, fragment, or compute; newer systems also expose task/mesh and ray-tracing stages. The execution model determines what starts an invocation, what built-ins exist, and what inputs and outputs mean. See [SPIR-V Execution Model](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#Execution_Model) and [GLSL overview](https://docs.vulkan.org/glsl/latest/chapters/overview.html).

## entry point

An entry point is the externally selectable function at which a shader stage begins. In SPIR-V, `OpEntryPoint` ties a function to an execution model and declares the interface variables used by that entry point. See [SPIR-V § Entry Point and Execution Model](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#_entry_point_and_execution_model).

## scalar values

Shader arithmetic ultimately operates on scalar values such as booleans, signed and unsigned integers, and floating-point values of supported widths. Exact widths and capabilities depend on the shader environment and device. See [SPIR-V types](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#_types) and [GLSL basic types](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#basic-types).

## vector values

Vectors package a small fixed number of scalar components and are a fundamental GPU data form for positions, colors, texture coordinates, and SIMD-like arithmetic. Most arithmetic and comparison operations are defined componentwise. See [GLSL basic types](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#basic-types).

## matrix values

Matrices are fixed-size collections of floating-point columns used heavily for linear transformations and other numerical work. Shader languages normally provide matrix construction, component access, matrix-vector multiplication, and related operations directly. See [GLSL matrices](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#basic-types).

## composite values

Arrays, structures, and other aggregates let shader code group primitive values into larger typed objects. Portable intermediate representations preserve these logical types even though a final hardware compiler may flatten or register-allocate them differently. See [SPIR-V type declarations](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#_types).

## built-in invocation identifiers

Shaders receive built-in values identifying the current vertex, instance, fragment coordinate, workgroup, local invocation, primitive, sample, and similar execution-specific facts. These are the bridge between one generic program and the particular item it is processing. See [GLSL built-in variables](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#built-in-language-variables).

## stage inputs

Inputs are values supplied to an invocation from the API, a previous pipeline stage, rasterization/interpolation, or the execution environment. In SPIR-V these are commonly variables in the `Input` storage class. See [SPIR-V storage classes](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#Storage_Class).

## stage outputs

Outputs are values written by one stage for later pipeline processing, later shader stages, rasterization, or framebuffer output. In SPIR-V they are commonly variables in the `Output` storage class. See [SPIR-V storage classes](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#Storage_Class).

## uniform / constant data

Uniform or constant data is supplied externally and is normally shared read-only across many invocations. It is how an application passes parameters that do not vary per vertex or fragment, such as transforms, material constants, and configuration values. See [SPIR-V storage classes](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#Storage_Class) and [GLSL storage qualifiers](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#storage-qualifiers).

## private per-invocation storage

Private storage belongs to one invocation, including ordinary local variables and private module-scope state where supported. It is not a communication mechanism between independently executing invocations. See [SPIR-V `Private` and `Function` storage classes](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#Storage_Class).

## workgroup / shared storage

Compute-like execution models expose fast storage shared by invocations in the same workgroup. Correct communication through it requires barriers and the relevant memory semantics; it is not automatically coherent with arbitrary work elsewhere on the device. See [GLSL compute processor overview](https://docs.vulkan.org/glsl/latest/chapters/overview.html#compute-processor) and [SPIR-V storage classes](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#Storage_Class).

## storage buffers

Storage buffers expose application-managed memory to shaders for structured or unstructured reads and writes. They are a central primitive for GPU computation because they let shader invocations exchange large data sets through device memory. See [Vulkan resource descriptors](https://docs.vulkan.org/spec/latest/chapters/descriptors.html) and [SPIR-V storage classes](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#Storage_Class).

## sampled images / textures

A sampled image or texture represents multidimensional formatted data intended to be accessed through texture sampling rules. Coordinates, filtering, level-of-detail selection, and format conversion distinguish this from a raw buffer load. See [Vulkan images](https://docs.vulkan.org/spec/latest/chapters/images.html) and [Vulkan descriptors](https://docs.vulkan.org/spec/latest/chapters/descriptors.html).

## storage images

Storage images expose formatted image data for explicit shader reads and writes rather than ordinary filtered sampling. They are useful when the shader itself is producing or modifying image-like data. See [Vulkan resources](https://docs.vulkan.org/spec/latest/chapters/resources.html) and [Vulkan descriptors](https://docs.vulkan.org/spec/latest/chapters/descriptors.html).

## samplers

A sampler carries the state governing how coordinates are converted into sampled image values: filtering, addressing/wrapping, mipmapping, comparison behavior, and related rules. Some APIs/languages combine image and sampler concepts; others expose them separately. See [Vulkan samplers](https://docs.vulkan.org/spec/latest/chapters/samplers.html).

## loads and stores

Loads read values from declared memory and stores write values back. In SPIR-V the basic memory operations are explicit (`OpLoad`, `OpStore`, and pointer/access-chain operations), making load/store one of the clearest portable lower-level shader primitives. See [SPIR-V memory instructions](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#_memory_instructions).

## arithmetic

Shaders provide arithmetic on supported scalar, vector, and matrix values: addition, subtraction, multiplication, division, remainder/modulus forms, fused or extended operations where available, and many mathematical built-ins. These operations are lowered by the compiler to whatever native instructions the GPU provides. See [SPIR-V instructions](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html) and [GLSL operators](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#operators).

## bit operations

Integer shifts, bitwise AND/OR/XOR/NOT, bit extraction/insertion, bit counts, and related operations are portable shader building blocks. They matter for packing, masks, hashing, indexing, and data-oriented GPU algorithms. See [GLSL integer functions](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#integer-functions).

## comparisons and selection

Comparisons produce boolean conditions, while selection chooses values or control-flow paths from those conditions. At a lower IR level this includes comparison instructions, conditional branches, and select-like operations. See [SPIR-V instruction set](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html) and [GLSL operators](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#operators).

## control flow

Shader programs can branch, loop, merge control-flow paths, and return from functions. The source language may present structured `if`, `switch`, `for`, and `while`, while SPIR-V represents functions as basic blocks and control-flow graphs with structured-control metadata. See [SPIR-V functions and control flow](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#_functions) and [GLSL statements](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#statements-and-structure).

## function calls

Functions package computation behind typed parameters and return values. Compilers may inline them completely, so a source-level call is a semantic primitive rather than necessarily a physical GPU call instruction. See [GLSL functions](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#function-definitions) and [SPIR-V functions](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#_functions).

## interpolation

In raster graphics, values produced at vertices can be interpolated across a primitive to produce per-fragment inputs. Qualifiers select smooth perspective-correct interpolation, non-perspective interpolation, flat values, centroid/sample behavior, and related rules. See [GLSL interpolation qualifiers](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#interpolation-qualifiers).

## derivatives

Fragment processing commonly exposes derivatives such as rates of change across neighboring fragment invocations. These are fundamental to implicit texture level-of-detail selection and many screen-space effects, but their validity depends on the execution model and control flow. See [SPIR-V derivatives](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#_derivatives) and [GLSL derivative functions](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#derivative-functions).

## texture sampling

Texture sampling combines coordinates, an image, sampler state, and often implicit or explicit level-of-detail information to produce filtered texel values. It is a semantically rich primitive because dedicated texture hardware may perform addressing, filtering, format conversion, and anisotropic sampling. See [Vulkan image operations](https://docs.vulkan.org/spec/latest/chapters/images.html) and [GLSL texture functions](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#texture-functions).

## image load / store

Image load/store accesses image texels explicitly, generally without the filtering semantics of ordinary texture sampling. It is the image-shaped counterpart to storage-buffer memory access. See [Vulkan images](https://docs.vulkan.org/spec/latest/chapters/images.html) and [GLSL image functions](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#image-functions).

## atomics

Atomic operations perform read-modify-write updates that cannot be torn by competing invocations. They are the basic primitive for counters, locks, work queues, reductions, and other cross-invocation coordination, although they do not by themselves replace the larger memory-ordering model. See [GLSL atomic memory functions](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#atomic-memory-functions) and [SPIR-V atomic instructions](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#_atomic_instructions).

## workgroup barriers

A workgroup barrier coordinates invocations within a workgroup so that execution reaches a defined synchronization point. It is normally paired with the appropriate memory semantics when shared data must become visible across invocations. See [GLSL compute overview](https://docs.vulkan.org/glsl/latest/chapters/overview.html#compute-processor) and [SPIR-V barrier instructions](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#_barrier_instructions).

## memory barriers and memory ordering

Memory barriers constrain when writes become available and visible to other operations and which operations may be reordered. Modern explicit APIs make this especially important: execution order alone does not automatically imply the memory visibility an algorithm needs. See [SPIR-V memory model](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#_memory_model) and [Vulkan synchronization](https://docs.vulkan.org/spec/latest/chapters/synchronization.html).

## subgroup operations

A subgroup is a device-defined set of invocations that can cooperate more tightly than a whole workgroup, using ballots, shuffles, votes, reductions, scans, and related operations. This is the portable concept corresponding roughly to vendor terms such as a warp or wave, without assuming a particular physical width. See [SPIR-V subgroup instructions](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#_group_and_subgroup_instructions).

## fragment discard / termination

Fragment shaders can terminate an invocation so that it does not contribute normal framebuffer outputs. The exact operation and side-effect rules depend on the language and environment; GLSL exposes `discard`, while lower representations distinguish several termination forms. See [GLSL jump statements](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#jumps) and [SPIR-V termination instructions](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html).

## geometry emission

Geometry-capable stages can emit vertices and end primitives, turning one input primitive or workgroup into zero or more output primitives. This is not present in every shader stage, but where available it is a direct programmable primitive for producing geometry. See [Vulkan geometry shading](https://docs.vulkan.org/spec/latest/chapters/geometry.html).

## raster outputs

Fragment-stage code can produce color outputs and may control depth or sample-related outputs where supported. Those values then feed fixed-function per-sample operations such as depth/stencil testing and blending rather than directly becoming pixels with no further processing. See [OpenGL rendering pipeline](https://wikis.khronos.org/opengl/Rendering_Pipeline_Overview) and [GLSL fragment processor overview](https://docs.vulkan.org/glsl/latest/chapters/overview.html#fragment-processor).

## compute workgroups and dispatch

Compute shaders are launched as a grid of workgroups, each containing a configured number of local invocations. The application chooses the dispatch dimensions; shader built-ins identify the workgroup and invocation, and shared memory/barriers provide workgroup-local cooperation. See [GLSL compute processor overview](https://docs.vulkan.org/glsl/latest/chapters/overview.html#compute-processor).

## specialized execution models (task / mesh / ray tracing)

Modern GPU APIs add execution models beyond the classic graphics and compute stages. Task/mesh shaders generate groups of geometry cooperatively, while ray-tracing pipelines add ray-generation, intersection, any-hit, closest-hit, miss, and callable behavior around acceleration-structure traversal. These are real portable shader primitives when the corresponding API/device capabilities are enabled, but are not universal across all GPUs. See [Vulkan mesh shading](https://docs.vulkan.org/spec/latest/chapters/VK_NV_mesh_shader/mesh.html), [Vulkan ray tracing](https://docs.vulkan.org/spec/latest/chapters/raytracing.html), and [SPIR-V execution models](https://registry.khronos.org/SPIR-V/specs/unified1/SPIRV.html#Execution_Model).

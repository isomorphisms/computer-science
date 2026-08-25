# GLSL primitives

## Scope

This file treats the current desktop OpenGL Shading Language, GLSL 4.60, as a source-language layer. GLSL is not the final GPU machine instruction set: OpenGL implementations compile/link it, and Vulkan commonly consumes SPIR-V produced from GLSL or another source language. The list below therefore names the language and shader-execution primitives a programmer directly controls rather than every standard-library overload.

Primary references: [GLSL 4.60 specification](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html), [Khronos OpenGL registry](https://registry.khronos.org/OpenGL/index_gl.php), and [Khronos OpenGL/GLSL reference pages](https://registry.khronos.org/OpenGL-Refpages/index.php).

## compilation unit

A GLSL compilation unit is source text compiled for one shader stage. Declarations must obey GLSL scope and ordering rules, and linked shader stages must present mutually compatible interfaces. Source: [GLSL 4.60 specification](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html).

## preprocessor

GLSL has a C-like preprocessing phase with object-like and function-like macros, conditional compilation, line control, and predefined macros. It is deliberately smaller than the C preprocessor and runs before GLSL parsing proper. Source: [GLSL preprocessor](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#preprocessor).

## version and extension directives

`#version` selects the language version/profile, while `#extension` controls language behavior exposed through extensions. These directives determine which syntax and semantics the compiler is allowed to accept. Source: [GLSL preprocessor directives](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#preprocessor).

## identifiers and declarations

Programs introduce typed variables, functions, structures, blocks, and other named entities through declarations. GLSL is statically typed and requires declarations before use subject to its scope rules. Source: [GLSL variables and types](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#variables-and-types).

## scalar types

Core scalar types include `bool`, signed and unsigned integers, floating-point values, and `void` for no return value; supported width variants depend on the language feature/capability. Scalars are the elements from which vectors, matrices, and composites are built. Source: [GLSL basic types](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#basic-types).

## vector types

`vec*`, `ivec*`, `uvec*`, `bvec*`, and double-precision vector families hold two to four scalar components. Component selection/swizzling and componentwise operators make vectors a first-class language primitive. Source: [GLSL basic types](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#basic-types).

## matrix types

`mat*` and `dmat*` types represent fixed-size floating-point matrices, with square and non-square forms. GLSL defines matrix indexing, constructors, arithmetic, and matrix/vector multiplication directly. Source: [GLSL basic types](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#basic-types).

## opaque types

Opaque types represent externally managed resources rather than ordinary values: samplers, images, and atomic counters are the major core families. Their contents cannot be copied around like ordinary structs; operations occur through dedicated built-ins and API bindings. Source: [GLSL opaque types](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#opaque-types).

## arrays

Arrays provide fixed or runtime-sized sequences where the storage/interface rules permit them. They are used for ordinary local data, shader interfaces, buffers, textures/images, and stage-specific inputs/outputs. Source: [GLSL arrays](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#arrays).

## structures

`struct` combines existing types into a named or anonymous aggregate with member selection. Structures are ordinary typed composites and can appear inside arrays and interface-storage declarations subject to GLSL restrictions. Source: [GLSL structures](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#structures).

## interface blocks

Interface blocks group related input, output, uniform, or buffer-backed variables under a common layout/interface declaration. They are the structured bridge between shader code and API-visible buffer or stage interfaces. Source: [GLSL interface blocks](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#interface-blocks).

## storage qualifiers

Storage qualifiers describe where a variable comes from and who can see it: important forms include `const`, `in`, `out`, `uniform`, `buffer`, and `shared`. Their legal meanings depend on shader stage and scope. Source: [GLSL storage qualifiers](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#storage-qualifiers).

## interpolation qualifiers

`smooth`, `flat`, and `noperspective` control how vertex-produced values become fragment inputs across a rasterized primitive. They specify interpolation semantics rather than merely optimization hints. Source: [GLSL interpolation qualifiers](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#interpolation-qualifiers).

## auxiliary and memory qualifiers

Qualifiers such as `centroid`, `sample`, `patch`, `coherent`, `volatile`, `restrict`, `readonly`, and `writeonly` refine interpolation, tessellation interfaces, and memory-access semantics. These matter because they can change both what accesses are legal and what visibility/aliasing assumptions the implementation may make. Source: [GLSL qualifiers](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#type-qualifiers).

## layout qualifiers

`layout(...)` attaches explicit interface facts such as locations, component/index assignments, descriptor-style bindings where supported, block memory layout, local workgroup size, tessellation modes, geometry input/output modes, and fragment-output properties. It is the main syntax for making otherwise implicit pipeline agreements explicit. Source: [GLSL layout qualifiers](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#layout-qualifiers).

## precision, invariant, and precise qualifiers

Precision-related declarations govern numerical requirements where the profile uses them, while `invariant` constrains cross-program reproducibility of selected outputs and `precise` restricts transformations that could change expression results. These are semantic controls on numerical behavior rather than new value types. Source: [GLSL type qualifiers](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#type-qualifiers).

## constants and constant expressions

Literal values, `const` objects, and expressions meeting the specification's constant-expression rules can be evaluated as compile-time values and used where GLSL requires compile-time determinacy. Source: [GLSL constants](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#constants).

## constructors and conversions

Type constructors create scalar, vector, matrix, array, and structure values from component expressions. GLSL permits a defined set of implicit conversions and many explicit constructor-based conversions, but remains strongly typed compared with C-style implicit coercion. Source: [GLSL conversions and constructors](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#conversions-and-constructors).

## operators and expressions

GLSL expressions combine values using arithmetic, logical, bitwise, relational, assignment, indexing, member-selection, and conditional operators. The operator set is defined over scalar/vector/matrix types with specific overload and conversion rules. Source: [GLSL operators](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#operators).

## functions and overloading

Functions have typed parameters and return types; GLSL supports overloading by parameter types and parameter qualifiers such as `in`, `out`, and `inout`. Recursion is not part of the ordinary GLSL execution model. Source: [GLSL functions](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#functions).

## selection statements

`if`/`else` and `switch` choose which statements execute based on runtime values. On parallel hardware, different invocations may follow different branches, but the language defines the program semantics independently of how the implementation schedules those paths. Source: [GLSL selection](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#selection).

## loop statements

`for`, `while`, and `do` express repeated execution. Compilers may unroll or otherwise transform loops, but those transformations must preserve GLSL-visible behavior. Source: [GLSL iteration](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#iteration).

## jump statements

`continue`, `break`, `return`, and fragment-stage `discard` alter normal structured execution. Their availability and effect depend on context and shader stage. Source: [GLSL jumps](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#jumps).

## shader stage

Core desktop GLSL 4.60 defines source semantics for vertex, tessellation-control, tessellation-evaluation, geometry, fragment, and compute processors. A compilation unit targets one of these stage environments, which determines its legal built-ins and interfaces. Source: [GLSL overview of shading](https://docs.vulkan.org/glsl/latest/chapters/overview.html).

## stage input and output variables

`in` and `out` variables form the programmable interface between application-fed data, successive shader stages, rasterization, and framebuffer output. Linking and layout rules determine how outputs from one stage match inputs to another. Source: [GLSL shader interfaces](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#shader-interfaces).

## built-in variables

Each shader stage receives or must produce certain predefined variables, for example vertex/instance IDs, positions, tessellation levels, primitive IDs, fragment coordinates, sample information, and compute invocation IDs. These are language-level names for pipeline facts supplied by the execution environment. Source: [GLSL built-in variables](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#built-in-language-variables).

## built-in functions

The standard library includes mathematical, geometric, matrix, relational, packing/unpacking, bit-manipulation, and stage-specific functions. They are intrinsic language facilities even when the implementation lowers them to multiple machine instructions. Source: [GLSL built-in functions](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#built-in-functions).

## texture functions

Texture built-ins perform sampling, projected sampling, explicit/implicit level-of-detail access, gradients, offsets, gathers, size/level queries, and direct texel fetching depending on sampler type. They expose the texture-sampling hardware model rather than ordinary memory reads. Source: [GLSL texture functions](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#texture-functions).

## image functions

Image built-ins query dimensions and explicitly load, store, or atomically update image texels. Unlike normal texture sampling, image access is storage-like and participates in explicit shader memory-synchronization rules. Source: [GLSL image functions](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#image-functions).

## atomic operations

GLSL exposes atomic counter operations and atomic memory functions for shared variables, buffer-backed data, and images where allowed. These provide indivisible read-modify-write operations for coordination between invocations. Source: [GLSL atomic functions](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#atomic-counter-functions).

## derivative and interpolation functions

Fragment shaders can compute derivatives and explicitly request interpolation at centroid/sample/offset positions. These operations depend on the rasterization execution model and neighboring fragment invocations rather than ordinary scalar function evaluation. Source: [GLSL derivative functions](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#derivative-functions).

## memory barrier functions

Barrier built-ins constrain execution and/or memory visibility for shared variables, buffer variables, images, atomic counters, and related resources. They are the shader-side primitive for expressing dependencies that parallel execution does not provide automatically. Source: [GLSL shader memory control functions](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#shader-memory-control-functions).

## compute workgroups and shared variables

A compute shader executes in workgroups whose local size is declared by layout qualifiers; built-ins identify local/global invocation positions. `shared` variables provide workgroup-local communication when paired with appropriate barriers. Source: [GLSL compute processor](https://docs.vulkan.org/glsl/latest/chapters/overview.html#compute-processor).

## geometry emission

Geometry shaders can call stage-specific built-ins such as `EmitVertex` and `EndPrimitive` to construct zero or more output primitives from an input primitive. This is direct control over primitive generation inside the classic graphics pipeline. Source: [GLSL geometry-shader functions](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#geometry-shader-functions).

## fragment discard

`discard` terminates the current fragment-shader invocation so that it produces no normal fragment outputs. It is a control-flow primitive specific to fragment processing, with interactions with derivatives and side effects that make placement significant. Source: [GLSL jump statements](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html#jumps).

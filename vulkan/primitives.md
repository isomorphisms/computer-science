# Vulkan primitives

## Scope

This list treats current Vulkan as an explicit device-control API and groups it into conceptual primitives rather than every `vk*` command, structure, flag, or extension. Vulkan exposes many things that OpenGL keeps implicit: device/queue selection, memory allocation/binding, command recording, synchronization scopes, image layouts, and resource ownership. Core Vulkan is the baseline; extension-dependent families such as mesh shading and ray tracing are labeled as such.

Primary references: [Vulkan specification](https://docs.vulkan.org/spec/latest/), [fundamentals](https://docs.vulkan.org/spec/latest/chapters/fundamentals.html), [devices and queues](https://docs.vulkan.org/spec/latest/chapters/devsandqueues.html), [resources](https://docs.vulkan.org/spec/latest/chapters/resources.html), [command buffers](https://docs.vulkan.org/spec/latest/chapters/cmdbuffers.html), and [synchronization](https://docs.vulkan.org/spec/latest/chapters/synchronization.html).

## instance

`VkInstance` is the application’s connection to the Vulkan implementation. It establishes instance-level extensions/layers and is the parent scope from which physical devices and instance-level functionality are discovered. Source: [Vulkan initialization](https://docs.vulkan.org/spec/latest/chapters/initialization.html).

## layers and extensions

Layers can intercept/augment Vulkan behavior, most notably for validation and tooling, while extensions add optional API capabilities beyond a core version. Applications enumerate support and explicitly enable the instance/device extensions they intend to use. Source: [Vulkan extensions](https://docs.vulkan.org/spec/latest/chapters/extensions.html).

## physical device

`VkPhysicalDevice` represents an available Vulkan implementation/device with reported properties, limits, features, memory types/heaps, queue families, and supported formats/extensions. Applications inspect these capabilities before creating a logical device. Source: [Devices and Queues](https://docs.vulkan.org/spec/latest/chapters/devsandqueues.html).

## logical device

`VkDevice` is the application-created logical connection to a chosen physical device, with a selected set of features/extensions and queues. Most resource creation and command execution occurs under this device. Source: [Devices and Queues](https://docs.vulkan.org/spec/latest/chapters/devsandqueues.html).

## queue families and queues

Queue families describe groups of queues with capabilities such as graphics, compute, transfer, sparse binding, video, and presentation support. `VkQueue` objects are asynchronous submission endpoints; different queues have almost no automatic ordering with each other, so dependencies must be expressed explicitly. Sources: [Devices and Queues](https://docs.vulkan.org/spec/latest/chapters/devsandqueues.html) and [Fundamentals](https://docs.vulkan.org/spec/latest/chapters/fundamentals.html).

## device memory

`VkDeviceMemory` is explicitly allocated from a device memory type and then bound to resources such as buffers/images. Vulkan separates resource creation from backing-memory allocation, allowing suballocation and application control over host visibility, coherence, locality, and memory budgeting. Source: [Memory Allocation](https://docs.vulkan.org/spec/latest/chapters/memory.html).

## buffers

`VkBuffer` is a linear byte-addressed resource used for vertex/index data, uniforms, storage, transfers, indirect commands, device-addressed data, acceleration-structure backing, and other roles selected by usage flags. A non-sparse buffer generally must be bound to memory before device use. Source: [Resource Creation](https://docs.vulkan.org/spec/latest/chapters/resources.html).

## buffer views

`VkBufferView` gives a contiguous buffer range a texel format so shaders can access the data through texel-buffer/image-style operations. It is a typed view layered on the underlying buffer resource. Source: [Resource Creation — Buffer Views](https://docs.vulkan.org/spec/latest/chapters/resources.html#resources-buffer-views).

## images

`VkImage` is a multidimensional formatted resource with mip levels, array layers, samples, usage flags, and implementation-dependent internal layout. Images back textures, storage images, rendering attachments, transfer targets/sources, and presentation images. Source: [Images](https://docs.vulkan.org/spec/latest/chapters/images.html).

## image views

`VkImageView` selects a compatible subset of an image’s subresources and gives it a view type, format interpretation, and component mapping. Shaders and render attachments generally use image views rather than raw image handles. Source: [Resource Creation — Image Views](https://docs.vulkan.org/spec/latest/chapters/resources.html#resources-image-views).

## samplers

`VkSampler` stores image-sampling behavior such as filtering, address modes, mipmap selection, LOD limits, anisotropy, and comparison sampling. It is independent from image storage and can be combined with sampled image descriptors. Source: [Samplers](https://docs.vulkan.org/spec/latest/chapters/samplers.html).

## descriptors

A descriptor is opaque binding data that makes buffers, image views, samplers, acceleration structures, and other resources visible to shaders. The shader interface identifies descriptors by bindings/sets or newer descriptor mechanisms. Source: [Resource Descriptors](https://docs.vulkan.org/spec/latest/chapters/descriptors.html).

## descriptor set layouts

`VkDescriptorSetLayout` declares the bindings in a descriptor set: descriptor types, array counts, and which shader stages may access them. It is part of the contract between pipeline layout, shader resource interface, and actual bound descriptor data. Source: [Descriptor Sets](https://docs.vulkan.org/spec/latest/chapters/descriptorsets.html).

## descriptor pools and descriptor sets

Descriptor pools allocate descriptor sets; descriptor sets hold concrete resource descriptors matching a set layout and are bound into command buffers for shader access. Updating descriptor contents is separate from binding the set for execution. Source: [Descriptor Sets](https://docs.vulkan.org/spec/latest/chapters/descriptorsets.html).

## pipeline layouts and push constants

A pipeline layout describes the descriptor-set layouts and push-constant ranges used by a pipeline. Push constants provide a small block of command-recorded values that can be changed cheaply without allocating/updating descriptor-backed memory. Sources: [Pipeline Layouts](https://docs.vulkan.org/spec/latest/chapters/descriptorsets.html#descriptorsets-pipelinelayout) and [Push Constants](https://docs.vulkan.org/guide/latest/push_constants.html).

## shader modules and SPIR-V

Vulkan shaders are defined in SPIR-V subject to Vulkan’s SPIR-V environment rules; `VkShaderModule` packages SPIR-V code for pipeline creation in the traditional model. Vulkan 1.4 requires support for SPIR-V through version 1.6, though individual capabilities still depend on enabled Vulkan features/extensions. Sources: [Vulkan Environment for SPIR-V](https://docs.vulkan.org/spec/latest/appendices/spirvenv.html) and [SPIR-V Registry](https://registry.khronos.org/SPIR-V/).

## pipelines

`VkPipeline` represents compiled graphics, compute, or ray-tracing pipeline state. Graphics pipelines combine shader stages with most fixed-function state, while dynamic-state features allow selected state to be supplied later in command buffers. Source: [Pipelines](https://docs.vulkan.org/spec/latest/chapters/pipelines.html).

## pipeline cache

Pipeline caches store implementation-managed data that can accelerate later pipeline creation and can be serialized/reused under the specification’s compatibility rules. They are a performance primitive rather than a rendering-semantic requirement. Source: [Pipeline Cache](https://docs.vulkan.org/spec/latest/chapters/pipelines.html#pipelines-cache).

## command pools

A `VkCommandPool` is an allocation/reset arena for command buffers associated with a queue family. Pool ownership and host synchronization matter because recording/reset operations mutate pool-managed storage. Source: [Command Pools](https://docs.vulkan.org/spec/latest/chapters/cmdbuffers.html#commandbuffers-pools).

## command buffers

`VkCommandBuffer` records state-setting commands, draw/dispatch operations, transfers, barriers, queries, and other device work for later queue submission. Primary command buffers can be submitted to queues and can execute secondary command buffers. Source: [Command Buffers](https://docs.vulkan.org/spec/latest/chapters/cmdbuffers.html).

## dynamic state

Dynamic state is pipeline state intentionally omitted from the immutable/compiled portion of a graphics pipeline and instead set by commands in a command buffer. Which state can be dynamic depends on core version/features/extensions. Source: [Pipelines](https://docs.vulkan.org/spec/latest/chapters/pipelines.html).

## primitive topology

Input assembly specifies how vertex indices are assembled into graphics primitives: point lists, line lists/strips, triangle lists/strips/fans, adjacency variants, and patch lists, subject to device/features and pipeline configuration. These are the literal geometric primitives fed into the classic graphics pipeline. Source: [Drawing](https://docs.vulkan.org/spec/latest/chapters/drawing.html).

## draw commands

`vkCmdDraw*` commands record graphics work using the currently bound graphics pipeline, vertex/index buffers, descriptors, and dynamic state. Variants support indexed, indirect, multi-draw, and instanced work, keeping launch parameters in command buffers or GPU-readable buffers. Source: [Drawing](https://docs.vulkan.org/spec/latest/chapters/drawing.html).

## compute dispatch

`vkCmdDispatch*` records compute workgroups for execution with a bound compute pipeline and resources. Dispatch dimensions define the workgroup grid; the shader defines the local workgroup size. Source: [Dispatching Commands](https://docs.vulkan.org/spec/latest/chapters/dispatch.html).

## transfer, clear, blit, and resolve commands

Vulkan records explicit commands for buffer/image copies, fills/updates, image clears, filtered blits, and multisample resolves. These are first-class device operations with their own supported queue types, pipeline stages, access types, and layout requirements. Source: [Copies and Blits](https://docs.vulkan.org/spec/latest/chapters/copies.html).

## dynamic rendering

Dynamic rendering begins/ends rendering directly from attachment descriptions recorded in the command buffer, without requiring a pre-created render-pass/framebuffer object for that rendering instance. It is the modern simpler core rendering model for many uses. Source: [Render Pass — Dynamic Rendering](https://docs.vulkan.org/spec/latest/chapters/renderpass.html#renderpass-dynamicrendering).

## render passes, subpasses, and framebuffers

The traditional rendering model uses `VkRenderPass` to describe attachment lifetimes/layouts and subpass relationships, and `VkFramebuffer` to supply concrete attachment image views. It can encode efficient on-chip attachment use and synchronization relationships; dynamic rendering removes the need for these objects in many cases but does not erase the concepts. Source: [Render Pass](https://docs.vulkan.org/spec/latest/chapters/renderpass.html).

## pipeline stages and access scopes

Synchronization names logical pipeline stages and memory access types so dependencies can be scoped to the work that actually needs ordering. These stage/access masks are not merely performance hints: they define synchronization scopes in Vulkan’s execution and memory dependency model. Source: [Synchronization and Cache Control](https://docs.vulkan.org/spec/latest/chapters/synchronization.html).

## fences

`VkFence` communicates completion from submitted device work back to the host. A queue submission can signal a fence, and host code can poll or wait for it before reusing resources or command infrastructure. Source: [Synchronization — Fences](https://docs.vulkan.org/spec/latest/chapters/synchronization.html#synchronization-fences).

## semaphores

Semaphores establish dependencies between queue operations and are the main GPU-to-GPU/queue-to-queue synchronization primitive. Binary semaphores carry signaled/unsignaled state; timeline semaphores expose monotonically increasing payload values for richer dependency graphs. Source: [Synchronization — Semaphores](https://docs.vulkan.org/spec/latest/chapters/synchronization.html#synchronization-semaphores).

## events

Events are fine-grained synchronization primitives that can be set/reset by host/device commands and waited on inside command buffers under defined rules. They split a dependency into a signal side and a later wait side. Source: [Synchronization — Events](https://docs.vulkan.org/spec/latest/chapters/synchronization.html#synchronization-events).

## pipeline and memory barriers

Pipeline-barrier commands insert execution and memory dependencies at a point in a command buffer. Buffer/image memory barriers add resource ranges, access scopes, image layout transitions, and queue-family ownership information to those dependencies. Source: [Synchronization and Cache Control](https://docs.vulkan.org/spec/latest/chapters/synchronization.html).

## image layouts and layout transitions

Each image subresource has a Vulkan layout describing the kinds of accesses for which its current representation is valid. Applications transition layouts explicitly through image barriers or rendering dependencies; the implementation may change the underlying physical organization. Source: [Resource Creation — Image Layouts](https://docs.vulkan.org/spec/latest/chapters/resources.html#resources-image-layouts).

## queue-family ownership transfers

For resources used with exclusive sharing, access can require explicit ownership transfer when moving between queue families. Release/acquire barrier operations describe the handoff alongside the necessary synchronization. Source: [Synchronization — Queue Family Ownership Transfer](https://docs.vulkan.org/spec/latest/chapters/synchronization.html).

## query pools and queries

`VkQueryPool` stores asynchronous query results such as occlusion counts, timestamps, pipeline statistics, and extension-defined measurements. Commands reset, begin/end/write, copy, or retrieve query results without forcing every measurement through immediate host synchronization. Source: [Queries](https://docs.vulkan.org/spec/latest/chapters/queries.html).

## surfaces, swapchains, and presentation

Window-system integration extensions expose a presentation `VkSurfaceKHR`; a `VkSwapchainKHR` manages a set of presentable images that applications acquire, render to, and submit for presentation. Presentation is deliberately outside the device-independent core rendering model and depends on platform/WSI support. Source: [Window System Integration](https://docs.vulkan.org/spec/latest/chapters/VK_KHR_surface/wsi.html).

## sparse resources

Sparse buffers/images decouple virtual resource address space from physical memory binding so selected regions/pages can be resident or aliased. Sparse binding is performed through queue operations and requires advertised sparse capabilities. Source: [Sparse Resources](https://docs.vulkan.org/spec/latest/chapters/sparsemem.html).

## device addresses

Buffer device address features let shaders and some device operations refer to buffer data through GPU virtual addresses rather than only descriptor bindings. This enables pointer-like data structures but introduces stricter lifetime, alignment, and synchronization responsibilities. Source: [Buffer Device Address](https://docs.vulkan.org/guide/latest/buffer_device_address.html).

## external memory and synchronization

Vulkan extensions can export/import memory, semaphore, and fence payloads through platform handles for interoperability with other APIs, processes, or devices. These are the primitives for crossing Vulkan’s ordinary object ownership boundary while preserving explicit synchronization. Source: [Vulkan external memory extensions](https://docs.vulkan.org/spec/latest/chapters/memory.html).

## mesh and task shading

Mesh-shader extensions replace the classic vertex-input/primitive-assembly front end with workgroup-oriented task and mesh stages that directly emit vertices and primitives. Support is optional and extension/feature dependent, but it exposes a distinct modern geometry-generation primitive. Source: [Vulkan Mesh Shading](https://docs.vulkan.org/spec/latest/chapters/VK_NV_mesh_shader/mesh.html).

## acceleration structures and ray tracing

Ray-tracing extensions add acceleration structures, shader binding tables, ray-tracing pipelines, trace-ray commands, and ray-generation/intersection/any-hit/closest-hit/miss/callable stages. Traversal is largely fixed-function while programmable shader stages handle ray behavior and hit processing. Source: [Vulkan Ray Tracing](https://docs.vulkan.org/spec/latest/chapters/raytracing.html).

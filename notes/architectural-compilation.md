# Architectural compilation: programming by goals, choices, and recombination

Status: reconciled design notes. ComputerScience planner/compiler implementation has not started in this repository.

This document deliberately distinguishes three kinds of statement:

- **settled direction**: goals and constraints repeatedly stated for this project;
- **working hypothesis**: a design worth testing, not yet an architectural commitment;
- **open question**: something the implementation experiments must answer.

The catalogs in this repository are inputs to that work. A large body of prose is not, by itself, an architectural compiler or evidence that a choice has been implemented.

## Settled direction

The system should:

- begin with semantic and mathematical intent rather than a preselected library, language, or representation;
- preserve dimension computations and relationships, shapes, ragged structure, algebraic identities, and other semantic facts for as long as possible; "computable" does not mean every dimension is a literal known before execution;
- search among concrete algorithms and platform primitives using explicit assumptions, conservative calculations, measurements, and recorded failures;
- emit an inspectable plan that says what was selected, what was rejected, and why;
- produce small, readable, target-specific programs rather than carrying the design-time catalog into the deployed result;
- favor small programs with explicit inputs and outputs that can terminate and be composed by a shell or by Grease, instead of assuming that every problem wants one long-lived monolithic process.

These are constraints on the work. The exact language for goals, the location of adverbs, and the role of an LLM are not settled.

## The question

How high-level can programming actually be?

A great deal of programming today consists of choosing and stitching together already-known algorithms and implementations. At the top level, however, the programmer is usually forced to commit early to low-level architectural choices: a particular container, storage scheme, concurrency model, parser, decoder, library, numerical representation, and so on.

The proposed separation is:

1. state the goal and the externally meaningful behavior;
2. resolve architectural decisions over a potentially huge space of known algorithms and components;
3. produce an explicit, inspectable implementation plan;
4. only then perform ordinary mechanical compilation to machine code.

The first stage is not ordinary compilation. It is closer to architectural synthesis or elaboration.

## A deliberately expensive upper layer

There is no requirement that the design-time system be small or fast in the same way that the deployed program must be small or fast.

It may be reasonable for the architectural system to have:

- an enormous catalog of algorithms and implementations;
- extensive benchmarks and test corpora;
- machine- and operating-system-specific measurements;
- proofs, contracts, failure cases, and compatibility information;
- many alternative implementations of one semantic operation;
- a large dependency graph of consequences and follow-up choices;
- expensive first-time search, testing, or compilation.

A terabyte-scale architectural knowledge base would not be intrinsically objectionable if it substantially improved program design while ultimately emitting a small program.

This separates **cost of deciding how to build the program** from **cost of the resulting program**.

## Recombination rather than invention

Many components are instances of solved or at least heavily worked-out problems. Examples include sorting, queues, hash tables, parsers, compression, gesture decoding, numerical kernels, database indexes, layout algorithms, and graph algorithms.

The architectural stage should be able to say, approximately:

> I need an operation with these semantics and these constraints. Which known components can supply it, and which combinations actually fit together?

It then recombines existing work rather than requiring the high-level programmer to manually instantiate every design decision.

This does not mean implementations are interchangeable. It means their differences should become data available to the design process.

## A fixed comparison usually has no single winner

There is usually no single best implementation.

Implementations differ in:

- latency;
- throughput;
- memory;
- code size;
- accuracy;
- numerical error;
- determinism;
- energy use;
- parallelism;
- startup cost;
- persistence requirements;
- network use;
- privacy properties;
- target hardware;
- implementation language;
- licenses;
- ease of inspection;
- failure behavior.

After the target, workload, metrics, uncertainty, and hard constraints have been made explicit, the remaining candidates will often form a partial order or Pareto frontier rather than a ranked list. Before that context is fixed, claiming that one catalog entry dominates another is usually meaningless.

A higher-level program can state requirements and preferences without prematurely naming one algorithm.

## "Adverbs"

One working name for implementation-selection modifiers is **adverbs**.

The verb says what operation is wanted. The adverbs describe how it should be realized without themselves naming the realization.

For example:

```text
decodeSwipe gesture
    offline
    small
    low-latency
    low-memory
```

The operation is `decodeSwipe`. `offline`, `small`, `low-latency`, and `low-memory` constrain the implementation space.

Some of these are hard constraints. Some are preferences. Some are aesthetic choices. Some choices reveal new consequential questions.

The name "adverb" is provisional, but the separation is useful. The current leaning is that architectural adverbs belong primarily to ComputerScience's search problem. Idriç/Edric should carry the computable structure that makes those choices possible—dimension expressions and relationships, shapes, raggedness, and algebraic meaning—without becoming the owner of every architectural preference. Some of those facts may become known only after an input is validated or another value is computed.

## Programming as a branching Q&A

At the architectural level, program construction may look less like writing a linear recipe and more like answering a branching series of questions.

Some questions can be answered mechanically:

- this implementation requires a server, but the program is offline;
- this component cannot run on the target architecture;
- the current licensing policy or legal review flags this combination;
- these interfaces do not compose;
- this memory bound cannot be met.

Other questions belong to the programmer:

- 3 MB and 93% accuracy, or 18 MB and 96% accuracy?
- deterministic behavior, or somewhat better average performance?
- a simpler implementation, or a more specialized one?
- optimize startup, steady-state throughput, battery use, or binary size?

A design choice can expose another branch of questions. The system should surface the implications rather than silently make consequential choices.

Whether an LLM should be an advisor, question-asker, catalog researcher, candidate generator, critic, or something else remains open. Any LLM contribution must be recorded as a proposal or explanation, not silently promoted to fact. Measurements, derivations, contracts, and the selected plan must remain inspectable and replayable without trusting the model's memory. Consulting another model, memoizing paper calculations, and eventually bootstrapping more of the design process are experiments, not settled architecture.

## Ordinary compilation remains ordinary

Once architectural choices have been resolved, the lower stage should become comparatively mechanical and reproducible:

```text
goal
  -> architectural search / elaboration
  -> explicit selected components and contracts
  -> mechanically checkable program
  -> conventional compiler
  -> machine code
```

The expensive, uncertain, conversational part should not need to happen on every rebuild. The selected architecture can be committed and reproduced.

"Mechanical" does not mean trivial. ABI boundaries, linkers, build systems, drivers, packaging, and deployment still exist. The point is that they should not silently reopen already settled architectural questions.

## Small programs and composition

The default unit should be a program that does one intelligible job, exposes its data boundary, and can finish. A shell or Grease can connect such programs. Long-lived services and fused processes remain possible when measurements justify them, but they are implementation choices rather than the assumed shape of the system.

This changes the architectural search problem. The planner must be able to compare not only algorithms inside a process, but also boundaries between programs: serialization, copying, startup time, persistence, streaming, failure isolation, and opportunities to fuse stages. Composition should stay visible even when a particular target eventually benefits from fusion.

## STL as an incomplete precursor

The C++ Standard Template Library is relevant as an early approximation of part of this idea.

STL separates generic algorithms from data structures and gives programmers reusable implementations instead of forcing them to reimplement every solved problem. But it still leaves much of the architectural selection burden on the programmer.

The programmer is still expected to decide whether the problem wants a vector, deque, list, map, unordered map, and so forth, often before having a good reason to know. Generic programming makes the selected pieces reusable, but does not solve the higher-level question:

> Given my actual goal and constraints, which representation and algorithm should I choose?

An architectural compiler would move some of that selection burden into an explicit design-space search.

This also suggests that awkward template machinery can be evidence of an abstraction boundary being expressed at the wrong language level: useful genericity is being recovered through a complicated secondary mechanism rather than represented directly in the language and toolchain.

## An algorithm is not its paper pseudocode

A catalog cannot pretend that a named algorithm completely specifies an implementation.

There may be several layers:

```text
mathematical / conceptual idea
    -> paper pseudocode
    -> concrete algorithmic decisions
    -> data representation
    -> implementation language
    -> runtime and operating-system assumptions
    -> compiler decisions
    -> hardware behavior
```

Someone who implements a paper in C or C++ necessarily reifies many things the pseudocode left unspecified. Those choices can change performance dramatically and can sometimes undermine claims made at the abstract level.

Therefore the architectural system should preserve provenance and assumptions rather than flattening everything into a package name such as `shark2`.

## Keep the semantic layer above disposable target languages

C is not the universal intermediate representation for this project. It is one optional disposable terminal output for a CPU lowering—for example, RefC-generated C handed to Android NDK/Clang. It is not required, it does not define the shared CPU/GPU contract, and it must not mediate the GPU route. Lowering through C before architectural selection would erase the shapes, dimension relations, maps, reductions, padding identities, and algebraic equivalences that the architectural stage needs.

A working division of responsibility is:

```text
Idriç / Edric
  -> preserve semantic intent, computable sizes and shapes, raggedness, and algebra
ComputerScience
  -> compare alternatives and emit a shared typed plan with evidence
selected typed plan
  -> target-specific lowering
     -> direct CPU code or optional terminal C -> native toolchain
     -> typed shader IR -> GLSL ES -> Android GPU driver
```

The exact boundary is still experimental, but semantic information must not disappear merely because a familiar backend language is convenient. Conversely, a target restriction such as the current GLSL backend's fixed shader arrays is evidence for a lowering decision, not a reason to erase ragged semantics from the source.

## First vertical slice: SURFER

SURFER is the first concrete end-to-end test and the initial single-user research environment. It exposes mathematical intent, variable polynomial sizes, vector operations, and meaningful CPU/GPU choices without requiring the planner to solve every domain at once.

There are already two distinct kinds of implementation evidence outside this repository. The Algebraic Variety Explorer mobile app is a working CPU-only Java renderer built from Christian Stussak's jsurf code; it is the behavioral and visual oracle. The Idris-to-GLSL ES repository contains a real restricted compiler backend and a bounded SURFER-style root-search capability test; that test is neither a complete renderer nor a robust root isolator. Neither is an implemented ComputerScience planner.

The slice needs one shared typed rendering contract above the CPU/GPU split. The first CPU evidence route may use RefC-generated C followed by Android NDK/Clang and an ARMv7 native library. On that route C is disposable terminal output, not the shared IR. Direct Thumb-2/NEON emission remains a later research candidate, and the installed ABI and usable features must be measured rather than inferred from a processor name. The GPU route should preserve the shared plan into a typed shader representation and lower it to GLSL ES. CPU host code may feed assets, uniforms, and frame state to the GPU without forcing shader computation through C.

The fixed-array restriction in the existing shader backend is a target fact. Ragged semantic data may later lower through offsets, padding with an identity, specialization, multiple kernels, or rejection; it should not be silently redescribed as fixed merely to fit that backend.

Christian Stussak's nearest-opaque Java renderer is an oracle, not the desired architecture. Homotopy-specific S/T behavior belongs in the Homotopy layer rather than being baked into a generic renderer.

Acceptance is not "we wrote catalog notes." It requires executable CPU and GPU paths from a small semantic input through a shared typed plan, comparison of hit/miss, first-hit distance, normals, and images against the oracle within stated tolerances, target-specific measurements, and a reproducible record of selected and rejected choices. A first CPU milestone may precede GPU integration, but the vertical slice is not complete until both paths execute and can be checked.

## SHARK² / swipe decoding as a candidate example

A phone keyboard may provide another manageable example. The candidate families below are a working survey, not a settled decision that SHARK² or any other family is the correct decoder.

High-level intent might be:

```text
phone keyboard
ordinary text entry
good swipe typing
offline
small installed size
low latency
math/programming symbol layers
```

Swipe decoding could then have several candidate families:

- SHARK²-style geometric decoding;
- learned spatial models;
- trie-constrained beam search;
- language-model-heavy approaches;
- hybrids.

The high-level keyboard designer should not necessarily have to choose one immediately. The architectural system should compare compatible implementations and surface the tradeoffs that materially affect the requested keyboard.

The selected decoder can later be compiled as ordinary code. The giant catalog and all rejected alternatives do not belong in the phone APK.

## Beware abstract algorithms whose assumptions fail on real machines

A major reason this upper layer needs empirical information is that an elegant decomposition in an algorithm or programming-language model may not correspond to good execution on real hardware.

Examples of hidden assumptions include:

- memory access being treated as uniform-cost;
- recursion or divide-and-conquer creating poor locality or allocation behavior;
- parallel pieces being assumed independent when synchronization and movement dominate;
- elegant immutable representations creating allocation and cache costs;
- asymptotic analyses hiding the actual range of problem sizes;
- pseudocode ignoring representation, copying, boxing, pointer chasing, branch prediction, SIMD, GPU transfer, or scheduler overhead.

This is relevant to long-running claims that functional decomposition would expose "free" parallelism. Decomposability in the source language does not itself establish that parallel execution will be profitable on a particular machine.

A high-level language should therefore be able to remain high-level **without becoming hardware-indifferent**. Architectural selection must eventually connect abstract semantics to measured or proven properties of concrete implementations on concrete targets.

There is a remembered Haskell talk by Edward Kmett, and a separate Lambda Days talk, touching related failures of seemingly clean divide-and-conquer / free-parallelism arguments. Exact references should be recovered before turning these recollections into citations.

## Contracts need to be richer than ordinary type signatures

To compose independently developed algorithms mechanically, the catalog probably needs contracts for more than input and output types.

Potential contract dimensions include:

- semantic behavior;
- effects;
- ownership and lifetime;
- allocation behavior;
- concurrency assumptions;
- numerical precision and error bounds;
- latency and throughput models;
- memory bounds;
- determinism;
- persistence;
- network access;
- failure modes;
- target capabilities;
- approximation / quality measures.

This is partly a programming-language problem and partly an empirical software-engineering problem.

Calculations should be conservative where the inputs are uncertain: preserve ranges or intervals, state assumptions, and avoid laundering estimates into exact-looking scores. Raw benchmark observations and failures should remain attached to the target, ABI, workload, implementation revision, compiler/driver versions, and active adverbs. Derived summaries can be regenerated; discarded observations cannot.

## Implementation status and experiments

This repository contains catalogs and prose schemas, not an executable component model, observation store, verifier, planner, constraint solver, autotuner, or end-to-end ComputerScience compiler. Those catalogs were capture and reference work; their size is not evidence that planner implementation has begun.

Implemented neighboring work should be named precisely rather than absorbed into that claim: Idriç is a real compiler line, the separate Idris-to-GLSL ES backend is real target-lowering work, and the Java SURFER app is a real CPU renderer/oracle. The next step is to connect and measure the smallest shared SURFER CPU/GPU slice and write one manual selection trace. Only then is there enough concrete evidence to implement the smallest useful planner/chooser.

SURFER remains the first intended vertical slice. IB/eyebrowser is the committed second slice and belongs on a separate development branch: it tests process composition, renderer selection, data movement, durable/cache separation, and resource policy rather than numerical CPU/GPU lowering. Its first concrete trace should connect the existing network-free Android prepaint viewer to the ICU search/fetch handoff and atomically paint the returned Idriç projection. Field Mouse remains exploratory until its inputs, outputs, and acceptance criteria are written down.

## Open design questions

- What is the representation of a high-level goal before implementation choices are resolved?
- Which "adverbs" belong in the language, which belong in project policy, and which belong only in the conversational design layer?
- Which facts must Idriç/Edric preserve in types or return values so independently terminating programs can still compose safely?
- How are hard constraints distinguished from preferences?
- How are incomparable candidates represented and explained?
- How much of component compatibility can be proven statically?
- How are benchmark results tied to hardware and workload without becoming stale metadata?
- How does the system distinguish a semantic difference from a mere implementation difference?
- When should architectural choices remain unresolved until deployment or runtime?
- How are selected decisions frozen so an ordinary rebuild remains deterministic?
- What is the right boundary between an LLM elaborator, a constraint solver, a package resolver, an autotuner, and the conventional compiler?
- Can useful architectural search proceed without an LLM, and which model-assisted steps can be checked or replayed deterministically?
- Can the architectural stage produce a useful explanation of *why* each important choice was made?

## Working thesis

High-level programming need not mean pretending implementation details do not exist. It can mean **deferring implementation decisions until the system has enough information to make or surface them intelligently**.

The programmer specifies goals, invariants, tastes, and consequential tradeoffs. An architectural system searches and recombines known algorithms and implementations, discovers consequences, asks questions where human judgment is actually needed, and produces an explicit architecture with an evidence trail. Small target programs can then be generated and composed through ordinary system interfaces. The size of the design-time knowledge base is not a virtue by itself; its value is whether it helps produce a better, smaller, understandable result.

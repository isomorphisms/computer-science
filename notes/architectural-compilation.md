# Architectural compilation: programming by goals, choices, and recombination

Status: rough engineering / programming-language-design notes.

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

## The algorithm catalog is a partial order, not a menu with one winner

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

The design space is therefore closer to a dense partially ordered or Pareto space than a ranked list.

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

The name "adverb" is provisional, but the separation is useful.

## Programming as a branching Q&A

At the architectural level, program construction may look less like writing a linear recipe and more like answering a branching series of questions.

Some questions can be answered mechanically:

- this implementation requires a server, but the program is offline;
- this component cannot run on the target architecture;
- these licenses cannot be combined;
- these interfaces do not compose;
- this memory bound cannot be met.

Other questions belong to the programmer:

- 3 MB and 93% accuracy, or 18 MB and 96% accuracy?
- deterministic behavior, or somewhat better average performance?
- a simpler implementation, or a more specialized one?
- optimize startup, steady-state throughput, battery use, or binary size?

A design choice can expose another branch of questions. The system should surface the implications rather than silently make consequential choices.

An LLM is potentially useful here because the initial goal and many preferences are naturally underspecified. The LLM need not be the final compiler or trusted proof engine. It can mediate between human intent and an explicit, mechanically checkable architecture.

## Ordinary compilation remains ordinary

Once architectural choices have been resolved, the lower stage should become boring again:

```text
goal
  -> architectural search / elaboration
  -> explicit selected components and contracts
  -> mechanically checkable program
  -> conventional compiler
  -> machine code
```

The expensive, uncertain, conversational part should not need to happen on every rebuild. The selected architecture can be committed and reproduced.

## Data-structure vocabulary

Keep representation names semantically distinct throughout the catalog and its
explanations:

- a list remains a list when its length is known;
- use `SizedList` or `ListOfLength` when a list's length is part of its
  type, including when that length is computed and packaged with the result;
- use `Array` for indexed contiguous storage;
- reserve `Vector` for a genuine mathematical or numeric vector.

A known length is a contract about a value. It does not by itself change a
list's representation or turn the list into a vector. Upstream compatibility
names may remain at their boundary, but they should not determine the
architecture's vocabulary.

## STL as an incomplete precursor

The C++ Standard Template Library is relevant as an early approximation of part of this idea.

STL separates generic algorithms from data structures and gives programmers reusable implementations instead of forcing them to reimplement every solved problem. But it still leaves much of the architectural selection burden on the programmer.

The programmer is still expected to decide whether the problem wants a dynamic array, deque, list, map, unordered map, and so forth, often before having a good reason to know. Generic programming makes the selected pieces reusable, but does not solve the higher-level question:

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

## SHARK² / swipe decoding as a concrete example

A phone keyboard gives a manageable example.

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

A major reason this upper layer needs empirical information is that an elegant decomposition in an algorithm or language model may not correspond to good execution on real hardware.

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

## Open design questions

- What is the representation of a high-level goal before implementation choices are resolved?
- Which "adverbs" belong in the language, which belong in project policy, and which belong only in the conversational design layer?
- How are hard constraints distinguished from preferences?
- How are incomparable candidates represented and explained?
- How much of component compatibility can be proven statically?
- How are benchmark results tied to hardware and workload without becoming stale metadata?
- How does the system distinguish a semantic difference from a mere implementation difference?
- When should architectural choices remain unresolved until deployment or runtime?
- How are selected decisions frozen so an ordinary rebuild remains deterministic?
- What is the right boundary between an LLM elaborator, a constraint solver, a package resolver, an autotuner, and the conventional compiler?
- Can the architectural stage produce a useful explanation of *why* each important choice was made?

## Working thesis

High-level programming need not mean pretending implementation details do not exist. It can mean **deferring implementation decisions until the system has enough information to make or surface them intelligently**.

The programmer specifies goals, invariants, tastes, and consequential tradeoffs. A large architectural system searches and recombines known algorithms and implementations, discovers consequences, asks questions where human judgment is actually needed, and produces an explicit architecture. Conventional compilation then turns that architecture into executable code.

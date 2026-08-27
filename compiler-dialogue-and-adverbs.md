# Compiler dialogue and implementation adverbs

Date: 2026-08-26

## Motivation

The `X\b` × 100,000 Thumb fixture exposes a general compiler-design question.

The semantic request can be tiny:

> emit the byte sequence `X\b` 100,000 times

but there are materially different ways to realize it:

- make 100,000 tiny writes;
- buffer the output and make a few larger writes;
- use a fixed-size bounded buffer and flush in chunks;
- compile more than one implementation and choose at runtime based on the sink or deployment environment.

These implementations can have nearly identical source-level meaning while differing radically in syscall count, memory use, latency, power, code size, startup cost, failure visibility, and suitability for a terminal versus a pipe/file/device.

The compiler should not silently pretend there is one universally correct implementation policy.

## Verbs and adverbs

A useful mental model is:

- the **verb** says what computation or effect is requested;
- an **adverb** says how that computation should be carried out.

For output:

```text
verb:     emit these bytes
adverbs:  immediately / buffered / in 4 KiB chunks / adaptively
```

Other possible implementation adverbs include:

```text
compactly
quickly
with bounded memory
with low latency
lazily
eagerly
serially
in parallel
vectorized
on the CPU
on the GPU
at F16 / F32 / F64 precision
with exact overflow checks
with a stated approximation tolerance
```

This is not merely syntax. It is a possible language/compiler architecture: keep semantic intent separate from execution policy where the separation is real and useful.

## A compiler can converse instead of merely warn

A compiler normally emits diagnostics after it has already chosen a model of what the programmer ought to have meant. Another possibility is to surface an actual choice when a decision is consequential and cannot be inferred safely.

For the 100,000-output fixture, a useful interaction might communicate facts such as:

```text
This loop emits 200,000 bytes.

Immediate output:
  about 100,000 write operations
  about 2 bytes per write
  minimal buffering
  earliest visibility

Buffered output:
  bounded memory chosen by policy
  far fewer write operations
  later visibility unless explicitly flushed

Adaptive output:
  compile both policies
  choose using the output sink and stated latency requirement
```

The important behavior is that the compiler supplies information to the designer rather than scolding the designer.

The answer should also be durable. A programmer should be able to answer once, record the policy in source/project configuration, change it later, or select different policies for different call sites/deployments.

## Questions worth asking

Do not interrogate the programmer about every optimization. Ask only where the answer changes an important tradeoff that the compiler cannot determine from semantics or target facts.

Potential questions/policies include:

- Is this output interactive, or is throughput more important than immediate visibility?
- Is memory bounded to a known amount?
- Is code size more important than startup or throughput?
- May two writes be combined if their byte order remains identical?
- Must each effect become externally visible before the next computation?
- Is failure/crash visibility between effects semantically important?
- Is the target a terminal, pipe, regular file, socket, device, GPU, or bare-metal peripheral?
- Is an approximation allowed, and if so what error/tolerance is permitted?
- Is the policy fixed for this build or may it be selected dynamically?

## Semantic boundary: buffering is not always "just an optimization"

If the only observable result is the final byte stream, then combining writes can preserve the relevant semantics.

If timing and visibility are observable, these can differ:

```text
write X
flush
compute for one second
write Y
```

versus

```text
buffer X
compute for one second
buffer Y
flush XY
```

Likewise, a crash between `X` and `Y`, a terminal animation, a hardware register, or a network peer may distinguish them.

Therefore an adverb must state which observations are allowed to change. The compiler should not call two implementations equivalent until the relevant semantic boundary is explicit.

## Compile A, compile B, choose C

A particularly useful pattern is:

```text
semantic program P
       |
       +--> implementation A: immediate / low latency
       |
       +--> implementation B: buffered / high throughput
       |
       +--> selector C: choose A or B from runtime facts
```

`C` is itself a program.

Examples of runtime facts it could inspect include:

- terminal versus regular file;
- message/output size;
- available memory;
- battery/power mode;
- target hardware capability;
- user-selected latency/throughput mode.

This avoids forcing one global choice where case A and case B genuinely differ.

It also gives the compiler an interesting new responsibility: not merely generate code, but expose a family of justified implementations and the conditions under which each is appropriate.

## Profiles can answer compiler questions

The conversation need not literally happen at every build. Answers can become named deployment profiles:

```text
interactive-phone
small-bare-metal
batch-throughput
low-power
smallest-binary
exact-debug
```

A profile is a bundle of implementation adverbs/constraints. The compiler can ask only when a program reaches a choice not settled by the current profile.

## Evidence, not cleverness

Whenever possible the compiler should attach measurable consequences to a choice rather than labels such as "fast" or "optimized".

For example:

```text
immediate:
  100,000 write syscalls
  2-byte output unit
  O(1) buffer

buffered 4096:
  at most about 49 full-buffer writes for 200,000 bytes,
  plus a final partial write
  <= 4096-byte working buffer
```

The exact estimate may depend on the backend/OS, but explicit quantities are more useful than an unexplained optimization level.

The compiler can also benchmark alternatives and retain evidence by target/revision rather than assuming that a heuristic remains true forever.

## Consequence for tiny-program composition

The same architecture applies to code size and runtime dependencies.

A programmer may want:

```text
runtime-free
smallest standalone ELF
shared runtime
fastest warm execution
smallest total installation for 1,000 programs
```

These are not identical objectives.

A compiler can expose them as policies instead of silently optimizing for one conventional desktop model.

The earlier Thumb fixture is a useful seed example: changing one `X` output into `X\b` repeated 100,000 times changed a deliberately minimal ELF from 101 bytes to 114 bytes because repetition was represented as control flow. The next question is not "which representation is always best?" but "which representation and effect policy are best under the programmer's stated constraints?"

## Research direction

Treat implementation policy as a first-class layer between semantics and backend code generation:

```text
source semantics
      ↓
proved/declared observational boundary
      ↓
implementation adverbs / constraints
      ↓
candidate implementations
      ↓
measurements + target facts
      ↓
static choice or runtime selector
      ↓
backend code
```

This could support both human-directed compilation and automated specialization without making compiler heuristics invisible or pretending every optimization preserves every possible observation.
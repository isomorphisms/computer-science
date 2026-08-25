# IB vertical slice

Status: committed second ComputerScience slice, developed separately from SURFER. This file is an acceptance contract and manual selection trace specification; it is not planner implementation.

## Why IB is separate

SURFER remains first. It tests preservation of mathematical structure and selection of CPU/GPU numerical lowerings.

IB tests a different architectural problem:

- several small terminating programs and process boundaries rather than one numerical kernel;
- durable browser-owned state separated from disposable fetch, prepaint, index, and renderer state;
- selection and escalation among cheap information prepaint, images, JavaScript, and heavier renderers;
- foreground latency versus background investigation and prefetch;
- network, storage, memory, binary-size, and Android-permission constraints;
- the cost of serialization, startup, copying, persistence, and keeping a process resident.

IB may gather evidence in parallel, but it does not replace SURFER's position as the first complete vertical slice. After the first SURFER trace justifies a minimal planner, IB should test whether that machinery generalizes beyond numerical CPU/GPU choice.

## Existing implementation evidence

The [IB repository](https://github.com/isomorphisms/ib) already contains executable work that this slice must use rather than redescribe:

- an Idriç browser core with duplicate-preserving history, rebuildable indices, storage classification, and a deterministic workbench for 10,000 known URLs, 32 logical tabs, and 3- or 10-tab resident limits;
- progressive information prepaint and scientific prefetch paths;
- [IB PR #16](https://github.com/isomorphisms/ib/pull/16), a network-free Android prepaint viewer with tappable links, search handoff, and atomic replacement of partial projections;
- [IB PR #19](https://github.com/isomorphisms/ib/pull/19), the Grease/shell ICU search/fetch boundary feeding the Idriç prepaint path.

Those are implemented components and boundaries. They are not an implemented ComputerScience planner, and the missing return path between #16 and #19 is still concrete work.

## First selection trace

Start with one search query entered in the Android prepaint viewer.

1. Convert the search text into the exact search request.
2. Send the request through the Termux command bridge.
3. Have Grease/shell invoke ICU to fetch.
4. Let Grease/shell place fetched bytes in disposable cache.
5. Run Idriç information extraction and prepaint.
6. Write a local prepaint artifact.
7. Atomically replace the page in the network-free viewer.
8. Let tappable links repeat the same request path.

The trace must preserve which process owns each step and the exact artifacts crossing each boundary.

### Hard constraints

- The display APK has no Internet permission, does not use WebView, and owns neither browser state nor search/network policy.
- Grease/shell, reached through the Termux command bridge, owns executable network/fetch orchestration; ICU is the fetch primitive for this first path.
- Idriç owns browser state, policy and invariants, information extraction, and the renderer-neutral prepaint model.
- Durable tabs, visits, and user decisions remain separate from disposable response, prepaint, and renderer caches.
- A useful prepaint appears before optional heavyweight rendering.
- Link and linked-image targets survive extraction and painting.
- Scaling the known URL universe does not scale the live renderer working set; retain the existing 10,000-URL, 32-tab, and 3-to-10-resident invariants.
- Field Mouse or another JavaScript runtime is an explicit later escalation, not hidden work in the first trace.

### Candidate plans to record

At minimum, compare:

1. Grease/shell invoking ICU, followed by Idriç prepaint and the network-free viewer;
2. handing the request to an external browser;
3. escalating after prepaint to a heavier IB renderer.

The first plan is the intended integration trial. The other plans remain visible so their different permissions, startup costs, memory, interactivity, and state ownership can be explained rather than silently discarded.

## Evidence required

Record:

- the exact query, URL, command, input bytes, prepaint artifact, and source revisions;
- process startup and end-to-end time to first useful paint;
- bytes fetched, bytes retained, and which retained bytes are durable versus cache;
- peak or bounded resident memory for the bridge, fetcher, prepaint producer, and viewer where measurable;
- Android permissions and proof that WebView is absent;
- what happens on bridge failure, invalid content, timeout, cache clearing, and process restart;
- selected and rejected plans with the measurements or hard constraints that decided them.

Unknown measurements remain unknown. A guessed cost or LLM recommendation is not evidence.

## Acceptance

The first IB slice is complete when:

- a search entered in the network-free viewer causes the exact ICU request to run through the bridge;
- returned bytes are converted by Idriç into a renderer-neutral prepaint and painted atomically;
- at least one returned link is tappable and completes the same round trip;
- the last valid page survives malformed or failed replacement input;
- clearing disposable cache does not delete durable tab or visit state;
- the 10,000-URL workbench still bounds resident renderers independently of universe size;
- the trace contains actual timing, byte, permission, state-ownership, and failure observations;
- the selected path and meaningful rejected alternatives can be replayed without consulting a chat transcript.

Writing this file, adding more catalogs, or merely placing #16 and #19 on neighboring branches does not complete the slice.

## Non-goals

This first trace does not require:

- a full HTML/CSS browser engine;
- JavaScript execution;
- WebView, Chromium, GeckoView, or Servo inside the display APK;
- a universal URL grammar or universal renderer policy;
- merging IB implementation code into the ComputerScience repository;
- an automatic planner before the manual trace exists.

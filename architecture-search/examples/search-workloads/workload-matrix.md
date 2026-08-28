# Workload and tool matrix

The familiar tools below are reference lanes, not interchangeable competitors.
Each benchmark names the lane first, then chooses commands that implement the
same observable contract closely enough to serve as oracles or baselines.

## Tool lanes

| Tool or family | Primary input unit | Selection job | Default/typical projection | What it teaches the compiler study |
| --- | --- | --- | --- | --- |
| GNU `find` | filesystem entry | traverse and test path/metadata predicates; optionally act | matching pathnames when `-print` is explicit or implied | tree walking, pruning, metadata calls, action order, safe filename boundaries |
| GNU `grep -F` / `fgrep` | text line over byte streams/files | one or more fixed strings | matching lines with optional names/numbers/count/status | fixed-string oracle, line boundaries, result-density and printing cost |
| GNU `grep` | text line | basic/extended/Perl-style regex according to mode | matching lines or selected projections | regex semantics must remain distinct from literal search |
| `awk` | record split into fields | programmable pattern/action selection and transformation | action-defined output; omitted action for a true pattern prints the record | record parsing, stateful programs, projection, aggregation, and output semantics |
| `bioawk` | biological record/fields | `awk` actions over FASTA/Q, SAM, VCF, BED, GFF, or header-derived fields | selected/transformed biological records or fields | structured biological boundaries, field-aware search, gzip/decoder cost |
| `ag` | candidate files plus contents | recursive candidate pruning followed by literal/regex content search | matching lines, names, counts, context, or status by mode | enumeration/filter/match/format pipeline; parallel independent files |
| `rg` / ripgrep | candidate files plus contents | recursive regex or fixed-string content search with ignore/type policies | explicit match projections | modern recursive-search baseline and strong candidate pruning |
| `git grep` | tracked/work-tree blobs | content search over Git's selected file universe | matching lines/paths | an alternate enumerator can dominate without changing the matcher contract |

`find ... -exec grep ...` is also a deliberate composition lane. It makes the
enumeration/matching boundary visible and exposes batching and process-startup
choices that a fused tool hides.

## Required result shapes

The same matcher should be exercised through several observable projections.
They are separate measurements:

| Result shape | Required work and likely distortion |
| --- | --- |
| quiet membership / exit status | permits earliest legal exit; minimizes formatting |
| first match and position | requires an order and offset unit; still permits early exit |
| count | scans required domain but avoids full record emission |
| filenames/record identifiers with matches | permits per-container early exit |
| all positions | exposes match density and offset construction |
| full matching lines/records | includes allocation, formatting, copying, synchronization, and output bandwidth |
| transformed records | exercises parsing, projection, and output representation rather than matching alone |

Run output to a controlled sink, a pipe, a regular file, and an interactive
terminal only when those are actually relevant. Record the destination.

## Corpus geometries

| Corpus profile | Shape | Relevant questions | Common invalid extrapolation |
| --- | --- | --- | --- |
| source tree | many mostly small text files, nested paths, ignore/type rules | traversal startup, stat/open cost, pruning, mixed literals/regex, sparse output | treating one Linux-kernel checkout as every filesystem |
| media workstation | relatively few huge video/audio objects, sidecars, project files, thumbnails, indexes | metadata/name search, catalog/index lookup, storage bandwidth, warm assets, expensive accidental content reads | assuming recursive content grep is the user's real operation |
| biological sequence | long records, small/ambiguous alphabet, wrapping, FASTQ qualities, compression, varying match density | record boundaries, packed symbols, bit-parallel/SIMD candidates, decoder cost, chunk boundaries | equating raw lines with biological records |
| SMS/message rows | many short mutable records with date/address/box metadata | cursor order, structured pruning, normalization, newest-first `k`, local index freshness | importing filesystem ignore and mmap policy |
| logs/events | append-heavy line/record streams with timestamps and fields | streaming, window predicates, repeated queries, tail latency, compression | assuming static corpus or free preprocessing |
| synthetic crossover family | controlled sizes, patterns, alphabets, layouts, and densities | isolate thresholds and validate analytical predictors | using synthetic wins as user-impact claims |

The media example is intentionally not a request to scan terabytes of video.
It tests whether the planner notices that a different semantic operation,
catalog, index, or filesystem organization is appropriate.

## Parameters that must vary

At minimum retain these workload coordinates:

- entry/record count and depth;
- file/record size distribution, not only total bytes;
- text/binary/compressed fractions and decoder;
- pattern count, length, repetition, alphabet, and compile-time/dynamic status;
- match density, clustering, earliest-match position, and output bytes;
- line/record/chunk boundary placement;
- ignored/pruned fraction and cost of deciding it;
- cold/warm cache and maintained-index state;
- storage/filesystem/mount and target hardware;
- concurrency and CPU-placement policy;
- required order, offsets, and projection.

These coordinates are candidate features for predictors and selectors. They
are not license to collapse the result into one undocumented benchmark score.


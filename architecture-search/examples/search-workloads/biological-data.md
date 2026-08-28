# Biological data as a required search proxy

Biological data belongs in the shared compiler tests because it supplies
search shapes that source trees often do not: very long records, a tiny but
ambiguous alphabet, high repetition, overlapping motifs, record-aware
semantics, optional quality strings, compression, and chunk boundaries.
It is a proxy and contrast case, not a claim that biology is "just grep."

## Two distinct semantic lanes

1. **Raw-byte/line lane.** Use a byte or line oracle such as `grep -F`. Newline
   and header bytes participate exactly as declared.
2. **Parsed-record lane.** Use FASTA/FASTQ record semantics, for example via
   `bioawk -c fastx` or a separately checked parser. Sequence wrapping may be
   removed inside a record, quality is a different field, and a match cannot
   cross from one biological record into another.

These lanes may intentionally return different results on the same file. That
difference is a semantic test, not a bug to average away.

## Tier 0: tiny exact fixtures

Every backend adoption starts with small fixtures whose complete outputs can
be inspected:

| Case | Data and query | Required observation |
| --- | --- | --- |
| overlap | sequence `AAAAA`, literal `AAA` | all-match positions are `0,1,2`; membership/count modes state their own projection |
| absent motif | sequence `ACGTACGT`, literal `TTT` | no sequence match and the declared no-match exit/result |
| present motif | sequence `ACGTACGT`, literal `CGT` | positions `1,5` under zero-based byte offsets |
| wrapped FASTA | one record whose `ACG`/`T` split crosses a physical line boundary | parsed-record lane may match `ACGT`; raw line lane must not silently do so |
| record boundary | record 1 ends `AC`, record 2 begins `GT` | parsed search must not invent `ACGT` across records |
| FASTQ quality decoy | sequence lacks `AAA`, quality contains `AAA` | sequence-field query does not match the quality field |
| ambiguous symbol | data contains `N` and lower-case bases | literal, wildcard/ambiguity, and case policy are stated rather than inferred |
| chunk boundary | a motif is divided across input chunks within one record | streaming implementation preserves the declared record semantics |

Also include empty input, empty sequence, one-byte pattern, pattern longer than
the record, and lengths around the backend's machine-word/packed-state boundary.

## Tier 1: deterministic generated families

Generate corpora from recorded seeds and parameters so crossovers can be
reproduced. Vary:

- total bases and record-length distribution;
- alphabet (`ACGT`, ambiguous IUPAC symbols, and declared case policy);
- motif length and count;
- match density, clustering, and earliest match;
- random-looking versus repetitive/low-complexity regions;
- FASTA wrapping width and FASTQ quality length;
- plain versus gzip input;
- chunk size and boundary alignment.

Generated data is appropriate for mechanism isolation, not for claims about a
scientific population.

## Tier 2: pinned real snapshots

Later measurements may add public biological snapshots. Each snapshot record
must keep its source, license/terms, retrieval date, exact file hashes, format,
compression, preprocessing commands, and derived shape summary. Do not use a
mutable "latest" download in a reproducibility claim.

The suite should include more than one real shape when real-data conclusions
are made: for example long assembled sequences and many shorter reads. One
dataset cannot represent all sequence-search users.

## Baselines and compiler artifacts

For a fixed literal, retain a competent portable baseline and exact oracle.
Candidate implementations may include scalar scans, Two-Way/Boyer–Moore-family
search, Shift-And/bit-parallel state, candidate-byte filtering, SIMD, packed
two-bit sequence representations, automata for many motifs, or an index plus
exact verification. They are candidates, not mandatory stages.

For every candidate record:

- setup/preprocessing and steady-state work separately;
- match positions/records and boundary correctness;
- loads, comparisons, branches, register state, spills, tables, and vector use;
- bytes decoded/scanned and output bytes;
- code, table/index, and working-set size;
- exact target and execution context.

An idea can plausibly lose on a many-small-file source tree and win on long
resident sequences, or the reverse, because traversal, setup, alphabet,
record length, match density, output, and index amortization differ. The suite
exists to expose those crossovers instead of arguing from analogy.

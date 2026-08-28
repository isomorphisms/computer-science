# Canonical backend adoption issue

Use the title and body below in each CPU/compiler repository. The duplication
is intentionally small; detailed notes remain here.

## Title

Adopt the shared search-workload comparison suite

## Body

<!-- shared-search-workload-suite-v1 -->

Use the canonical ComputerScience search suite when this compiler/backend is
ready to test search-related semantics or lowering:

- [suite and adoption sequence](https://github.com/isomorphisms/computer-science/tree/stacks-moduli-and-deformation-notes/architecture-search/examples/search-workloads)
- [`ag` / The Silver Searcher review](https://github.com/isomorphisms/computer-science/blob/stacks-moduli-and-deformation-notes/notes/the-silver-searcher-review.md)
- [search families, compiler adverbs, and the stacky boundary](https://github.com/isomorphisms/computer-science/blob/stacks-moduli-and-deformation-notes/notes/search-algorithm-families-adverbs-and-stacky-boundary.md)

This is a thin adoption/tracking issue, not a request to copy the notes or to
bypass this repository's native language, ABI, execution, or I/O gates.

When the prerequisites exist:

- [ ] state the exact semantic lane and portable oracle (`find`, `grep -F`/`fgrep`, `awk`/`bioawk`, `ag`, `rg`, or an explicit composition);
- [ ] include the shared biological fixtures, especially overlap, line/record/chunk boundaries, quality-field decoys, and lengths around the target word boundary;
- [ ] contrast the biological shape with a source-tree workload and at least one deliberately different corpus geometry;
- [ ] predict setup, bytes/records/leaves visited and pruned, loads, comparisons, branches, register pressure, tables/indexes, output work, and likely crossover points;
- [ ] check exact outputs before performance claims;
- [ ] inspect assembly/object code for registers, spills, branches/table dispatch, vector instructions, calls, and code/data size;
- [ ] measure on an exactly recorded machine/workload/filesystem/output context and preserve failures and non-wins;
- [ ] report paired differentials and affected strata, not only a histogram or aggregate average;
- [ ] retain the Pareto frontier and state which adverbs/profile justify any selection.

Do not use "fast" as an unscoped property. A definitive measurement applies to
its recorded setup; results elsewhere are predictions until checked.


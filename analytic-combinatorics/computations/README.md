# Computations

This directory collects short computations that turn analytic-combinatorics results into quantities useful for rough engineering estimates.

The intended progression is:

1. write a combinatorial model;
2. derive or identify its generating function;
3. get an exact or asymptotic coefficient estimate;
4. convert the count into a directly useful magnitude such as bits, bytes, candidate states, or unavoidable enumeration work;
5. compare the prediction with measurements from a concrete implementation and machine.

Start with [quick-estimates.md](quick-estimates.md).

Future computations can include small scripts when calculation becomes annoying by hand, but each script should keep the mathematical derivation visible rather than replacing it with a black-box numeric answer.

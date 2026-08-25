# Analytic combinatorics

These notes are intended to support **pen-and-paper estimates** before or alongside measurements on actual machines.

Analytic combinatorics turns structural descriptions of discrete objects into generating functions and then turns the analytic behavior of those generating functions into quantitative estimates. In this repository the main use is practical: estimate counts, growth rates, likely time or space scales, and the number of bits required to represent families of objects before deciding what must be measured experimentally.

The estimates here are not substitutes for benchmarks. They are a first pass for questions such as:

- How many objects of size `n` are even possible?
- Is the relevant growth polynomial, exponential, factorial, or something in between?
- What exponential base controls the scale?
- What polynomial correction multiplies that exponential growth?
- How many bits are needed just to name or store one of these objects?
- If an algorithm explicitly enumerates a class, what unavoidable time or space scale follows from the class size?
- If a parameter is marked in a generating function, what are its expected value and fluctuations?

## Textbook

Philippe Flajolet and Robert Sedgewick, *Analytic Combinatorics*.

- Official book site: https://ac.cs.princeton.edu/
- Full textbook PDF supplied by the authors: https://ac.cs.princeton.edu/home/AC.pdf
- Online lectures and slides: https://ac.cs.princeton.edu/online/
- Errata: https://ac.cs.princeton.edu/errata/

The files here follow the nine main chapters of the book:

1. [Combinatorial Structures and Ordinary Generating Functions](01-combinatorial-structures-and-ogfs.md)
2. [Labelled Structures and Exponential Generating Functions](02-labelled-structures-and-egfs.md)
3. [Combinatorial Parameters and Multivariate Generating Functions](03-combinatorial-parameters-and-multivariate-gfs.md)
4. [Complex Analysis, Rational and Meromorphic Asymptotics](04-complex-analysis-rational-meromorphic-asymptotics.md)
5. [Applications of Rational and Meromorphic Asymptotics](05-applications-rational-meromorphic-asymptotics.md)
6. [Singularity Analysis of Generating Functions](06-singularity-analysis.md)
7. [Applications of Singularity Analysis](07-applications-singularity-analysis.md)
8. [Saddle-Point Asymptotics](08-saddle-point-asymptotics.md)
9. [Multivariate Asymptotics and Limit Laws](09-multivariate-asymptotics-and-limit-laws.md)

Worked estimates live in [`computations/`](computations/).

## Working rule

Keep two layers separate:

1. **Mathematical scale.** Derive a count, coefficient asymptotic, expected parameter, or information-theoretic lower bound.
2. **Machine observation.** Measure the implementation, hardware, input representation, cache state, compiler choices, and other concrete conditions.

The first layer says what scale should be expected. The second says what actually happened on a machine.

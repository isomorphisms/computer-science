# Quick estimates

A few standard calculations that are useful as templates.

## 1. Fibonacci recurrence: dominant pole

For `F_0=0`, `F_1=1`, and `F_n=F_{n-1}+F_{n-2}`, the generating function is

\[
F(z)=\frac{z}{1-z-z^2}.
\]

The denominator has roots corresponding to the golden ratio. The nearest singularity gives

\[
F_n\sim \frac{\varphi^n}{\sqrt 5},
\qquad
\varphi=\frac{1+\sqrt5}{2}.
\]

So any process that explicitly visits a Fibonacci-sized family grows by about a factor `φ ≈ 1.618` for each additional unit of `n`.

The information needed merely to distinguish all `F_n` possibilities is roughly

\[
\log_2 F_n \approx n\log_2\varphi-\tfrac12\log_2 5.
\]

This converts a combinatorial count into a lower bound measured in bits.

## 2. Catalan objects: square-root singularity

The Catalan generating function satisfies

\[
C(z)=1+zC(z)^2
\]

and has explicit solution

\[
C(z)=\frac{1-\sqrt{1-4z}}{2z}.
\]

The dominant singularity is `ρ=1/4`, with square-root type. Singularity analysis gives

\[
C_n\sim \frac{4^n}{\sqrt\pi\,n^{3/2}}.
\]

Thus the dominant scale is exponential base `4`, with a substantial polynomial correction.

The minimum information required to identify one Catalan object among all size-`n` Catalan objects is approximately

\[
\log_2 C_n
\approx
2n-\frac32\log_2 n-\frac12\log_2\pi.
\]

This is a useful sanity check for compact representations of binary-tree-like objects.

## 3. Central binomial coefficient

Stirling's approximation gives

\[
\binom{2n}{n}\sim \frac{4^n}{\sqrt{\pi n}}.
\]

Therefore

\[
\log_2\binom{2n}{n}
\approx
2n-\frac12\log_2(\pi n).
\]

Again the leading storage scale is about `2n` bits, but the correction is visible and easy to retain in a hand calculation.

## 4. Supercritical sequence schema

Suppose

\[
F(z)=\frac{1}{1-G(z)}
\]

and the first relevant positive solution of `G(ρ)=1` is a simple one, occurring before a singularity of `G`. Locally,

\[
1-G(z)\approx \rho G'(\rho)(1-z/\rho),
\]

so

\[
[z^n]F(z)
\sim
\frac{1}{\rho G'(\rho)}\rho^{-n}.
\]

For a rough estimate, solving `G(ρ)=1` already gives the exponential growth factor `ρ^{-1}`. Evaluating `G'(ρ)` supplies the leading constant.

## 5. From count to exhaustive work

If there are `a_n` candidate objects and an algorithm must inspect every one, then the candidate count itself gives a lower bound of order `a_n` operations, independent of implementation details.

If each candidate needs at least `b_n` bits of live representation, retaining all candidates requires at least

\[
a_n b_n
\]

bits before allocator, alignment, pointer, object-header, cache, or indexing overhead.

The point is not that real programs achieve these bounds. They usually do not. The count gives a scale against which actual measurements can be compared.

## 6. Converting an asymptotic count to decimal magnitude

For

\[
a_n\approx C\alpha^n n^\beta,
\]

take logarithms instead of computing a huge integer:

\[
\log_{10}a_n
\approx
\log_{10}C+n\log_{10}\alpha+\beta\log_{10}n.
\]

Similarly,

\[
\log_2 a_n
\approx
\log_2 C+n\log_2\alpha+\beta\log_2 n
\]

directly estimates the number of bits needed to distinguish `a_n` possibilities.

These logarithmic forms are usually the most convenient pen-and-paper representation once exact counts become enormous.

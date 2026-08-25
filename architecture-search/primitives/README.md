# Target primitives

A primitive is an operation available on a particular execution target: an ISA instruction or idiom, GPU facility, operating-system service, driver capability, or measured platform operation.

Each useful record should eventually identify:

- the target, installed ABI, operating system, runtime, compiler/driver versions, and provenance;
- inputs, outputs, semantic contract, side effects, alignment/layout requirements, and failure behavior;
- feature detection and restrictions rather than assumptions based only on a marketing name;
- costs or cost ranges, with a link to raw measurements or a stated derivation;
- known interactions with neighboring operations.

Platform primitives are not semantic algorithms. Several primitives may realize one algorithm, and one primitive may participate in many algorithms.

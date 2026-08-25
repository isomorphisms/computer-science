# Measurements

Measurements are target- and workload-specific observations, not timeless properties of an algorithm.

Raw records should retain the machine and device identity, installed ABI, operating system, compiler or driver versions, source revision, build flags, workload, data shape, active architectural adverbs, repetitions, uncertainty, and observed result.

Keep failures, timeouts, incompatibilities, and surprising results. They are inputs to later planning and prevent the same bad branch from being rediscovered. Summaries and fitted models should link back to raw observations and be safe to regenerate.

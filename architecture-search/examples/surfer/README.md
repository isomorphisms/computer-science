# SURFER vertical slice

SURFER is the first intended implementation trial for ComputerScience.

## Goal

Carry a compact mathematical renderer specification through typed architectural choices to a result that can be compared with the existing Christian Java renderer, while retaining dimensions, ragged structure, maps/reductions, algebraic identities, and relevant error bounds.

## Initial target paths

- CPU: the historical first device target is ARMv7-A, Thumb-2, and NEON, subject to verification of the installed ABI and usable features. Direct target code remains the desired experiment for numeric inner loops; C may be used only as disposable terminal output where appropriate.
- GPU: preserve a typed mathematical/shader IR and lower it to GLSL ES for the Android driver.

The generic renderer must not absorb Homotopy-specific S/T behavior. The nearest-root Christian Java version is an oracle for behavior and images, not the architecture to reproduce.

## Evidence required

- a small semantic input with explicit shapes and dimensions;
- at least one CPU and one GPU candidate plan, even if one is rejected early;
- assumptions and conservative calculations for computation and data movement;
- raw measurements tied to the exact device, ABI, software stack, and revision;
- comparison with the oracle, including image/error criteria;
- an inspectable record of the selected path, rejected alternatives, and reasons.

Writing these records is preparatory work. The slice is complete only when the path executes and its result can be checked.

# Altium / practical electronics learning resources

## Scope

`altium/` is intentionally a loose name. Altium Designer is not a computer-science primitive. This folder is a bucket for the practical electronics questions that appear as soon as a schematic becomes a physical object: where current actually flows, why a capacitor helps, when a trace becomes a transmission line, why ground is not a magic zero-voltage sink, how fields couple, and when any of this matters enough to care.

The point is not to turn every simple circuit into a signal-integrity project. It is to have a nearby resource when the physical details become the limiting factor.

## Altium Academy
Altium's education channel mixes tool-specific material with genuinely general PCB/electronics instruction. Altium describes Zach Peterson's series as covering design principles that are not tool-specific: https://resources.altium.com/p/resources-lifelong-learner

## Zachariah Peterson — Altium Academy video series
Zach Peterson maintains a compiled index of his Altium Academy videos here: https://www.zachariahpeterson.com/altium-academy-video-series/ . This is the easiest broad entry point for his Q&A-style explanations of PCB and electronics questions.

## Zachariah Peterson — electrical return-current paths
A particularly useful conceptual correction is that current must have a physical return path whose geometry matters. His article is here: https://resources.altium.com/p/what-return-current-path-pcb

## Zachariah Peterson — grounding and reference planes
Use the Altium/Academy material when “ground” stops being an abstract node and the question becomes where return current physically flows, how reference planes work and how plane changes affect EMI. Related article: https://resources.altium.com/p/follow-your-multilayer-ground-return-path-to-prevent-emi

## Zachariah Peterson — decoupling capacitors and power integrity
Decoupling is a good example of the desired level of attention: know why the capacitor and connection geometry matter, then stop worrying once the design is comfortably inside its requirements. Peterson's Academy index includes current decoupling and power-integrity videos.

## Zachariah Peterson — capacitance, inductance and impedance
These are not merely component labels; every conductor geometry has distributed electric and magnetic behavior. The practical question is when those parasitics are negligible and when edge rate, current or geometry makes them dominant.

## Zachariah Peterson — transmission lines and controlled impedance
A wire/trace becomes usefully modeled as a transmission line when propagation time is significant relative to signal edge timing. The Altium material is useful for converting that abstract statement into PCB stackup, trace width, reference-plane and termination decisions.

## Zachariah Peterson — differential pairs
Differential signaling is not simply “two traces near each other.” Pair geometry, reference planes, common-mode behavior and receiver requirements determine what matching actually matters.

## Zachariah Peterson — vias, stackups and layer transitions
Layer changes modify return paths and impedance. Peterson's current Academy index includes HDI stackups, via dispersion and layer-transition material: https://www.zachariahpeterson.com/altium-academy-video-series/

## Zachariah Peterson — EMI / EMC
The useful goal is not mystical anti-noise ritual; it is identifying current loops, field coupling, return discontinuities, filtering and enclosure/cable paths that can actually create emissions or susceptibility problems.

## Zachariah Peterson — PCB design rules and manufacturability
A board must be buildable as well as electrically correct. Altium's DFM guide is a useful broad reference: https://resources.altium.com/p/dfm-guidebook

## Altium Resources / education material
The wider Altium resource library can be used as a searchable practical supplement when a board-level question appears. Treat vendor-tool advice separately from underlying electrical claims.

## Forrest M. Mims III — Getting Started in Electronics
Forrest Mims is included here because his hand-drawn, build-it-and-measure-it style is almost the opposite of overabstracting electronics. *Getting Started in Electronics* covers basic electricity, components, integrated circuits and a large collection of small circuits. His official site identifies it among his books: https://forrestmims.org/publications/

## Forrest M. Mims III — Engineer's Mini-Notebook series
The Mini-Notebooks are compact collections around timers, op-amps, sensors, communications, formulas and small circuits. They are useful when the right level of abstraction is “here is a transistor, resistor, LED and switch; what happens?” rather than a software stack.

## Forrest M. Mims III — publications index
Forrest Mims' own publication index is here: https://forrestmims.org/publications/ . It can be mined later for specific short notes or experiments rather than trying to catalog everything now.

## breadboard-and-meter-first electronics
This is the working philosophy for the folder: sometimes the best explanation is a tiny circuit, a meter/scope reading and a physical observation. Computer-controlled measurement is useful when it adds something; it should not displace the simpler experiment merely because more software can be inserted.

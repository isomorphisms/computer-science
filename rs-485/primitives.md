# RS-485 primitives

## Scope

Yes: **RS-485** is the industrial one you were trying to name, not RS-465. The formal standard is TIA-485-A, *Electrical Characteristics of Generators and Receivers for Use in Balanced Digital Multipoint Systems*. RS-422 is the nearby standard you were also half-remembering; there is no need to invent an EIA-434/RS-465 target here.

References: TI RS-485 introduction: https://www.ti.com/document-viewer/lit/html/SLLA418 ; TI RS-485 basics: https://www.ti.com/lit/wp/slla545/slla545.pdf ; Analog Devices RS-232/422/485 comparison: https://www.analog.com/en/resources/technical-articles/guide-to-selecting-and-using-rs232-rs422-and-rs485-serial-data-standards.html

## balanced differential signaling
RS-485 sends information as a differential voltage on a balanced pair. External noise coupled similarly into both wires can be rejected by the receiver.

## A / B differential pair
The bus is normally described with A and B conductors. Unfortunately vendor polarity naming is not perfectly historical-consistent, so always check the transceiver truth table rather than trusting the letters alone.

## multipoint bus
Multiple nodes share the same physical medium. This is a major reason RS-485 became common in factory automation, motor control, building automation and fieldbus systems.

## multiple drivers
Unlike RS-422, more than one node may be capable of driving a bus, but ordinary designs ensure only the intended transmitter is enabled at a given time.

## multiple receivers
Many receivers can listen to the same pair. Loading is specified in terms of unit loads rather than simply assuming one receiver.

## driver enable / tri-state
A transmitter can release the line so another node may drive it. Correct direction-control timing is central to half-duplex RS-485 implementations.

## half-duplex two-wire arrangement
A single differential pair is commonly shared for both directions. Nodes take turns transmitting.

## full-duplex four-wire arrangement
Two differential pairs can provide separate directions. This is useful for a controller-to-many-receivers style network, though topology and protocol still need explicit design.

## unit load
The standard defines receiver loading through the unit-load model. Traditional calculations use a maximum loading equivalent to 32 unit loads; modern fractional-unit-load transceivers can permit more physical nodes while remaining within the electrical load budget.

## termination
A long/fast RS-485 trunk is a transmission line. Parallel termination is normally placed at the ends of the main bus, with resistor value chosen to match the cable's differential characteristic impedance.

## failsafe / idle biasing
A released bus can otherwise sit near the receiver threshold. Bias networks or transceivers with built-in failsafe behavior establish a defined idle state. Biasing is an implementation concern layered onto the standard electrical limits.

## receiver differential threshold
Receivers classify the bus state from the difference between A and B. Small differential voltages must be detected correctly across the permitted common-mode range.

## common-mode range
Nodes can tolerate a substantial difference in local ground potential while still reading the differential pair, within the transceiver/standard limits. This is useful but is not unlimited galvanic isolation.

## main bus / trunk topology
RS-485 works best as a linear trunk with termination at the ends. Treating it as arbitrary star wiring can create reflections and signal-integrity problems.

## stubs
Branches from the main trunk add discontinuities. Their acceptable length depends on edge rate, cable and timing, so “slow baud rate” alone is not always the whole story.

## transmit-to-receive turnaround
In two-wire systems, software/hardware must stop driving after the final transmitted bit and enable reception at the right time so another node can answer.

## signal reference / ground considerations
Differential signaling does not make ground/reference issues disappear. Common-mode limits, shielding, bonding and isolation must be handled for the actual installation.

## protocol / addressing / character framing supplied separately
RS-485 does **not** define Modbus addresses, start/stop bits, CRCs, packet boundaries or message meaning. Modbus RTU, Profibus, DMX512 and many proprietary protocols use RS-485 as an electrical layer.

# RS-422 primitives

## Scope

The name usually used in practice is RS-422; the current standard is TIA-422-B, *Electrical Characteristics of Balanced Voltage Digital Interface Circuits*. It is an electrical interface, not an application protocol.

References: TIA-422-B listing: https://store.accuristech.com/standards/tia-tia-422-b?product_id=2591646 and TI overview: https://www.ti.com/lit/an/slla070d/slla070d.pdf

## balanced differential signaling
Information is represented by the voltage difference between two conductors rather than one conductor relative to ground, improving noise immunity and useful distance.

## generator
A generator is the line driver for one RS-422 circuit.

## receiver
A receiver senses the differential voltage while tolerating the specified common-mode range.

## one generator per circuit
A defining distinction from RS-485 is that an RS-422 circuit has one active generator rather than multiple drivers taking turns on one pair.

## multiple receivers per circuit
One generator can feed more than one receiver, which permits a one-to-many multidrop arrangement.

## A / B differential pair
The two signal conductors form a balanced pair. Vendor A/B polarity naming has historically caused confusion, so actual data-sheet truth tables should be checked when connecting transceivers.

## termination
Long/fast differential wiring behaves as a transmission line and is normally terminated at the receiving end according to the cable and application.

## receiver differential threshold
The receiver decides state from the differential voltage between the pair, not from either line alone.

## common-mode range
Both conductors can move together relative to local ground within limits while the differential information remains readable.

## point-to-point link
One driver and one receiver is the simplest RS-422 topology.

## multidrop one-driver link
A single driver can distribute the same signal to multiple receivers. This is not the same as RS-485 multipoint operation with multiple enabled transmitters over time.

## full-duplex two-pair arrangement
A common bidirectional system uses a separate differential pair for each direction, giving continuous full-duplex communication.

## protocol / character framing supplied separately
RS-422 does not tell an application what bytes mean, how devices are addressed or what checksum to use. UART framing, proprietary packets or a higher-level protocol supplies that layer.

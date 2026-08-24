# CAN bus primitives

## Scope

This is a first-pass catalog of the things a CAN implementation or measurement can directly deal with. Classical CAN and CAN FD are the main baseline; CAN XL is included as the newer generation rather than pretending “CAN” is frozen in 1990.

Primary references: Bosch CAN protocol overview: https://www.bosch-semiconductors.com/products/ip-modules/can-ip-modules/can-ip-overview/canipprotocols.html and CAN in Automation knowledge base: https://www.can-cia.org/can-knowledge/

## physical bus
A CAN network is a shared serial bus. The protocol controller and the physical transceiver are separate conceptual pieces; the transceiver converts controller logic to the electrical bus.

## CAN_H / CAN_L
High-speed CAN normally uses a differential pair named CAN_H and CAN_L. Receivers care primarily about their voltage difference, which provides useful noise rejection.

## dominant bit
A dominant bit actively drives the bus state and wins when another node simultaneously transmits recessive. This electrical/logical asymmetry is what makes bitwise arbitration possible.

## recessive bit
A recessive bit is the passive logical state. A transmitter sending recessive while observing dominant learns that another transmitter has priority.

## bit time and sample point
Each bit is divided into timing segments around a configured sample point. Nominal bit rate alone is not enough; propagation delay, oscillator tolerance and network length affect usable timing.

## synchronization and resynchronization
Nodes align their local bit timing to observed bus edges. Resynchronization corrects bounded phase errors while communication continues.

## 11-bit identifier
Classical base-format CAN frames carry an 11-bit arbitration identifier. Lower numerical identifiers dominate higher ones during arbitration.

## 29-bit extended identifier
Extended-format CAN provides a 29-bit identifier. It expands the identifier space while preserving CAN's arbitration mechanism.

## non-destructive arbitration
Multiple nodes may begin transmitting together. Each monitors the bus bit by bit; a node that sends recessive and sees dominant stops transmitting, while the winning frame continues without being corrupted.

## data frame
A data frame is the ordinary carrier of application data plus identifier, control, CRC, acknowledgement and framing fields.

## remote frame (Classical CAN)
Classical CAN defines a remote frame for requesting a data frame with a matching identifier. CAN FD does not use remote frames.

## error frame
A node detecting a protocol error can deliberately signal an error frame, causing the current transmission to be discarded and retried under the protocol rules.

## overload frame
Classical CAN defines overload signaling to delay the next frame under limited conditions. It is distinct from an error frame.

## start of frame
The start-of-frame bit marks the transition from idle/intermission into a new frame and provides a synchronization edge.

## control field / DLC
Control bits identify frame format features. The data-length code indicates payload length; in CAN FD, DLC values above 8 map to larger standardized payload sizes rather than directly equaling the byte count.

## data field
This is the application payload. Classical CAN carries up to 8 bytes; CAN FD extends the payload to as much as 64 bytes.

## CRC field
The cyclic redundancy check protects the transmitted frame against many classes of corruption. CAN FD strengthens the CRC scheme for its larger frames.

## ACK field
Receivers that accepted a frame correctly assert the acknowledgement slot. Lack of acknowledgement is itself an error condition for the transmitter.

## end of frame / intermission
Fixed recessive sequences close a frame and separate it from the next bus access opportunity.

## bit stuffing
CAN inserts complementary stuff bits after runs of identical bits in specified frame regions. Receivers remove them; violations are detectable errors.

## error detection
CAN combines CRC checking, frame-format checks, acknowledgement checks, bit monitoring and stuffing checks. CAN in Automation gives a useful summary of these mechanisms.

## error counters and error-active / error-passive / bus-off
Transmit and receive error counters control a node's fault-confinement state. Persistent faults move a node from error-active to error-passive and eventually bus-off rather than allowing one bad node to destroy the network indefinitely.

## CAN FD FDF / BRS / ESI
CAN FD adds explicit format/status bits: FDF identifies an FD frame, BRS permits a faster data phase, and ESI exposes the transmitter's error-state information.

## CAN FD larger payload
CAN FD supports payloads beyond 8 bytes, up to 64 bytes, while retaining identifier arbitration at the beginning of the frame.

## CAN XL
CAN XL is the newer CAN generation with substantially larger payloads and higher data rates. It belongs beside Classical CAN and CAN FD as a protocol generation, not as a small optional flag on a Classical CAN frame.

## transceiver
The transceiver is the electrical interface to CAN_H/CAN_L. It handles bus drive and receive levels; protocol framing and arbitration live in the controller.

## controller
The CAN controller implements frame serialization, arbitration, stuffing, CRC, acknowledgement, error handling and buffering. A microcontroller may integrate it, but the functions remain conceptually distinct.

## termination
High-speed CAN is normally treated as a transmission line and terminated at the ends of the main bus. Termination, topology and stub lengths are physical-network facts, not application-layer protocol choices.

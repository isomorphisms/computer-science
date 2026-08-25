# I²C primitives

## Scope

I²C is a shared two-wire, addressed, synchronous bus. Unlike SPI, it has a real bus-level specification covering signaling, arbitration, acknowledgement and timing.

Primary reference: NXP, *I2C-bus specification and user manual*, UM10204 Rev. 7.0: https://www.nxp.com/docs/en/user-guide/UM10204.pdf

## SDA
Serial Data carries address, direction, payload and acknowledgement information. It is bidirectional.

## SCL
Serial Clock determines the bit timing. A controller normally generates it, but targets may hold it low when clock stretching is permitted.

## open-drain / open-collector drive
Devices normally pull SDA/SCL low or release them; they do not actively drive the bus high. This wired-AND behavior is fundamental to arbitration and acknowledgement.

## pull-up resistors
Released lines return high through pull-ups. Pull-up value, supply voltage and total capacitance determine rise behavior and therefore usable speed.

## controller
A controller initiates a transaction and supplies the clock. Older documentation calls this a master.

## target
A target responds to an address and participates under the controller's clock. Older documentation calls this a slave.

## START condition
A START is SDA transitioning high-to-low while SCL is high. It claims the bus and begins a transfer.

## repeated START condition
A controller may issue another START without first issuing STOP. This keeps ownership while changing direction or addressing another phase of a compound transaction.

## STOP condition
A STOP is SDA transitioning low-to-high while SCL is high. It releases the bus after a transaction.

## 7-bit address
The common address form selects one target using seven address bits followed by a direction bit.

## 10-bit address
The specification also defines a larger 10-bit address sequence for devices needing the expanded address space.

## read / write direction bit
The address phase includes a bit indicating whether the controller intends to write to or read from the addressed target.

## 8-bit data byte
Payload is transferred byte by byte, most-significant bit first, with an acknowledgement bit after each byte.

## ACK
The receiver pulls SDA low during the acknowledgement clock pulse to indicate successful acceptance of the preceding byte.

## NACK
Leaving SDA high during the acknowledgement clock signals non-acknowledgement. It can mean no responding target, inability to accept more data, or the controller ending a read.

## clock stretching
A device may hold SCL low to delay the next clock transition where the relevant mode/devices permit it. Real controllers differ in how completely they support this feature.

## arbitration
If multiple controllers start together, each monitors SDA while transmitting. A controller that attempts a high but observes a low loses arbitration without corrupting the winning transfer.

## multi-controller operation
I²C was designed to permit more than one controller on the same bus, though many practical embedded systems use only one.

## clock synchronization
Open-drain SCL behavior lets controllers synchronize their clocking when more than one is active.

## bus-free condition
The specification defines an idle interval with SDA and SCL high before a new START under the relevant timing rules.

## Standard-mode / Fast-mode / Fast-mode Plus / High-speed mode
I²C defines several speed/timing regimes. A design has to satisfy the electrical and timing requirements for the mode actually used; the name “I²C” alone does not imply a rate.

## rise time / fall time / setup / hold timing
The protocol is constrained by explicit edge, setup and hold times. These are just as real as the logical START/ACK vocabulary when deciding whether a physical bus will work.

## bus capacitance
Total line capacitance interacts with the pull-ups and rise-time limits. A bus that is logically correct can still fail because it is electrically too large or slow.

## reserved addresses / general call
Parts of the address space have special meanings. The general-call address and other reserved patterns must not be treated as ordinary device addresses.

## data valid while SCL high
Except for START and STOP, SDA is expected to remain stable while SCL is high. Data transitions normally occur while SCL is low.

## stuck-bus detection and recovery conventions
A device failure can leave SDA or SCL held low. Recovery procedures such as clocking SCL and generating a STOP are common engineering conventions, but the exact recovery behavior must be checked against the devices involved.

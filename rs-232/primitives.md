# RS-232 primitives

## Scope

RS-232 is the familiar historical name; the current TIA designation is TIA-232-F. It defines an interface between data terminal equipment and data circuit-terminating equipment, including electrical, mechanical and functional interchange-circuit characteristics. It should not be confused with a microcontroller's TTL-level UART pins.

References: TIA-232-F standard listing: https://store.accuristech.com/standards/tia-tia-232-f?product_id=2594289 and Analog Devices comparison: https://www.analog.com/en/resources/technical-articles/guide-to-selecting-and-using-rs232-rs422-and-rs485-serial-data-standards.html

## DTE
Data Terminal Equipment is one endpoint role, historically a terminal/computer. The DTE/DCE distinction determines which interface circuits are inputs or outputs.

## DCE
Data Circuit-terminating Equipment is the complementary endpoint role, historically a modem or communications device.

## point-to-point link
Ordinary RS-232 is fundamentally a one-driver/one-receiver interface rather than a shared multipoint field bus.

## signal common / ground reference
RS-232 signaling is single-ended with respect to a common reference. Ground-potential differences therefore matter much more than with a balanced differential pair.

## single-ended signaling
Each interchange circuit uses one signal conductor referenced to common rather than a differential pair.

## MARK state
MARK is the logical-one / idle-signaling state and is represented by a negative interface voltage at the receiver under RS-232 conventions.

## SPACE state
SPACE is the logical-zero state and is represented by a positive interface voltage.

## TXD
Transmitted Data is the primary outgoing data circuit.

## RXD
Received Data is the primary incoming data circuit.

## RTS
Request To Send is a control interchange circuit commonly used as part of hardware flow/control handshaking.

## CTS
Clear To Send is the complementary permission/status circuit associated with RTS in common configurations.

## DTR
Data Terminal Ready indicates terminal-side readiness in traditional modem-oriented interfaces.

## DSR
Data Set Ready indicates DCE-side readiness in traditional interfaces.

## DCD
Data Carrier Detect reports detection of an appropriate received carrier in modem-oriented use.

## RI
Ring Indicator reports an incoming ringing condition in classic modem applications.

## timing / clock interchange circuits
TIA-232 also defines timing circuits for synchronous arrangements. RS-232 is broader than the common three-wire TX/RX/GND asynchronous cable.

## connector and pin-assignment interface subsets
The standard defines mechanical/interface arrangements and functional circuit sets. DE-9 and DB-25 connectors are common real-world manifestations, but not every RS-232 device exposes every signal.

## hardware handshaking
Control circuits can coordinate readiness and flow independently of the data stream. Many modern uses omit most modem-control lines.

## UART asynchronous framing (common companion, not the same standard)
Start bit, data bits, optional parity and stop bits are usually supplied by a UART and carried over RS-232 electrical signaling. They are conceptually separate: UART-style framing can exist at TTL/CMOS levels, and RS-232 can also support other arrangements.

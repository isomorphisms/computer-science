# SPI primitives

## Scope

SPI is a de facto family rather than one complete standards document. Different chips agree on a small electrical/shift-register vocabulary and then define their own command framing, timing and extensions. Microchip explicitly notes that SPI has no formal specification.

References: https://developerhelp.microchip.com/xwiki/bin/view/applications/SPI/ and https://www.microchip.com/en-us/products/microcontrollers/8-bit-mcus/peripherals/communication-connectivity/spi

## host
The host initiates transfers and generates the serial clock. Older documents usually call this the master.

## client
A client participates when selected and shifts data in response to the host clock. Older documents usually call this the slave.

## SCK
Serial clock is driven by the host. Every transfer is defined relative to its edges.

## MOSI / SDO
The host-to-client data line is commonly called MOSI; device documentation may instead use SDO/SDI names from the viewpoint of each device.

## MISO / SDI
The client-to-host data line is commonly called MISO. It is independent of the host-to-client line in ordinary four-wire SPI.

## chip select / CS / SS
A select line chooses the participating client and commonly defines a transaction boundary. It is often active-low and commonly one line per client.

## shift register
SPI's simplest mental model is two shift registers exchanging one bit on every clock cycle. Hardware peripherals add buffers and FIFOs around that core behavior.

## clock cycle
A clock cycle provides one shift/sample opportunity in each direction. The exact active edges depend on CPOL and CPHA.

## full-duplex exchange
Standard four-wire SPI shifts one bit each way simultaneously. A read therefore still requires the host to transmit something, often dummy bits.

## clock polarity (CPOL)
CPOL chooses the idle level of SCK and therefore whether the leading edge is rising or falling.

## clock phase (CPHA)
CPHA chooses which clock edge is used for sampling versus changing data. Both devices must agree.

## SPI modes 0-3
The four combinations of CPOL and CPHA are conventionally called modes 0 through 3. Device data sheets should be treated as authoritative because nomenclature around “leading” and “trailing” edges is less ambiguous than folk descriptions.

## bit order
Many devices use most-significant-bit first, but some controllers and peripherals permit least-significant-bit first. Bit order is a transaction-format choice, not guaranteed by the name SPI.

## word width
Eight-bit words are common, but hardware may support other widths. The selected peripheral's data sheet defines what constitutes a command, address and payload.

## clock rate
The host chooses SCK within the electrical/timing limits of both sides. Maximum rate depends on the devices, board wiring and timing margins rather than on one universal SPI number.

## transaction / chip-select interval
Many peripherals interpret the interval while CS is asserted as one command transaction. Whether CS may remain asserted across bytes is device-specific.

## multiple clients
SCK and data lines can be shared while each client gets its own select. Unselected clients must not drive the shared return-data line.

## daisy chain
Some devices intentionally connect one device's serial output to the next device's serial input so a long shift register can be clocked through one select line. This is device behavior layered on SPI-like signaling.

## three-wire / half-duplex variant
Some devices collapse the two data directions onto one bidirectional line. This is a common SPI-family variant rather than the canonical four-wire full-duplex arrangement.

## dual / quad / octal SPI variants
Flash memories and related devices may transfer multiple data bits per clock using two, four or eight data lines. These extend the SPI idea but require device-specific command and bus-width rules.

## dummy clocks / dummy data
Because clocking itself causes shifting, a host often sends meaningless bits to receive useful data or supplies extra dummy clocks for a device's internal latency.

## no built-in addressing, acknowledgement or packet format
SPI itself does not define I²C-style addresses, acknowledgement bits, checksums or a universal packet format. Those come from the selected peripheral protocol.

# Architecture

## Power

- Input protection front-end (screw terminal → reverse-polarity diode → fuse → VIN_PROTECTED)
- 12V rail: K7812-1000R3 non-isolated module regulator, SIP-3, 1A — no discrete magnetics or feedback network
- 3.3V rail: EVISUN K7803-1000R3 non-isolated module regulator, SIP-3, 1A — no discrete magnetics or feedback network
- Both modules (K7812 for 12V, K7803 for 3.3V) source directly from VIN_PROTECTED (19-24V supported input) in parallel. This avoids cascading and ensures each rail is independently regulated from the protected input. With the current Schottky reverse-polarity stage, 17-18V operation is not guaranteed.
- Decoupling caps on input and output of each module; no other external components required
- First-pass current budget: 12V rail 1A continuous, 3.3V rail 1A continuous with Wi-Fi burst headroom
- Module choice rationale: eliminates inductor/diode/feedback layout risk on Rev A; priority is first-pass success

## Control

- ESP32-S3-WROOM-1-N16R8 as the preferred module choice for Rev A if PCBWay sponsorship and sourcing allow it
- 16 MB flash and 8 MB PSRAM for CircuitPython headroom, buffer space, and future firmware growth
- Use the standard Espressif module footprint and keep the antenna keep-out clear
- If cost or sourcing pressure is significant, downgrade to an ESP32-S3 WROOM-1 PSRAM variant with less flash rather than changing the module family
- ESP32-S3 module with stable boot/reset wiring
- Fan PWM driven as open-drain compatible control
- Tach input protected and level-safe for 3.3V GPIO

## Interfaces

- External UART programming header (1x6, 2.54mm) for flashing and logging via external downloader
- External SPI display header
- Optional expansion header using only safe GPIOs

## Future Revision Note

- Onboard rail telemetry is intentionally deferred from Rev A. Test points cover bring-up needs, and a telemetry block can be added in a later revision if real diagnostic value appears.

## Layout rules

- Keep the board below the pricing threshold by budgeting to a smaller outline early
- Place power modules with input/output decoupling caps within 10mm of module pins
- Keep switch-node concerns eliminated — modules have no exposed switching nodes
- Reserve clear access for screw terminal and external programming header
- Antenna keep-out clear of copper under ESP32-S3 module

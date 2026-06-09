# Schematic Plan

## Current Lock State

- LOCKED: ESP32S3Core (module selection, EN/BOOT network, and exported control nets)
- LOCKED: Fan4Wire (connector choice, PWM sink stage, tach conditioning, and root integration)
- LOCKED: DisplayHeader_ST7789 (live-verified sourcing for J_DISP, 33R damping network, optional MISO link, local bypass, and BL open-collector stage)
- LOCKED: ExternalProgrammingHeader (1x6 external UART/programming header with EN/GPIO0 control nets)
- VALIDATED: Fresh root ERC passes with 0 errors on current schematic set

## Top-Level KiCad Hierarchy

- `fan_controller_revA` as the root sheet
- `InputProtection` with [InputProtection spec](sheet-specs/InputProtection.md)
- `Power12V` with [Power12V spec](sheet-specs/Power12V.md)
- `Power3V3` with [Power3V3 spec](sheet-specs/Power3V3.md)
- `ESP32S3Core` with [ESP32S3Core spec](sheet-specs/ESP32S3Core.md)
- `Fan4Wire` with [Fan4Wire spec](sheet-specs/Fan4Wire.md)
- `DisplayHeader_ST7789` with [DisplayHeader_ST7789 spec](sheet-specs/DisplayHeader_ST7789.md)
- `ExternalProgrammingHeader` with [ExternalProgrammingHeader spec](sheet-specs/ExternalProgrammingHeader.md)
- `OptionalExpansionHeader` with [OptionalExpansionHeader spec](sheet-specs/OptionalExpansionHeader.md)
- `TestPoints` with [TestPoints spec](sheet-specs/TestPoints.md)

## Net Flow

1. `J1` input connector enters `InputProtection`
2. Protected VIN feeds `Power12V` and `Power3V3` in parallel
3. Regulated 12V feeds the fan power pin path
4. Regulated 3.3V feeds `ESP32S3Core`, `DisplayHeader_ST7789`, and safe expansion power
5. `ESP32S3Core` controls `Fan4Wire` via PWM and reads tach back from the fan interface
6. `ESP32S3Core` talks to `ExternalProgrammingHeader` for flashing/logging and to `DisplayHeader_ST7789` via SPI
7. `OptionalExpansionHeader` stays decoupled from the mandatory control path except for shared 3.3V, GND, and safe GPIO nets
8. `TestPoints` taps only stable rails and debug nets; it must not alter control behavior

## Sheet Boundaries

- `InputProtection`: connector, reverse protection, fuse, TVS, input filtering
- `Power12V`: K7812 module stage, input decoupling, 12V rail distribution
- `Power3V3`: K7803 module stage, input/output decoupling, 3.3V rail distribution
- `ESP32S3Core`: module, boot/reset, UART0, and mandatory control GPIO assignment
- `Fan4Wire`: 4-pin fan header, PWM open-drain drive, tach input conditioning, fan power pin
- `DisplayHeader_ST7789`: SPI display header and any series damping parts
- `ExternalProgrammingHeader`: external downloader interface exposing UART/EN/BOOT plus 3V3/GND
- `OptionalExpansionHeader`: safe spare GPIO header with per-line protection
- `TestPoints`: labeled rail and debug points for bring-up

## Capture Order

1. Lock `InputProtection` and power entry
2. Capture `Power12V` around the chosen reference layout
3. Capture `Power3V3` around the chosen reference layout
4. Capture `ESP32S3Core` and boot/reset wiring
5. Add `Fan4Wire`
6. Add `DisplayHeader_ST7789`
7. Add `ExternalProgrammingHeader`
8. Add `TestPoints`
9. Add `OptionalExpansionHeader` only after the mandatory path is stable

## Archived Sheet Note

- `ProgrammingUSB` is retained in the repository for reference only and is not part of the active Rev A signal path.

## Future Revision Note

- Onboard rail telemetry is deferred from Rev A. If bring-up or long-term diagnostics show a real need, add it in a later revision rather than reserving GPIOs and BOM lines now.

## Rules

- Do not use unvetted values for module-decoupling passives
- Keep module input/output decoupling close to module pins per datasheet
- Keep all user-facing headers clear of boot-strap pins and other sensitive ESP32-S3 lines
- Make sure every required part can be assembled by PCBWay without end-user soldering
- Use subsheets to review and lock one subsystem at a time instead of capturing the entire board as one block

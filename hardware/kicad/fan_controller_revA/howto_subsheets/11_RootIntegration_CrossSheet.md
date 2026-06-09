# Root Integration and Cross-Sheet How-To (Beginner Version)

Target sheet: fan_controller_revA

## What this step does
This is where all finished subsheets are connected together at top level.

Think of it as:
- Power backbone: InputProtection -> Power12V/Power3V3 -> loads
- Signal backbone: ESP32S3Core <-> Fan/Display/ExternalProgramming sheets

## Before wiring
1. Open the root sheet.
2. If the root page is messy or from a failed import, remove clutter and place fresh sheet symbols manually.
3. Arrange sheets in a left-to-right flow:
- InputProtection on left
- Power12V and Power3V3 center-left
- ESP32S3Core center
- Fan4Wire, DisplayHeader_ST7789, ExternalProgrammingHeader, and optional sheets on right
4. For each sheet symbol, run Sync Sheet Pins so root pins match child labels.

## Net-level wiring (connect this to that)
Use the Wire tool and connect exactly these root nets:

1. Power rails
- VIN_PROTECTED from InputProtection -> Power12V VIN_PROTECTED
- VIN_PROTECTED from InputProtection -> Power3V3 VIN_PROTECTED
- 12V from Power12V -> Fan4Wire 12V
- 3V3 from Power3V3 -> ESP32S3Core
- 3V3 from Power3V3 -> DisplayHeader_ST7789
- 3V3 from Power3V3 -> ExternalProgrammingHeader
- 3V3 from Power3V3 -> Fan4Wire pull-up rail
- GND shared across all sheets

2. Fan-control interface
- ESP32S3Core FAN_PWM -> Fan4Wire FAN_PWM
- Fan4Wire FAN_TACH -> ESP32S3Core FAN_TACH

3. Display interface
- ESP32S3Core SPI_SCK -> DisplayHeader_ST7789 SPI_SCK
- ESP32S3Core SPI_MOSI -> DisplayHeader_ST7789 SPI_MOSI
- Mapping update: DISP_DC is on GPIO9, DISP_CS is on GPIO10, DISP_RST is on GPIO16, DISP_BL is on GPIO40
- ESP32S3Core DISP_CS -> DisplayHeader_ST7789 DISP_CS
- ESP32S3Core DISP_DC -> DisplayHeader_ST7789 DISP_DC
- ESP32S3Core DISP_RST -> DisplayHeader_ST7789 DISP_RST
- ESP32S3Core DISP_BL -> DisplayHeader_ST7789 DISP_BL

4. Programming interface
- ESP32S3Core UART_TX -> ExternalProgrammingHeader UART_TX
- ExternalProgrammingHeader UART_RX -> ESP32S3Core UART_RX
- ExternalProgrammingHeader EN_RESET -> ESP32S3Core EN_RESET
- ExternalProgrammingHeader GPIO0_BOOT -> ESP32S3Core GPIO0_BOOT

## Cross-sheet consistency rules
1. Root net names must exactly match child-sheet pin names.
2. Do not create duplicate names for the same signal with alternate spellings.
3. Keep optional-sheet nets isolated unless intentionally connected.

## Common mistakes to avoid
1. Forgetting Sync Sheet Pins after child-sheet edits.
2. Wiring VIN_PROTECTED in series through power sheets instead of parallel feed.
3. Mismatched net spellings between root and child sheets.
4. Leaving orphan sheet pins unconnected by accident.

## Final checks (2-minute checklist)
1. Every exported child label has a matching root sheet pin.
2. No orphan pins remain on root sheet symbols.
3. Power rails are connected in the intended topology.
4. Root ERC runs and only intentional warnings remain.

## Exit criteria
- Root sheet is fully wired and readable.
- Cross-sheet pin syncing is clean.
- Whole-project ERC has no unresolved real errors.

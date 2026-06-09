# ExternalProgrammingHeader How-To (Beginner Version)

Target sheet: ExternalProgrammingHeader

## What this sheet does
This sheet exposes a simple external programming/debug connector for the ESP32.

Think of it as:
- Connector out: 1x6 programming header
- Signals in/out: UART + reset/boot control + power/ground reference

## Parts to place
| RefDes | Part | LCSC | Why it is here |
|---|---|---|---|
| J_PROG | Conn_PinHeader_2.54mm_1x6 (right-angle THT) | C42453955 | External programming/debug interface with side cable exit |

## Before wiring
1. Open the root schematic.
2. If sheet ExternalProgrammingHeader does not exist, create a hierarchical sheet named ExternalProgrammingHeader and set its file to ExternalProgrammingHeader.
3. Open the ExternalProgrammingHeader subsheet.
4. Place J_PROG.

## Pin-level wiring (connect this to that)
Use the Wire tool and connect exactly these nets:

1. Header pins
- J_PROG pin 1 -> GND
- J_PROG pin 2 -> 3V3
- J_PROG pin 3 -> UART_TX
- J_PROG pin 4 -> UART_RX
- J_PROG pin 5 -> EN_RESET
- J_PROG pin 6 -> GPIO0_BOOT

2. Required labels
- Add hierarchical labels: 3V3, GND, UART_TX, UART_RX, EN_RESET, GPIO0_BOOT

## External adapter mapping note
Document this mapping in bring-up notes:
- adapter RX -> UART_TX
- adapter TX -> UART_RX
- adapter reset control -> EN_RESET (optional)
- adapter boot control -> GPIO0_BOOT (optional)

## Architecture rules you must keep
1. Keep EN_RESET and GPIO0_BOOT active-low behavior unchanged.
2. Do not rename UART_TX/UART_RX nets.
3. Leave BOOT and RESET buttons on ESP32 sheet for manual flashing fallback.
4. Do not add on-board USB-UART circuitry in this sheet.

## Root-sheet hookup check
In the root sheet, make sure:
1. ESP32S3Core UART_TX/UART_RX connect to ExternalProgrammingHeader UART_TX/UART_RX.
2. ESP32S3Core EN_RESET/GPIO0_BOOT connect to ExternalProgrammingHeader EN_RESET/GPIO0_BOOT.
3. 3V3 and GND are provided from main rails.

## Common mistakes to avoid
1. Swapping UART_TX and UART_RX.
2. Reversing EN_RESET and GPIO0_BOOT control lines.
3. Missing pin-1 indicator or unreadable silkscreen labels.

## Final checks (2-minute checklist)
1. All six nets are present and labeled correctly.
2. Header pin order matches intended cable convention.
3. ERC has no real wiring errors for this sheet.

## Exit criteria
- External programming interface is complete and unambiguous.
- Sheet is ready for root integration.

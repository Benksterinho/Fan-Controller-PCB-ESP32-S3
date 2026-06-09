# TestPoints How-To (Beginner Version)

Target sheet: TestPoints

## What this sheet does
This sheet adds easy probe points for bring-up and troubleshooting.

Think of it as:
- Power test pads: VIN_PROTECTED, 12V, 3V3, GND
- Signal test pads: boot/reset, UART, and fan control feedback

## Parts to place
This sheet is test pads only in Rev A, so there are no locked LCSC BOM lines required for capture.

## Before wiring
1. Open the root schematic.
2. If sheet TestPoints does not exist, create a hierarchical sheet named TestPoints and set its file to TestPoints.
3. Open the TestPoints subsheet.
4. Place ten test-point symbols and name them TP1 through TP10.

## Pin-level wiring (connect this to that)
Use the Wire tool and connect exactly these nets:

1. Power test points
- TP1 -> VIN_PROTECTED
- TP2 -> 12V
- TP3 -> 3V3
- TP4 -> GND

2. Control and programming test points
- TP5 -> FAN_PWM
- TP6 -> FAN_TACH
- TP7 -> UART_TX
- TP8 -> UART_RX
- TP9 -> EN_RESET
- TP10 -> GPIO0_BOOT

3. Labeling
- Place readable text labels near each TP so probing is obvious on printouts and screenshots.

## Placement guidance (simple and practical)
1. Put power test points near board edges with enough probe clearance.
2. Keep TP4 GND in an easy-to-reach position for oscilloscope ground clips.
3. Group related digital test points together.

## Architecture rules you must keep
1. Test points are measurement taps only, not routing hubs.
2. Do not rename nets at this sheet.
3. Keep the same net names used by source sheets.

## Root-sheet hookup check
In the root sheet, make sure:
1. TestPoints sheet pins match the ten nets listed above.
2. No duplicate or misspelled net names exist.

## Common mistakes to avoid
1. Swapping UART_TX and UART_RX labels.
2. Forgetting a dedicated GND test point.
3. Creating a new net by typo instead of connecting to the existing one.

## Final checks (2-minute checklist)
1. All ten test points are present and uniquely labeled.
2. Each test point is wired to the intended net.
3. ERC has no real wiring errors for this sheet.

## Exit criteria
- Critical power and debug nets are accessible by probe.
- TestPoints sheet is ready for root integration.

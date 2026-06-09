# Power3V3 How-To (Beginner Version)

Target sheet: Power3V3

## What this sheet does
This sheet turns VIN_PROTECTED (from InputProtection) into a clean 3V3 rail using one integrated regulator module (K7803).

Use the project-local KiCad symbol `lcsc_imported:K7803-1000R3_C50396472` for U2.

Think of it as:
- Input in: VIN_PROTECTED + GND
- Regulated output out: 3V3 + GND

## Parts to place
| RefDes | Part | LCSC | Why it is here |
|---|---|---|---|
| U2 | EVISUN-K7803-1000R3 | C50396472 | Makes 3.3V from VIN_PROTECTED |
| C33_IN_BULK | 100uF 50V electrolytic | C72480 | Bulk smoothing on module input |
| C33_IN_BYP | 10uF 50V X5R ceramic, 1210 | C380537 | Input bypass |
| C33_OUT_BULK | 100uF 10V electrolytic | C2887276 | Bulk output reserve for load spikes |
| C33_OUT_BYP | 10uF 16V X5R ceramic, 0603 | C92487 | Output bypass |

## Before wiring
1. Open the root schematic.
2. If Power3V3 sheet does not exist, create hierarchical sheet Power3V3 and set file Power3V3.
3. Open the Power3V3 subsheet.
4. Place U2, C33_IN_BULK, C33_IN_BYP, C33_OUT_BULK, and C33_OUT_BYP.

## Pin-level wiring (connect this to that)
Use the Wire tool and connect exactly these nets:

1. VIN_PROTECTED net (input side)
- U2 VIN pin -> VIN_PROTECTED label
- C33_IN_BULK positive pin -> VIN_PROTECTED
- C33_IN_BYP pin 1 -> VIN_PROTECTED

2. GND net (shared return)
- U2 GND pin -> GND label
- C33_IN_BULK negative pin -> GND
- C33_IN_BYP pin 2 -> GND
- C33_OUT_BULK negative pin -> GND
- C33_OUT_BYP pin 2 -> GND

3. 3V3 net (output side)
- U2 VOUT pin -> 3V3 label
- C33_OUT_BULK positive pin -> 3V3
- C33_OUT_BYP pin 1 -> 3V3

Important polarity note:
- Electrolytics must be oriented correctly.
- C33_IN_BULK: + to VIN_PROTECTED, - to GND.
- C33_OUT_BULK: + to 3V3, - to GND.

## Placement guidance (simple and practical)
1. Keep C33_IN_BULK and C33_IN_BYP close to U2 VIN/GND pins.
2. Keep C33_OUT_BULK and C33_OUT_BYP close to U2 VOUT/GND pins.
3. Keep all capacitor-to-module wires short.
4. Use the same GND net everywhere in this sheet.

## Architecture rules you must keep
1. Feed U2 directly from VIN_PROTECTED.
2. Do not feed U2 from 12V.
3. Do not add inductor, catch diode, or feedback resistors.
4. This design uses integrated K78xx modules only.

## Root-sheet hookup check
In the root sheet, confirm:
1. VIN_PROTECTED enters Power3V3 sheet.
2. 3V3 leaves this sheet to ESP32S3Core and other consumers.
3. GND is common and consistent across all sheets.

## Common mistakes to avoid
1. Reversing electrolyte capacitor polarity.
2. Forgetting to connect output capacitors to 3V3.
3. Writing net name 3.3V or V3P3 instead of 3V3.
4. Accidentally using 12V as input to U2.

## Final checks (2-minute checklist)
1. ERC has no real wiring errors.
2. U2 VIN is on VIN_PROTECTED.
3. U2 VOUT, C33_OUT_BULK+, and C33_OUT_BYP pin 1 are on 3V3 and the net name is exactly 3V3.
4. All capacitor negative sides are on GND.
5. No discrete buck converter parts exist in this sheet.

## Exit criteria
- 3V3 net is exported and cleanly labeled.
- Sheet follows the module-based Rev A architecture.
- Sheet passes ERC after intentional warnings are reviewed.

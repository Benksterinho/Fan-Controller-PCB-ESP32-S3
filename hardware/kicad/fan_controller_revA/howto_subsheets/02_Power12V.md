# Power12V How-To (Beginner Version)

Target sheet: Power12V

## What this sheet does
This sheet turns VIN_PROTECTED (from InputProtection) into a clean 12V rail using one integrated regulator module (K7812).

Think of it as:
- Input in: VIN_PROTECTED + GND
- Regulated output out: 12V + GND

## Parts to place
| RefDes | Part | LCSC | Why it is here |
|---|---|---|---|
| U1 | K7812-1000R3 | C5369648 | Makes 12V from VIN_PROTECTED |
| C12_IN_BULK | 100uF 50V electrolytic | C72480 | Bulk input smoothing |
| C12_IN_BYP | 1uF 50V X7R 0603 | C15849 | Input bypass |
| C12_OUT_BULK | 47uF 25V electrolytic | C3029614 | Output bulk for fan startup and PWM load transients |
| C12_OUT_BYP | 100nF 50V X7R 0402 | C307331 | Output bypass at regulator VOUT |

## Before wiring
1. Open the root schematic.
2. If sheet Power12V does not exist, create a hierarchical sheet named Power12V and set its file to Power12V.
3. Open the Power12V subsheet.
4. Place U1, C12_IN_BULK, C12_IN_BYP, C12_OUT_BULK, and C12_OUT_BYP.

## Pin-level wiring (connect this to that)
Use the Wire tool and connect exactly these nets:

1. VIN_PROTECTED net
- U1 VIN pin -> VIN_PROTECTED label
- C12_IN_BULK positive pin -> VIN_PROTECTED
- C12_IN_BYP pin 1 -> VIN_PROTECTED

2. GND net
- U1 GND pin -> GND label
- C12_IN_BULK negative pin -> GND
- C12_IN_BYP pin 2 -> GND

3. 12V output net
- U1 VOUT pin -> 12V label
- C12_OUT_BULK positive pin -> 12V
- C12_OUT_BYP pin 1 -> 12V

4. Output return to GND
- C12_OUT_BULK negative pin -> GND
- C12_OUT_BYP pin 2 -> GND

Important polarity note:
- Electrolytics must be oriented correctly.
- C12_IN_BULK: + to VIN_PROTECTED, - to GND.

## Placement guidance (simple and practical)
1. Put C12_IN_BULK and C12_IN_BYP physically close to U1 VIN and GND pins.
2. Put C12_OUT_BULK and C12_OUT_BYP physically close to U1 VOUT and GND pins.
3. Keep the VIN_PROTECTED and GND wires short between U1 and both input capacitors.
4. Keep output-cap wiring short from U1 VOUT to C12_OUT_BULK/C12_OUT_BYP and then to GND.
5. Keep 12V trace short from U1 VOUT to the 12V label.
6. Use the same GND net everywhere in this sheet.

## Architecture rules you must keep
1. Feed U1 directly from VIN_PROTECTED.
2. Do not cascade this stage from another rail.
3. Do not add inductor, catch diode, or feedback divider.
4. This design uses integrated K78xx modules only.

## Root-sheet hookup check
In the root sheet, make sure:
1. VIN_PROTECTED enters this Power12V sheet.
2. 12V leaves this sheet toward the fan stage.
3. GND is shared consistently across sheets.

## Common mistakes to avoid
1. Reversing C12_IN_BULK polarity.
2. Forgetting to wire C12_IN_BYP to both VIN_PROTECTED and GND.
3. Naming output net something else (must be exactly 12V).
4. Mixing up U1 pin names; always follow the K7812 symbol pin names VIN, GND, VOUT.

## Final checks (2-minute checklist)
1. ERC runs with no real wiring errors.
2. U1 VIN is on VIN_PROTECTED, not on raw VIN.
3. U1 VOUT goes to 12V and the net name is exactly 12V.
4. U1 GND and all capacitor returns are on GND.
5. C12_IN_BULK polarity is correct.

## Exit criteria
- 12V net is generated and exported.
- Only module-based architecture is used (no discrete buck power stage).
- Sheet passes ERC after intentional warnings are reviewed.

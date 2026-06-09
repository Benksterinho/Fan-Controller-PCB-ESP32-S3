# OptionalExpansionHeader How-To (Beginner Version)

Target sheet: OptionalExpansionHeader

## What this sheet does
This sheet exposes a protected 3V3/GND expansion header and a small set of safe GPIOs for future add-ons.

Think of it as:
- Power out: 3V3 through a polyfuse + GND
- GPIO out: a selected set of non-boot-critical ESP32 pins

## Parts to place
| RefDes | Part | LCSC | Why it is here |
|---|---|---|---|
| J_EXP | PZ2.54-2X5P-H25 | C42431818 | Expansion header connector |
| R_EXP1 R_EXP2 R_EXP3 R_EXP4 R_EXP5 R_EXP6 R_EXP7 R_EXP8 | 33R 0603 | C2909394 | Series protection/damping on expansion GPIO lines |
| F_EXP | 0805L050WR | C207022 | Polyfuse on exported 3V3 power |

## Before wiring
1. Open the root schematic.
2. If sheet OptionalExpansionHeader does not exist, create a hierarchical sheet named OptionalExpansionHeader and set its file to OptionalExpansionHeader.
3. Open the OptionalExpansionHeader subsheet.
4. Place J_EXP, F_EXP, and eight series resistors R_EXP1 to R_EXP8.

## Pin-level wiring (connect this to that)
Use the Wire tool and connect exactly these nets:

1. Expansion power pins
- 3V3 -> F_EXP -> J_EXP power pin
- GND -> J_EXP ground pin

2. Expansion GPIO signals
- Use only these Rev A expansion nets:
- GPIO17
- GPIO18
- GPIO19
- GPIO20
- GPIO21
- GPIO38
- GPIO39
- GPIO41
- Route each signal through one 33R resistor before J_EXP pin

3. Required labels
- Add net labels for each exported GPIO line
- Add hierarchical labels for all exported expansion nets

## Placement guidance (simple and practical)
1. Put J_EXP near board edge for easy access.
2. Keep F_EXP close to the 3V3 entry to the header power pin.
3. Keep resistor bank close to the header for clean routing.

## Architecture rules you must keep
1. Do not export boot-critical or reserved GPIO pins.
2. Keep 3V3 export behind F_EXP polyfuse.
3. Preserve exact net naming from project GPIO map.

## Root-sheet hookup check
In the root sheet, make sure:
1. 3V3 and GND are connected from system rails.
2. Expansion GPIO nets originate from the intended ESP32S3Core nets.
3. No duplicate or conflicting GPIO exports are created.

## Common mistakes to avoid
1. Exporting GPIO0, GPIO3, GPIO45, or GPIO46.
2. Forgetting polyfuse on expansion 3V3.
3. Bypassing series resistors on expansion GPIO lines.

## Final checks (2-minute checklist)
1. J_EXP has fused 3V3 and GND pins wired.
2. Exactly eight approved GPIO lines are exported through 33R resistors.
3. Net labels and hierarchical labels match the project naming.
4. ERC has no real wiring errors for this sheet.

## Exit criteria
- Expansion header power and GPIO lines are complete and safe.
- Exported nets match Rev A constraints.
- Sheet is ready for root integration.

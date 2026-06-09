# Fan4Wire How-To (Beginner Version)

Target sheet: Fan4Wire

## What this sheet does
This sheet connects a standard 4-wire fan: 12V power, PWM control, and tach feedback to the ESP32.

Think of it as:
- Power in: 12V + GND
- Control in: FAN_PWM from ESP32
- Feedback out: FAN_TACH back to ESP32

## Parts to place
| RefDes | Part | LCSC | Why it is here |
|---|---|---|---|
| J_FAN | Conn_PinHeader_2.54mm_1x4 (right-angle) | C32713263 (hanxia HX PZ2.54-1x4P WZ) | 4-wire fan connector |
| Q1 | MMBT3904_Q1 | C181119 | Open-collector style PWM sink stage |
| R_BASE | 2k2 0603 | C4190 | Limits base current into Q1 |
| R_BPD | 100k 0603 | C25803 | Pull-down on base to keep Q1 off at reset |
| R_TACH_PU | 10k 0603 | C98220 | Pull-up for open-collector tach signal |
| R_TACH_SERIES | 1k 0603 | C22548 | Series protection on tach line to MCU |
| D_TACH_CLAMP | onsemi-MBR0520LT1G | C23848 | Tach clamp to 3V3 rail |
| C_FAN_BULK | 47uF 25V | C3029614 | Bulk capacitor on 12V fan rail |
| C_FAN_HF | 100nF 25V X7R 0402 | C307331 | High-frequency bypass on 12V fan rail |

## Before wiring
1. Open the root schematic.
2. If sheet Fan4Wire does not exist, create a hierarchical sheet named Fan4Wire and set its file to Fan4Wire.
3. Open the Fan4Wire subsheet.
4. Place J_FAN, Q1, R_BASE, R_BPD, R_TACH_PU, R_TACH_SERIES, D_TACH_CLAMP, C_FAN_BULK, and C_FAN_HF.

## Pin-level wiring (connect this to that)
Use the Wire tool and connect exactly these nets:

1. Fan power path
- J_FAN 12V pin -> 12V net
- J_FAN GND pin -> GND net
- C_FAN_BULK between 12V and GND
- C_FAN_HF between 12V and GND

2. PWM control path
- FAN_PWM net -> R_BASE -> Q1 base
- Q1 emitter -> GND
- Q1 collector -> J_FAN PWM pin
- R_BPD between Q1 base and GND

3. Tach feedback path
- J_FAN tach pin -> tach node
- R_TACH_PU from tach node to 3V3
- R_TACH_SERIES from tach node to FAN_TACH net
- D_TACH_CLAMP from FAN_TACH side to 3V3
- D_TACH_CLAMP orientation: cathode to 3V3, anode to FAN_TACH

4. Required labels
- Add hierarchical labels 12V, 3V3, GND, FAN_PWM, and FAN_TACH.

## Placement guidance (simple and practical)
1. Place C_FAN_BULK and C_FAN_HF close to J_FAN power pins.
2. Place Q1, R_BASE, and R_BPD close together.
3. Keep FAN_TACH conditioning parts close to each other and away from noisy 12V paths.
4. Keep GND return paths short for Q1 and fan power decoupling.

## Architecture rules you must keep
1. FAN_PWM is driven through Q1 stage, not directly to the fan pin.
2. FAN_TACH must be pulled up to 3V3.
3. Keep tach clamp direction fixed: cathode to 3V3, anode to FAN_TACH.
4. Do not power the fan from 3V3.

## Root-sheet hookup check
In the root sheet, make sure:
1. 12V enters Fan4Wire from Power12V.
2. 3V3 enters Fan4Wire for tach pull-up only.
3. FAN_PWM comes from ESP32S3Core.
4. FAN_TACH returns to ESP32S3Core.

## Common mistakes to avoid
1. Reversing tach clamp diode orientation.
2. Forgetting R_BPD on Q1 base.
3. Connecting fan power to the wrong rail.
4. Omitting local 12V decoupling at the fan connector.

## Final checks (2-minute checklist)
1. J_FAN power pins are correctly on 12V and GND.
2. Q1 stage is wired as base/emitter/collector path for PWM sink control.
3. Tach node has pull-up, series resistor, and clamp diode.
4. FAN_PWM and FAN_TACH labels are present and correct.
5. ERC has no real wiring errors for this sheet.

## Exit criteria
- Fan connector, PWM drive path, and tach return path are complete.
- Exported nets match the project naming convention.
- Sheet is ready for root integration.

# DisplayHeader_ST7789 How-To (Beginner Version)

Target sheet: DisplayHeader_ST7789

## What this sheet does
This sheet exposes a simple display header for an ST7789-style SPI display, with series resistors and local decoupling.

Think of it as:
- Power in: 3V3 + GND
- Signals in/out: SPI and display control lines
- Connector out: J_DISP pins for the display cable/header

## Parts to place
| RefDes | Part | LCSC | Why it is here |
|---|---|---|---|
| J_DISP | Conn_PinHeader_2.54mm_1x8 (right-angle THT) | C32713267 | Display header interface with side cable exit |
| R_SCK R_MOSI R_CS R_DC R_RST | 33R 0603 | C2909394 | Series damping on fast digital lines |
| C_DISP_VCC | 100nF 10V 0402 | C307331 | Local display header bypass capacitor |
| Q_BL | MMBT3904 NPN SOT-23 | C181119 | Open-collector backlight sink stage |
| R_BL_BASE | 2.2k 0603 | C4190 | Base current limit for Q_BL |
| R_BL_PD | 100k 0603 | C25803 | Base pull-down so BL stage defaults off |

## Before wiring
1. Open the root schematic.
2. If sheet DisplayHeader_ST7789 does not exist, create a hierarchical sheet named DisplayHeader_ST7789 and set its file to DisplayHeader_ST7789.
3. Open the DisplayHeader_ST7789 subsheet.
4. Place J_DISP, R_SCK, R_MOSI, R_CS, R_DC, R_RST, C_DISP_VCC, Q_BL, R_BL_BASE, and R_BL_PD.

## Pin-level wiring (connect this to that)
Use the Wire tool and connect exactly these nets:

1. Power pins
- J_DISP VCC pin -> 3V3
- J_DISP GND pin -> GND
- C_DISP_VCC between 3V3 and GND near J_DISP

2. SPI/control lines through series resistors
- SPI_SCK -> R_SCK -> J_DISP SCK pin
- SPI_MOSI -> R_MOSI -> J_DISP MOSI pin
- DISP_CS -> R_CS -> J_DISP CS pin
- DISP_DC -> R_DC -> J_DISP DC pin
- DISP_RST -> R_RST -> J_DISP RST pin

3. MISO policy for Rev A
- Leave J_DISP MISO/SDO pin unconnected (NC)

4. Backlight open-collector stage (fan-style)
- DISP_BL -> R_BL_BASE -> Q_BL base
- Q_BL base -> R_BL_PD -> GND
- Q_BL emitter -> GND
- Q_BL collector -> J_DISP BLK pin
- Use ESP32 GPIO40 PWM on DISP_BL to dim the backlight through Q_BL sink action

5. Required labels
- Add hierarchical labels: 3V3, GND, SPI_SCK, SPI_MOSI, DISP_CS, DISP_DC, DISP_RST, DISP_BL

## Placement guidance (simple and practical)
1. Place series resistors close to the header or in a tight grouped row.
2. Place C_DISP_VCC close to J_DISP VCC/GND pins.
3. Keep SPI/control traces short and parallel routing clean.

## Architecture rules you must keep
1. Use the fixed display net names from the project mapping.
2. Do not implement SPI_MISO on Rev A.
3. Do not power the display from 12V.

## Root-sheet hookup check
In the root sheet, make sure:
1. SPI and control nets come from ESP32S3Core.
2. 3V3 and GND are provided from the power/ground backbone.
3. Net names match exactly between this sheet and ESP32S3Core.

## Common mistakes to avoid
1. Bypassing the series resistors by wiring nets directly to J_DISP pins.
2. Mixing DISP_DC and DISP_RST labels.
3. Forgetting local VCC bypass at the header.
4. Accidentally routing display MISO/SDO into the MCU on Rev A.
5. Wiring BLK directly to DISP_BL and bypassing Q_BL.

## Final checks (2-minute checklist)
1. All required SPI/control lines are wired through the correct series resistors.
2. J_DISP has 3V3 and GND plus local 100nF bypass.
3. Label names match project conventions exactly.
4. ERC has no real wiring errors for this sheet.

## Exit criteria
- Display header and control interface are complete and labeled.
- Rev A no-MISO decision is explicit.
- Sheet is ready for root integration.

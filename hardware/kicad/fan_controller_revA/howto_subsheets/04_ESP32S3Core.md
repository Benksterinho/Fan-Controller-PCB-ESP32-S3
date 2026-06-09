# ESP32S3Core How-To (Beginner Version)

Target sheet: ESP32S3Core

## What this sheet does
This sheet places the ESP32-S3 module and gives it the minimum support parts it needs to boot, reset, talk to USB programming, control the fan, and drive the display.

Think of it as:
- Power in: 3V3 + GND
- Control core: ESP32-S3 module + decoupling + boot/reset parts
- Signals out: FAN_PWM, FAN_TACH, SPI_SCK, SPI_MOSI, DISP_CS, DISP_DC, DISP_RST, DISP_BL, UART_TX, UART_RX, EN_RESET, GPIO0_BOOT

Important capture rule:
- Build this sheet after the power sheets and after you already know the net names you want to export.

## Parts to place
| RefDes | Part | LCSC | Why it is here |
|---|---|---|---|
| U3 | ESP32-S3-WROOM-1-N16R8 | C2913202 | Main MCU and wireless module |
| C_DEC | 100nF 10V X7R 0402 | C307331 | Local high-frequency decoupling on the 3V3 rail |
| C_BULK | 22uF 16V X5R 1206 | C90146 | Local bulk decoupling for ESP32 current bursts |
| R_EN | 10k 0603 | C99198 | Pulls EN high for normal operation |
| R_BOOT | 10k 0603 | C99198 | Pulls GPIO0 high for normal boot |
| R_JTAG | 10k 0603 | C99198 | Pulls GPIO3 HIGH to select USB Serial/JTAG source and avoid floating strapping state |
| C_EN | 1uF 0603 | C15849 | Reset RC capacitor from EN to GND (Espressif typical EN RC with 10k pull-up) |
| SW_RESET | TL3342F160QG | C2886898 | Manual reset button |
| SW_BOOT | TL3342F160QG | C2886898 | Manual BOOT/download button |

## Before wiring
1. Open the root schematic.
2. If sheet ESP32S3Core does not exist, create a hierarchical sheet named ESP32S3Core and set its file to ESP32S3Core.
3. Open the ESP32S3Core subsheet.
4. Place U3, C_DEC, C_BULK, R_EN, R_BOOT, C_EN, SW_RESET, and SW_BOOT.
5. Do not place extra pull resistors, inductors, or experimental boot parts on this sheet.

## Pin-level wiring (connect this to that)
Use the Wire tool and connect exactly these nets:

1. 3V3 power net
- U3 3V3 supply pin -> 3V3 label
- C_BULK pin 1 -> 3V3
- C_DEC pin 1 -> 3V3
- R_EN one side -> 3V3
- R_BOOT one side -> 3V3
- R_JTAG one side -> 3V3

2. GND net
- All U3 GND pins -> GND label
- C_BULK pin 2 -> GND
- C_DEC pin 2 -> GND
- C_EN pin 2 -> GND
- SW_RESET one side -> GND
- SW_BOOT one side -> GND

3. EN reset net
- U3 EN pin -> EN_RESET net (input)
- R_EN other side -> EN_RESET
- C_EN pin 1 -> EN_RESET
- SW_RESET other side -> EN_RESET
- Add hierarchical label EN_RESET on this net (input on ESP32S3Core)

4. GPIO0 boot net
- U3 IO0 (GPIO0) pin -> GPIO0_BOOT net (input)
- R_BOOT other side -> GPIO0_BOOT
- SW_BOOT other side -> GPIO0_BOOT
- Add hierarchical label GPIO0_BOOT on this net (input on ESP32S3Core)

5. UART programming net labels
- U3 TXD0 (GPIO43) -> UART_TX label (output)
- U3 RXD0 (GPIO44) -> UART_RX label (input)

6. Fan-control net labels
- U3 IO4 (GPIO4) -> FAN_TACH label (input)
- U3 IO5 (GPIO5) -> FAN_PWM label (output)

7. Display SPI/control net labels
- U3 IO6 (GPIO6) -> SPI_SCK label (output)
- U3 IO7 (GPIO7) -> SPI_MOSI label (output)
- U3 IO9 (GPIO9) -> DISP_DC label (output)
- U3 IO10 (GPIO10) -> DISP_CS label (output)
- U3 IO16 (GPIO16) -> DISP_RST label (output)
- U3 IO40 (GPIO40) -> DISP_BL label (output)

8. GPIO3 strapping net
- U3 IO3 (GPIO3) -> R_JTAG other side
- Keep this net local to strapping support only (no functional load routing)

9. Intentionally unused pins
- Add no-connect flags only to pins you are intentionally leaving unused.
- Do not add net labels to reserved module pins.

## Label direction cheat sheet (for sheet pins)
Use this when setting hierarchical sheet-pin direction in KiCad.

1. Symbol naming reminder
- IOx on the ESP32 symbol means GPIOx.
- Example: GPIO0_BOOT connects to IO0 (not IO1).

2. Reset/boot control nets
- EN_RESET: Input on ESP32S3Core, Output on ExternalProgrammingHeader
- GPIO0_BOOT: Input on ESP32S3Core, Output on ExternalProgrammingHeader

3. UART nets
- UART_TX: Output on ESP32S3Core, Input on ExternalProgrammingHeader
- UART_RX: Input on ESP32S3Core, Output on ExternalProgrammingHeader

4. Fan nets
- FAN_PWM: Output on ESP32S3Core, Input on Fan4Wire
- FAN_TACH: Input on ESP32S3Core, Output on Fan4Wire

5. Display SPI/control nets
- SPI_SCK: Output on ESP32S3Core, Input on DisplayHeader_ST7789
- SPI_MOSI: Output on ESP32S3Core, Input on DisplayHeader_ST7789
- DISP_CS: Output on ESP32S3Core, Input on DisplayHeader_ST7789
- DISP_DC: Output on ESP32S3Core, Input on DisplayHeader_ST7789
- DISP_RST: Output on ESP32S3Core, Input on DisplayHeader_ST7789
- DISP_BL: Output on ESP32S3Core, Input on DisplayHeader_ST7789

If ERC complains in a mixed-control net, set both sides to Bidirectional as a practical fallback.

## Placement guidance (simple and practical)
1. Put U3 near a board edge so the antenna side can later keep clear of copper and metal.
2. Put C_DEC and C_BULK as close as possible to the U3 power pin area.
3. Put R_EN and C_EN close to the EN pin.
4. Put R_BOOT close to the GPIO0 pin.
5. Put SW_RESET and SW_BOOT where they are easy to press during bring-up.
6. Keep 3V3 and GND connections short between U3 and its local capacitors.

## Architecture rules you must keep
1. This sheet is powered only from 3V3, not from VIN_PROTECTED or 12V.
2. EN_RESET must be exported to ExternalProgrammingHeader for external reset/program control.
3. GPIO0_BOOT must be exported to ExternalProgrammingHeader for external boot/program control.
4. Do not route GPIO0, GPIO3, GPIO45, or GPIO46 to other functional circuits.
5. Use the fixed Rev A GPIO mapping:
- GPIO4 -> FAN_TACH
- GPIO5 -> FAN_PWM
- GPIO6 -> SPI_SCK
- GPIO7 -> SPI_MOSI
- GPIO9 -> DISP_DC
- GPIO10 -> DISP_CS
- GPIO16 -> DISP_RST
- GPIO40 -> DISP_BL
- GPIO43 -> UART_TX
- GPIO44 -> UART_RX

## Root-sheet hookup check
In the root sheet, make sure:
1. 3V3 enters this ESP32S3Core sheet from Power3V3.
2. EN_RESET and GPIO0_BOOT leave this sheet toward ExternalProgrammingHeader.
3. UART_TX and UART_RX leave this sheet toward ExternalProgrammingHeader.
4. FAN_PWM and FAN_TACH connect toward Fan4Wire.
5. SPI_SCK, SPI_MOSI, DISP_CS, DISP_DC, DISP_RST, and DISP_BL connect toward DisplayHeader_ST7789.

## Common mistakes to avoid
1. Forgetting local decoupling because the 3V3 regulator already has capacitors. The ESP32 still needs local caps on this sheet.
2. Mixing up UART direction. U3 GPIO43 is UART_TX and U3 GPIO44 is UART_RX.
3. Naming the boot/reset nets something different. They must be exactly EN_RESET and GPIO0_BOOT.
4. Putting other loads directly on GPIO0. Keep it only for the pull-up, button, and ExternalProgrammingHeader control path.
5. Reusing reserved or strapping pins for other functions.

## Final checks (2-minute checklist)
1. U3 power pins are on 3V3 and all ground pins are on GND.
2. C_BULK and C_DEC are connected between 3V3 and GND.
3. EN_RESET has a 10k pull-up to 3V3, a 1uF capacitor to GND, and a reset button to GND.
4. GPIO0_BOOT has a 10k pull-up to 3V3 and a BOOT button to GND.
5. GPIO labels match the fixed Rev A net names exactly.
6. GPIO3 has R_JTAG (10k) to 3V3 and is not routed to functional loads.
7. Reserved pins are not accidentally exported.
8. ERC runs with no real wiring errors after intentional warnings are reviewed.

## Exit criteria
- ESP32 module power and boot/reset support are complete.
- All required Rev A signal nets are exported with the correct names.
- Sheet is ready to connect to Fan4Wire, DisplayHeader_ST7789, and ExternalProgrammingHeader.

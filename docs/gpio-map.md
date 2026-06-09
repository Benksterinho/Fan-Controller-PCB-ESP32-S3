# ESP32-S3 GPIO Usage — Rev A Fan Controller

Module: ESP32-S3-WROOM-1-N16R8

---

## Assigned GPIOs

| GPIO | Net | Direction | Function | Sheet |
|------|-----|-----------|----------|-------|
| GPIO4 | FAN_TACH | IN | Tach pulse from fan (freq count) | Fan4Wire |
| GPIO5 | FAN_PWM | OUT | 25kHz PWM → Q1 base (LEDC peripheral) | Fan4Wire |
| GPIO6 | SPI_SCK | OUT | SPI clock to ST7789 | DisplayHeader_ST7789 |
| GPIO7 | SPI_MOSI | OUT | SPI data to ST7789 | DisplayHeader_ST7789 |
| GPIO9 | DISP_DC | OUT | Display data/command | DisplayHeader_ST7789 |
| GPIO10 | DISP_CS | OUT | Display chip select | DisplayHeader_ST7789 |
| GPIO16 | DISP_RST | OUT | Display reset | DisplayHeader_ST7789 |
| GPIO40 | DISP_BL | OUT | Display backlight enable/PWM | DisplayHeader_ST7789 |
| GPIO43 | UART_TX | OUT | UART0 TX → external downloader RX | ExternalProgrammingHeader |
| GPIO44 | UART_RX | IN | UART0 RX ← external downloader TX | ExternalProgrammingHeader |

## Strapping Pins (do not route to any load)

| GPIO | State at boot | How |
|------|--------------|-----|
| GPIO0 | HIGH = normal boot | 10k pull-up to 3V3; BOOT button pulls to GND |
| GPIO45 | LOW = VDD_SPI at 3.3V | 10k pull-down to GND (or module default) |
| GPIO3 | HIGH = USB Serial/JTAG source selected | 10k pull-up to 3V3 via R_JTAG |
| GPIO46 | floating = ROM log on | leave floating (module default) |

## Reserved (internal to module — never break out)

GPIO11, GPIO12, GPIO13, GPIO14, GPIO15 — Octal PSRAM interface on N16R8.

GPIO35, GPIO36, GPIO37 — internal module/PSRAM-connected lines on N16R8 variants; do not assign to external functions.

## Free for Expansion Header

After all assignments above, safe GPIOs for J_EXP:

GPIO1, GPIO2, GPIO17, GPIO18, GPIO19, GPIO20, GPIO21, GPIO38, GPIO39, GPIO41, GPIO42

Rev A J_EXP routes only these 8 GPIOs through 33Ω series resistors: GPIO17, GPIO18, GPIO19, GPIO20, GPIO21, GPIO38, GPIO39, GPIO41.

GPIO1, GPIO2, GPIO8, and GPIO42 remain available for future revisions but are not routed to J_EXP in Rev A.

---

## Boot/Reset Nets (route on ESP32S3Core sheet)

| Net | Pin | Components |
|-----|-----|-----------|
| EN_RESET | EN (pin 3) | R_EN 10k to 3V3; C_EN 1uF to GND; SW_RESET button to GND |
| GPIO0_BOOT | GPIO0 (pin 27) | R_BOOT 10k to 3V3; SW_BOOT button to GND |

EN_RESET and GPIO0_BOOT are exposed on the external programming header for external auto-program control. Manual BOOT/RESET flashing remains available via on-board buttons.

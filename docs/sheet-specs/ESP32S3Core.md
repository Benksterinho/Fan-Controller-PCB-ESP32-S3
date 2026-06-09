

# Antenna Keep-Out Guidance (Critical)

**ESP32-S3-WROOM-1 PCB antenna keep-out requirements (for layout):**
- **No copper pours, traces, or fills** under the antenna section of the module (top 3.5mm × 18mm region; see module datasheet Figure 7).
- **No vias, test points, or silkscreen** in the keep-out zone.
- **No metal objects within 3mm** of antenna edges in the horizontal plane (including mounting hardware, shields, or connectors).
- **Place module at board edge** so antenna overhangs or is near the edge, per Espressif hardware design guidelines.
- **In KiCad:**
	- Draw a keep-out zone on the top copper, top mask, and top silkscreen layers for the antenna region.
	- Add a board edge keep-out annotation and a note in the layout file: "ESP32 antenna keep-out: no copper, no traces, no vias, no silkscreen, no test points."
	- Confirm keep-out is respected in DRC and final Gerbers.

# Sheet Spec: ESP32S3Core

**KiCad sheet:** `ESP32S3Core`  
**Purpose:** ESP32-S3 module mounting, boot/reset network, decoupling, GPIO assignment, and strapping pin safety.  
**Input nets:** `3V3`, `GND`  
**Output/exposed nets:** `UART_TX`, `UART_RX`, `GPIO0_BOOT`, `EN_RESET`, plus all GPIO signal nets to downstream sheets.  
**Reference:** ESP32-S3-WROOM-1 datasheet (Espressif DS0003), ESP32-S3 Technical Reference Manual

---

## Module Selection

| Parameter | Value |
|-----------|-------|
| Module | ESP32-S3-WROOM-1-N16R8 (preferred) |
| Flash | 16MB external (N16) |
| PSRAM | 8MB Octal (R8) — required for CircuitPython heap |
| Antenna | On-module PCB antenna (no external RF components needed) |
| Package | SMD castellated edge, 2.54mm pitch |
| Footprint | ESP32-S3-WROOM-1 standard (52 pins) |
| Fallback | ESP32-S3-WROOM-1-N8R8 (8MB flash, same footprint) |

**Critical:** Do not substitute ESP32-S3-MINI — different footprint. Do not use ESP32-S3 bare die (requires RF layout expertise).

---

## Power Supply Pins

| Module pin | Net | Decoupling |
|------------|-----|------------|
| 3V3 (pin 2) | 3V3 | 10µF + 100nF MLCC at pin, on this sheet |
| GND (pins 1, 40, 41) | GND | All connected to ground plane |
| GND (EP / thermal pad) | GND | If exposed — solder to ground copper |

**Decoupling capacitors:**
- 1× 10µF MLCC, 10V, 0805, close to pin 2
- 1× 100nF MLCC, 10V, 0402, as close as possible to pin 2

These are in addition to any bulk cap on the 3V3 rail from Power3V3. Module datasheet requires local decoupling.

---

## Strapping Pins — Mandatory Safety Rules

Strapping pins determine boot mode and peripheral mapping at power-on. They are sampled once at reset. Wrong states = broken firmware uploads or non-boot.

| GPIO | Strapping Function | Required State | Implementation |
|------|--------------------|----------------|----------------|
| GPIO0 | Download mode vs normal boot | HIGH = normal boot; LOW = download mode | 10kΩ pullup to 3V3; BOOT button pulls to GND |
| GPIO3 | JTAG source selection | HIGH = USB Serial/JTAG selected for deterministic behavior | 10kΩ pullup to 3V3 (`R_JTAG`), no functional load on net |
| GPIO45 | VDD_SPI voltage config | LOW = 3.3V (correct for this board) | 10kΩ pulldown to GND or leave at module default (confirm module default via datasheet §4.4) |
| GPIO46 | ROM messages | HIGH = enabled; LOW = suppressed | Leave at module default (internal pulldown); do not drive |

**Rules:**
- GPIO0, GPIO3, GPIO45, GPIO46 must NOT be routed to expansion headers or load circuits.
- GPIO0 is shared with BOOT button — no other load on this net except the 10kΩ pullup, external programming control path, and the BOOT button.
- Strapping pins are sampled at EN (reset) rising edge. Boot button footprint must short GPIO0 to GND.

---

## Boot/Reset Network

### EN (Reset) Pin
| Component | Value | Notes |
|-----------|-------|-------|
| R_EN | 10kΩ | Pullup from EN to 3V3 |
| C_EN | 1uF, 0603 | EN to GND — recommended typical value for clean power-on reset with 10k pull-up |
| RESET button | Momentary NO pushbutton | Shorts EN to GND via 0Ω (direct), footprint on board |
| EN_RTS | From ExternalProgrammingHeader sheet | External programmer reset control drives EN_RESET (active-low), with on-board RESET button as manual fallback |

External programming control wiring:
- Reset control line → EN_RESET (active-low)
- Boot control line → GPIO0_BOOT (active-low)

The active Rev A path is external via the `ExternalProgrammingHeader` sheet. Manual BOOT and RESET buttons remain valid for flashing.

### BOOT (Download Mode) Pin
| Component | Value | Notes |
|-----------|-------|-------|
| R_BOOT | 10kΩ | Pullup from GPIO0 to 3V3 |
| BOOT button | Momentary NO pushbutton | Shorts GPIO0 to GND |

Do not add a mandatory capacitor from GPIO0 to GND in Rev A. If a footprint is reserved for experiments, keep it DNP by default so it does not interfere with the Espressif auto-program timing.

---

## GPIO Assignments

### Used GPIOs (assigned on this sheet, nets exposed to other sheets)

| GPIO | Net name | Function | Destination sheet |
|------|----------|----------|------------------|
| GPIO4 | FAN_TACH | Tachometer input | Fan4Wire |
| GPIO5 | FAN_PWM | 25kHz PWM output (LEDC) | Fan4Wire |
| GPIO6 | SPI_SCK | SPI clock for display | DisplayHeader_ST7789 |
| GPIO7 | SPI_MOSI | SPI MOSI for display | DisplayHeader_ST7789 |
| GPIO9 | DISP_DC | Display D/C (data/command) | DisplayHeader_ST7789 |
| GPIO10 | DISP_CS | Display chip select | DisplayHeader_ST7789 |
| GPIO16 | DISP_RST | Display reset | DisplayHeader_ST7789 |
| GPIO40 | DISP_BL | Display backlight enable/PWM | DisplayHeader_ST7789 |
| GPIO43 | UART_TX | UART0 TX to external downloader | ExternalProgrammingHeader |
| GPIO44 | UART_RX | UART0 RX from external downloader | ExternalProgrammingHeader |

### Reserved / Do Not Use
| GPIO | Reason |
|------|--------|
| GPIO11–GPIO15 | Internal to module (Octal PSRAM interface on N16R8) |
| GPIO35–GPIO37 | Internal to module on N16R8 variant; do not assign externally |
| GPIO0 | Strapping pin (BOOT) |
| GPIO3 | Strapping pin (JTAG) |
| GPIO45 | Strapping pin (VDD_SPI) |
| GPIO46 | Strapping pin (ROM messages) |

### Available for Expansion Header (OptionalExpansionHeader sheet)
Remaining free GPIOs after above assignments:
Safe candidates: GPIO1, GPIO2, GPIO17, GPIO18, GPIO19, GPIO20, GPIO21, GPIO38, GPIO39, GPIO41, GPIO42.

---

## Antenna Keep-Out Zone

The ESP32-S3-WROOM-1 PCB antenna extends from the top edge of the module.

**Keep-out requirements:**
- No copper pours, traces, or fills under the antenna section of the module (approximately the top 3.5mm × 18mm region per module datasheet Figure 7).
- No metal objects within 3mm of antenna edges in the horizontal plane.
- Module should be placed at board edge so antenna overhangs or is near the edge, per Espressif hardware design guidelines (ESP32-S3 Hardware Design Guidelines, Section 3).

**KiCad implementation:** Add a board edge keep-out zone annotation. Place module at board edge.

---

## Decoupling Summary (this sheet)

| Ref | Value | Package | Net | Placement |
|-----|-------|---------|-----|-----------|
| C_3V3_10U | 10µF MLCC, 10V | 0805 | 3V3 | At module VDD pin |
| C_3V3_100N | 100nF MLCC, 10V | 0402 | 3V3 | At module VDD pin, closer than 10µF |
| R_EN | 10kΩ | 0603 | EN to 3V3 | Near module |
| C_EN | 1uF | 0603 | EN to GND | Near module |
| R_BOOT | 10kΩ | 0603 | GPIO0 to 3V3 | Near module |
| SW_RESET | Tactile pushbutton | SMD, maker-friendly footprint | EN to GND | Panel edge |
| SW_BOOT | Tactile pushbutton | SMD, maker-friendly footprint | GPIO0 to GND | Panel edge |

---

## Net Boundaries (Sheet Interface)

**Inputs to this sheet:**
- `3V3` — from Power3V3
- `GND`

**Outputs / signals exposed at sheet boundary:**
- `UART_TX`, `UART_RX` → ExternalProgrammingHeader
- `EN_RESET` → ExternalProgrammingHeader
- `GPIO0_BOOT` → ExternalProgrammingHeader
- `FAN_PWM`, `FAN_TACH` → Fan4Wire
- `SPI_SCK`, `SPI_MOSI`, `DISP_CS`, `DISP_DC`, `DISP_RST`, `DISP_BL` → DisplayHeader_ST7789
- Free GPIOs → OptionalExpansionHeader

---

## Layout Rules

1. **Module placed at or near PCB edge** so antenna overhangs or clears board copper per keep-out rule.
2. **Boot/reset buttons on panel-accessible edge** — user must be able to press them for firmware updates.
3. **UART RX→TX/TX→RX** crossing must not create long loops — keep UART traces short from module to external programming header.
4. **Decoupling caps** placed as close as possible to VDD pin (under or adjacent to module).
5. **GPIO traces** for fan PWM, SPI, and UART should be kept away from module power routing and fan 12V high-current paths — minimum 3mm separation where practical.
6. **No vias through the antenna keep-out zone.**

---

## Checklist Before KiCad Capture

- [ ] Verify GPIO11-15 are locked to PSRAM in N16R8 module (check module datasheet Table 2-1)
- [ ] Verify GPIO45 strapping default for VDD_SPI in WROOM-1-N16R8 (should default LOW = 3.3V, confirm)
- [ ] Confirm GPIO4 and GPIO5 are not internally reserved in N16R8 variant
- [ ] Confirm EN/GPIO0 pull networks support both manual buttons and external programming control
- [ ] Download ESP32-S3-WROOM-1 KiCad footprint from Espressif component library or UltraLibrarian before starting capture

---

## Dependencies

- **Depends on:** `Power3V3` (3V3 net), `InputProtection` (GND, board-level supply stable)
- **Consumed by:** `Fan4Wire`, `DisplayHeader_ST7789`, `ExternalProgrammingHeader`, `OptionalExpansionHeader`

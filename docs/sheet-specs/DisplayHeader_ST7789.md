# Sheet Spec: DisplayHeader_ST7789

**KiCad sheet:** `DisplayHeader_ST7789`  
**Purpose:** 2.54mm pin header for ST7789 SPI display module, series damping resistors, and display reset/backlight lines.  
**Input nets:** `3V3`, `GND`, `SPI_SCK`, `SPI_MOSI`, `DISP_CS`, `DISP_DC`, `DISP_RST`, `DISP_BL` (all from ESP32S3Core)  
**Connector output:** J_DISP — 8-pin 2.54mm right-angle through-hole header

---

## Display Module Compatibility

Target: generic ST7789 SPI display module (1.3", 1.54", or 2.0" breakout boards commonly sold as "ST7789 240×240" or "ST7789V 240×320").

**Typical ST7789 breakout pinout (8-pin, most common layout):**

| Pin | Label | Function |
|-----|-------|----------|
| 1 | GND | Ground |
| 2 | VCC | 3.3V power |
| 3 | SCL / SCK | SPI clock |
| 4 | SDA / MOSI | SPI data in |
| 5 | RES / RST | Hardware reset (active low) |
| 6 | DC / RS | Data/Command select |
| 7 | CS | Chip select (active low) |
| 8 | BLK / BL | Backlight enable |

**Note:** Pin order varies across ST7789 breakout vendors. The header is a pass-through — users plug their specific module. Label pins clearly in silk screen. Optional: provide a 2-row pinout label on silkscreen next to J_DISP.

---

## Connector (J_DISP)

| Parameter | Value |
|-----------|-------|
| Type | 2.54mm pitch, 1x8 male header, right-angle, through-hole |
| Pins | 8 |
| PCBWay | THT, wave-solderable |
| Notes | No user soldering — PCBWay assembles. Rev A uses right-angle for lower vertical profile and side cable exit. |

---

## Series Damping Resistors

SPI signal lines should have series damping resistors to reduce ringing on fast edges (ST7789 can run at >20MHz SPI clock).

| Net | Resistor | Value | Package | Notes |
|-----|----------|-------|---------|-------|
| SPI_SCK | R_SCK | 33Ω | 0603 | Between ESP32 GPIO6 and J_DISP SCL pin |
| SPI_MOSI | R_MOSI | 33Ω | 0603 | Between ESP32 GPIO7 and J_DISP SDA pin |
| DISP_CS | R_CS | 33Ω | 0603 | Between ESP32 GPIO10 and J_DISP CS pin |
| DISP_DC | R_DC | 33Ω | 0603 | Between ESP32 GPIO9 and J_DISP DC pin |
| DISP_RST | R_RST | 33Ω | 0603 | Between ESP32 GPIO16 and J_DISP RES pin |

Rev A decision: SPI_MISO is not implemented. Leave any display-module MISO/SDO pin unconnected.

33Ω is a standard signal integrity value — low enough to not cause significant voltage drop, high enough to damp reflections at board edge.

---

## Power Lines to Header

| Header Pin | Net | Notes |
|------------|-----|-------|
| VCC | 3V3 | Directly from 3V3 rail; add 100nF bypass cap at header pin |
| GND | GND | Direct return |

**Local bypass cap:**
- 100nF MLCC, 10V, 0402, at J_DISP VCC pin.

Some ST7789 modules have a 3.3V regulator onboard and accept 5V VCC. This header provides 3.3V — confirm display module accepts 3.3V VCC (most bare ST7789 modules do).

## Rev A Verified Sourcing Picks

| Function | LCSC | Verified part |
|----------|------|---------------|
| J_DISP | C32713267 | hanxia HX PZ2.54-1x8P WZ, 2.54mm single-row 1x8 male right-angle through-hole header |
| R_SCK / R_MOSI / R_CS / R_DC / R_RST | C2909394 | FOJAN FRC0603F33R0TS, 33Ω ±1% 0603 thick-film resistor |
| C_DISP_VCC | C307331 | Samsung CL05B104KB54PNC, 100nF 50V X7R 0402 MLCC |
| Q_BL | C181119 | MMBT3904 NPN SOT-23 for open-collector backlight sink |
| R_BL_BASE | C4190 | 2.2kΩ 0603 base resistor for Q_BL |
| R_BL_PD | C25803 | 100kΩ 0603 base pull-down for Q_BL |

---

## Backlight (BLK) Pin

Use the same open-collector approach as the fan PWM stage so the ESP32 does not source backlight current directly.

| Node | Implementation |
|------|----------------|
| Control input | `DISP_BL` from ESP32 GPIO40 into `R_BL_BASE` (2.2kΩ) to `Q_BL` base |
| Base default state | `R_BL_PD` (100kΩ) from `Q_BL` base to GND |
| Sink stage | `Q_BL` MMBT3904: emitter to GND, collector to J_DISP BLK pin |
| Backlight output | J_DISP BLK pin is driven by open-collector sink for on/off and PWM dimming |

**Firmware note:** This stage is inverting. Higher PWM duty at GPIO40 results in stronger sink action on BLK.

---

## Net Summary

| Net | Direction | Header Pin |
|-----|-----------|------------|
| GND | Input | J_DISP pin 1 |
| 3V3 | Input | J_DISP pin 2 |
| SPI_SCK (via R_SCK) | Output | J_DISP pin 3 |
| SPI_MOSI (via R_MOSI) | Output | J_DISP pin 4 |
| DISP_RST (via R_RST) | Output | J_DISP pin 5 |
| DISP_DC (via R_DC) | Output | J_DISP pin 6 |
| DISP_CS (via R_CS) | Output | J_DISP pin 7 |
| DISP_BL (to Q_BL base via R_BL_BASE) | Output | Internal driver input |
| BLK (from Q_BL collector) | Output | J_DISP pin 8 |

---

## Layout Rules

1. **Damping resistors placed close to J_DISP**, between header and trace to ESP32 — correct side of the resistor should face the source (ESP32 side), termination faces the load (header).
2. **J_DISP placed on board edge or accessible face** for clean cable routing to display.
3. **Bypass cap at VCC pin** — immediately adjacent to J_DISP.
4. **SPI traces** should not run parallel to module power paths or fan 12V high-current traces. Keep at least 3mm separation where practical.
5. **RST trace** must not float — routed from ESP32 GPIO16 through R_RST to header pin. No pull-up needed (ESP32 drives it; display module typically has internal pull-up).

---

## Checklist Before KiCad Capture

- [ ] Confirm target display module pinout (measure or check datasheet for specific module)
- [ ] Verify right-angle connector pin-1 orientation matches silkscreen and cable pin order
- [ ] Verify display MISO/SDO pin remains NC in Rev A
- [ ] Verify BLK input behavior for your selected module (logic enable vs direct LED path)

---

## Dependencies

- **Depends on:** `ESP32S3Core` (SPI + CS/DC/RST nets), `Power3V3` (3V3 net)
- **Consumed by:** none (leaf sheet)

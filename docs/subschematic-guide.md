# Sub-Schematic Recommendation — Rev A

What to put on each sheet. Start with sheets that have no dependencies, then work towards the ESP32 sheet.

This guide is for manual schematic capture. Treat each block below as the target state for a blank child sheet, not as a patch against previously generated content.

Manual setup reminder:
1. Start on the root sheet.
2. Create the child sheet symbol and give it the expected name.
3. Open the child sheet file.
4. Place symbols, wires, and labels from the matching block below.
5. Return to root only after the child sheet is internally coherent.

---

## Capture Order (dependency-safe)

```
1. InputProtection   — no upstream deps
2. Power12V          — needs VIN_PROTECTED from sheet 1
3. Power3V3          — needs VIN_PROTECTED from sheet 1
4. Fan4Wire          — needs 12V, 3V3, FAN_PWM/TACH (define nets, wire later)
5. DisplayHeader     — needs 3V3, SPI nets
6. ExternalProgrammingHeader — needs 3V3, UART_TX/RX, EN_RESET, GPIO0_BOOT
7. OptionalExpansion — needs 3V3, GPIO nets
8. TestPoints        — pure TP pads, no active nets needed
9. ESP32S3Core       — place last; connects all signal nets together
```

Shortcut hints:
- `A` add symbol
- `W` wire
- `L` net label
- `P` power symbol
- `G` drag connected items
- `E` edit properties
- `Esc` cancel current command

---

## Sheet Contents

### InputProtection
```
J_PWR (2-pin screw terminal 5.08mm pitch)  → VIN_RAW
D_TVS_IN (SMBJ26A, SMB) from VIN_RAW to GND
D1 (SS36 SMA)  ←  VIN_RAW
F1 (2A fuse 1206)
C_IN_BYP (100nF 0402) across output
→ exports: VIN_PROTECTED, GND
```

### Power12V
```
U1 (K7812 module, SIP-3)  ←  VIN_PROTECTED
C12_IN_BULK (100uF/50V radial) — input bulk
C12_IN_BYP (1uF/50V 0603)      — input bypass
→ exports: 12V
(no feedback network, no inductor, no diode — module only)
```

### Power3V3
```
U2 (K7803 module, SIP-3)  ←  VIN_PROTECTED (parallel input, not from 12V)
C33_IN_BULK (100uF/50V radial) — input bulk
C33_IN_BYP (10uF/50V 1210)     — input bypass
C33_OUT_BULK (100uF/10V radial) — output bulk
C33_OUT_BYP (10uF/16V 0603)     — output bypass
→ exports: 3V3
```

### Fan4Wire
```
J_FAN (1x4 2.54mm)
  pin 1 → GND
  pin 2 → 12V  ← also: C_FAN_BULK (47uF) + C_FAN_HF (100nF) to GND
    pin 3 → tach conditioning: [R_TACH_PU 10k to 3V3] → [R_TACH_SERIES 1k] → FAN_TACH
      and [D_TACH_CLAMP Schottky] from FAN_TACH to 3V3 (cathode 3V3, anode FAN_TACH)
  pin 4 → Q1 collector (FAN_PWM_OC)

Q1 (MMBT3904 SOT-23)
  base  ← [R_BASE 2.2k] ← FAN_PWM
  base  → [R_BPD 100k]  → GND
  emitter → GND
  collector → J_FAN pin 4

← imports: 12V, 3V3, FAN_PWM
→ exports: FAN_TACH
```

### DisplayHeader_ST7789
```
J_DISP (1x8 2.54mm right-angle header)
  pin 1 → GND
  pin 2 → 3V3 ← [C_DISP_VCC 100nF] to GND
  pin 3 → SPI_SCK   ← [R_SCK 33R] ← GPIO6
  pin 4 → SPI_MOSI  ← [R_MOSI 33R]← GPIO7
  pin 5 → DISP_RST  ← [R_RST 33R] ← GPIO16
  pin 6 → DISP_DC   ← [R_DC 33R]  ← GPIO9
  pin 7 → DISP_CS   ← [R_CS 33R]  ← GPIO10

Q_BL (MMBT3904 SOT-23)
  base  ← [R_BL_BASE 2.2k] ← DISP_BL (GPIO40 PWM)
  base  → [R_BL_PD 100k]   → GND
  emitter → GND
  collector → J_DISP pin 8 (BLK)

Rev A policy:
  J_DISP MISO/SDO (if present on module) is left NC (not connected)

← imports: 3V3, GPIO6/7/8/9/10/16/40 (via net names)
```

### ExternalProgrammingHeader
```
J_PROG (1x6 2.54mm right-angle header)
  pin 1 → GND
  pin 2 → 3V3
  pin 3 → UART_TX (ESP32 GPIO43)
  pin 4 → UART_RX (ESP32 GPIO44)
  pin 5 → EN_RESET
  pin 6 → GPIO0_BOOT

External programmer wiring:
  adapter TX → UART_RX
  adapter RX ← UART_TX
  adapter reset control → EN_RESET (optional auto-program)
  adapter boot control  → GPIO0_BOOT (optional auto-program)

← imports: 3V3, UART_TX, UART_RX, EN_RESET, GPIO0_BOOT
```

### OptionalExpansionHeader  *(DNP entire sheet if not needed)*
```
J_EXP (2x5 2.54mm)
  3V3 pin → [F_EXP polyfuse 500mA] → 3V3
  GND pin → GND
  GPIO pins → [R_EXP1–8 33R each] → GPIO17/18/19/20/21/38/39/41

← imports: 3V3, free GPIOs
```

### TestPoints
```
TP1  VIN_PROTECTED
TP2  12V
TP3  3V3
TP4  GND
TP5  FAN_PWM
TP6  FAN_TACH
TP7  UART_TX
TP8  UART_RX
TP9  EN_RESET
TP10 GPIO0_BOOT

(TP pads only — no purchased components)
```

### ESP32S3Core  *(capture last — all nets must already exist)*
```
U3 (ESP32-S3-WROOM-1-N16R8)
  VDD → 3V3
  GND → GND (pins 1, 40, 41, EP)
  GPIO4  ← FAN_TACH
  GPIO5  → FAN_PWM
  GPIO6  → SPI_SCK
  GPIO7  → SPI_MOSI
  GPIO9  → DISP_DC
  GPIO10 → DISP_CS
  GPIO16 → DISP_RST
  GPIO40 → DISP_BL
  GPIO43 → UART_TX
  GPIO44 ← UART_RX
  GPIO17–42 (selected) → expansion nets
  EN     ← EN_RESET
  GPIO0  ← GPIO0_BOOT

Decoupling on this sheet:
  C_BULK (10uF 0603) at VDD pin
  C_DEC (100nF 0402) at VDD pin
  R_EN (10k) EN to 3V3; C_EN (1uF 0603) EN to GND
  R_BOOT (10k) GPIO0 to 3V3
  SW_RESET button EN to GND
  SW_BOOT button GPIO0 to GND
```

### Future Revision Note
```
Onboard rail telemetry is intentionally deferred from Rev A.
If bring-up later shows a real need, add a dedicated telemetry sheet in a future revision instead of reserving GPIOs and BOM lines now.
```

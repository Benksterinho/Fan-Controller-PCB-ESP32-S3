# Sheet Spec: OptionalExpansionHeader

**KiCad sheet:** `OptionalExpansionHeader`  
**Status:** OPTIONAL — populate only if spare GPIOs are desired for future firmware expansion.  
**Purpose:** Expose safe spare ESP32-S3 GPIOs on a 2.54mm header with per-line ESD/overcurrent protection. Must not conflict with strapping pins or internally reserved GPIOs.  
**Input nets:** `3V3`, `GND`, and spare GPIO nets from `ESP32S3Core`

---

## Safety Rules (Non-Negotiable)

1. **Strapping pins (GPIO0, GPIO3, GPIO45, GPIO46) must never appear on this header.**
2. **Module-internal GPIOs (GPIO11–GPIO15 in N16R8 variant) must never be routed here.**
3. **All header GPIO lines must have ESD protection** — either TVS array or series resistors at minimum.
4. **3V3 on header is protected with a polyfuse** to prevent shorts from external devices from browning out the main board.

---

## Available GPIOs (after all prior assignments)


From ESP32S3Core assignment table, the following GPIOs are assigned to mandatory functions:
- GPIO4: FAN_TACH
- GPIO5: FAN_PWM
- GPIO6, GPIO7, GPIO8, GPIO9, GPIO10: SPI display/control
- GPIO16: **Display reset only (do not use for expansion header)**
- GPIO40: **Display backlight control only (do not use for expansion header)**
- GPIO43, GPIO44: UART0

Reserved / prohibited:
- GPIO0, GPIO3, GPIO45, GPIO46: strapping pins
- GPIO11–GPIO15: PSRAM interface (N16R8)
- GPIO16: **Assigned to display reset, not available for expansion header**
- GPIO40: **Assigned to display backlight control, not available for expansion header**

**Safe candidates for expansion header:**
GPIO17, GPIO18, GPIO19, GPIO20, GPIO21, GPIO38, GPIO39, GPIO41, GPIO42

GPIO1 and GPIO2 are available for expansion because onboard telemetry is deferred from Rev A.

**Recommendation:** Expose up to 8 GPIOs on the header: GPIO17–GPIO21 (5 pins) + GPIO38, GPIO39, GPIO41 (3 pins) = 8 GPIO pins. **Do not use GPIO16 or GPIO40.**

---

## Connector (J_EXP)

| Parameter | Value |
|-----------|-------|
| Type | 2.54mm pitch, 2×5 female header (10 pins) |
| Rows | 2 rows × 5 pins |
| Pinout | 8 GPIO signals + 3V3 (protected) + GND |
| PCBWay | THT, wave-solderable |

Rev A locked pinout (2×5, standard 2.54mm header):

| Pin | Signal | Pin | Signal |
|-----|--------|-----|--------|
| 1 | GPIO17 | 2 | GPIO18 |
| 3 | GPIO19 | 4 | GPIO20 |
| 5 | GPIO21 | 6 | GPIO38 |
| 7 | GPIO39 | 8 | GPIO41 |
| 9 | 3V3 (protected) | 10 | GND |

GPIO1, GPIO2, and GPIO42 remain available at the MCU level for future revisions but are not routed to J_EXP in Rev A.

---

## ESD Protection

| Option | Implementation | Notes |
|--------|---------------|-------|
| A (recommended) | 24-line TVS array (e.g., PRTR5V0U2X per 2 lines, or USBLC6-4SC6) | Best protection, compact |
| B (simple) | 33Ω series resistors on each GPIO line | Limits fault current; no clamping |
| C (minimal) | No protection | Not recommended — external devices can damage ESP32 GPIO |

**Decision for rev A:** Option B — 33Ω series resistors on each GPIO. Simple, no additional ICs needed. Upgrade to TVS array in rev B if needed.

| Component | Value | Package | Qty | Notes |
|-----------|-------|---------|-----|-------|
| R_EXP_n (×8) | 33Ω, 0603 | 0603 | 8 | One per GPIO line, between ESP32 net and header pin |

---

## 3V3 Power on Header (Protected)

If 3V3 is exposed on the header, it must be polyfuse-protected to prevent external loads from browning out the main board.

| Component | Value | Notes |
|-----------|-------|-------|
| F_EXP | 500mA polyfuse (resettable PTC) | 3V3 to J_EXP power pin(s) |

Polyfuse trips at >500mA sustained draw, protecting the 3.3V rail. Resets when current drops.

---

## DNP Strategy

If this sheet is not populated:
- All resistors and polyfuse are DNP.
- GPIO pads on J_EXP are left unconnected on the board.
- No firmware changes needed — unused GPIOs are floating inputs, which is benign.

---

## Layout Rules

1. **Series resistors** placed between ESP32 traces and J_EXP header (resistors close to header, not close to module).
2. **3V3 polyfuse** placed before the 3V3 pin on J_EXP.
3. **J_EXP placed on accessible board edge** for easy external wiring.
4. **All GPIO traces** routed away from module input/output power paths and fan 12V high-current routing. Minimum 3mm clearance where practical.
5. **No strapping GPIO traces** routed to or near this header area.

---

## Dependencies

- **Depends on:** `ESP32S3Core` (spare GPIO nets), `Power3V3` (3V3 net)
- **Consumed by:** none (leaf sheet)
- **Capture order:** last, after all mandatory sheets are stable

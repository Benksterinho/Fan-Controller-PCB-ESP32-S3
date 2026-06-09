# Sheet Spec: TestPoints

**KiCad sheet:** `TestPoints`  
**Purpose:** Labeled test point pads for bring-up, debugging, and production verification. No active components. Must not alter any net behavior.  
**Rule:** Every test point is a passive net tap only — no added series resistance, no pull-up/pull-down, no load.

---

## Test Point List

| Ref | Net | Label | Signal type | Notes |
|-----|-----|-------|-------------|-------|
| TP1 | VIN_PROTECTED | VIN | DC power | After input protection; pre-module input |
| TP2 | 12V | 12V | DC power | K7812 module output |
| TP3 | 3V3 | 3V3 | DC power | K7803 module output |
| TP4 | GND | GND | Ground | Common reference (multiple pads OK) |
| TP5 | FAN_PWM | FAN_PWM | 3.3V PWM logic | ESP32 GPIO5 output |
| TP6 | FAN_TACH | FAN_TACH | 3.3V logic | Conditioned tach input to ESP32 GPIO4 |
| TP7 | EN_RESET | EN | 3.3V logic | ESP32-S3 EN pin |
| TP8 | GPIO0_BOOT | BOOT | 3.3V logic | ESP32-S3 GPIO0 |
| TP9 | UART_TX | TX | 3.3V UART | ESP32 TX (GPIO43) |
| TP10 | UART_RX | RX | 3.3V UART | ESP32 RX (GPIO44) |

Rev A default decision:
- TP11 (DISP_BL) is deferred to Rev B to avoid scope/routing churn. Re-open only if verified board space is available with no routing risk.

---

## Test Point Footprint

| Parameter | Value |
|-----------|-------|
| Pad size | 1.0mm drill, 1.5mm copper ring (through-hole) |
| Preferred type | Keystone 5000 or equivalent 1.0mm THT test point | 
| Alternate | SMD pad 1.5×1.5mm with silkscreen label (no height for probe but compact) |
| Assembly | PCBWay can insert THT test points; SMD pads require no assembly and cost less |

**Recommendation:** Use SMD test point pads (no component, just copper pad + silkscreen label) for power and logic rails. This reduces BOM cost and assembly complexity. A probe tip can contact the pad directly.

---

## Placement Guidelines

1. **Power rail test points (TP1–TP4):** Place near each rail's output cap — allows confirming rail is good before probing logic.
2. **Logic test points (TP5–TP10):** Place near the ESP32 module edge, in a row for easy logic analyzer clip attachment.
3. **Multiple GND pads:** Place at least 2–3 GND test points distributed near other test points so every probe can find a nearby return.
4. **All test points must be accessible from top-side probe** — do not place under headers or module.

---

## Silkscreen Requirements

Each test point pad must have:
- Ref designator (TP1, TP2…)
- Net name label (VIN, 12V, 3V3, GND, FAN_PWM, etc.)

---

## Dependencies

- **Depends on:** all mandatory sheets (uses only existing nets, adds no new components that affect behavior)
- **Consumed by:** none (leaf sheet)
- **Capture order:** last mandatory sheet, after all other functional sheets are captured

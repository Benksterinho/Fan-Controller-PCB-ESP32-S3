# Sheet Spec: Power3V3

**KiCad sheet:** `Power3V3`  
**Purpose:** Step down `VIN_PROTECTED` to 3.3V for all ESP32-S3 logic, display header, and peripherals using a proven non-isolated module regulator.  
**Input net:** `VIN_PROTECTED`  
**Output net:** `3V3`  

---

## Module Selection

| Parameter | Value |
|-----------|-------|
| Module family | K78xx-compatible non-isolated module regulator |
| Model | K7803 (or K7803-500 variant) |
| Topology | Integrated switching regulator module |
| Input voltage range | Wide input range per datasheet (must include 19-24V VIN_PROTECTED for this design; 17-18V not guaranteed with current Schottky reverse-polarity stage) |
| VIN | VIN_PROTECTED | Input source for K7803 module (parallel topology; see architecture) |
| Output voltage | 3.3V fixed |
| Output current | 1.0A nominal (selected for Wi-Fi/display headroom) |
| Efficiency | ~75–80% typical |
| Package | SIP-3 module form factor |

**Current capacity note:** K7803 at 300–500mA provides margin for:
- ESP32-S3 average: 100–150mA typical
- Display header: 50–100mA typical  
- Wi-Fi burst transients: covered by bulk output cap
- Test margin: ~50–100mA headroom

If 3.3V rail draws exceed the measured safe headroom during Wi-Fi activity, evaluate a higher-current compatible module variant.

**Rationale:** Pre-designed module eliminates risk of design iteration on discrete inductors, catch diodes, and feedback networks. Proven by manufacturer. High first-pass success rate.

---

---

## Decoupling & Input/Output Filtering

### Input Side (VIN_PROTECTED from InputProtection)
| Component | Value | Notes |
|-----------|-------|-------|
| C33_IN_BULK | 100uF, 50V | Bulk input decoupling, placed close to module VIN pin |
| C33_IN_BYP | 10uF ceramic, 50V X5R, 1210 | Input bypass in parallel with C33_IN_BULK; 1210 size avoids unrealistic 0603-at-50V assumptions |

### Output Side (3.3V)
| Component | Value | Notes |
|-----------|-------|-------|
| C33_OUT_BULK | 100µF, 10V | Bulk output cap for transient margin during Wi-Fi bursts |
| C33_OUT_BYP | 10uF ceramic, 16V X5R, 0603 | Output bypass in parallel with C33_OUT_BULK |

---

## Design Rules

- Place module close to VIN_PROTECTED source (minimize trace inductance to module VIN pin)
- Place output capacitors (C33_OUT_BULK, C33_OUT_BYP) immediately adjacent to module output pin — keep leads short
- Use ground plane for low-impedance return paths (both input and output GND must connect to plane)
- Follow module datasheet pin assignments for VIN, GND, and VOUT exactly
- Mount module in flat area with adequate thermal vias under package if needed
- **No external inductors, catch diodes, or feedback resistor networks required — module is fully integrated.**

---

## Net Connections

| Module Pin | Net | Notes |
|------------|-----|-------|
| VIN | VIN_PROTECTED | Input from InputProtection sheet (parallel with K7812 stage) |
| VOUT | 3V3 | Regulated 3.3V output to ESP32-S3, display header, and peripherals |
| GND (in/out) | GND | Common ground return |

---

## Output Rail Spec

| Net | Voltage | Max current (module) | Downstream consumers |
|-----|---------|----------------------|----------------------|
| 3V3 | 3.3V ± 3% | up to 1.0A module rating | ESP32-S3 module, display header, fan interface transistor bias network, external programming header (optional target power), test points, telemetry ADC, optional expansion header |

---

## Capacity & Contingency

**Current consumption summary (typical):**
- ESP32-S3 average: ~100–150mA
- Display refresh + GPIO: ~50–100mA  
- Wi-Fi TX burst: transient spikes (supported by C33_OUT_BULK cap ESR and series impedance)
- Margin: ~50–100mA available for future I/O expansion

**If 3.3V rail proves undersized after bringup:**
1. Measure peak current during Wi-Fi and SPI display refresh
2. If sustained output current approaches the module limit, evaluate:
   - A higher-current compatible module variant
   - Load reduction on optional rails
3. Update BOM and Power3V3 sheet accordingly

This is a **first-pass design assumption**; bringup will validate.

---

## Checklist Before KiCad Capture

- [ ] Confirm Mornsun K7803 LCSC part number and availability (typical LCSC search: "Mornsun K7803")
- [ ] Verify K7803 package/footprint compatibility with PCBWay assembly (SIP-3)
- [ ] Confirm 100uF/50V electrolytic input cap LCSC MPN (SMD)
- [ ] Confirm 100µF/10V electrolytic output cap LCSC MPN (SMD)
- [ ] Download K7803 datasheet; verify pin assignments match schematic net table
- [ ] Plan PCB layout: place module close to VIN_PROTECTED input routing

---

## Dependencies

- **Depends on:** `InputProtection` sheet (`VIN_PROTECTED` input net must be present)
- **Consumed by:** `ESP32S3Core`, `Fan4Wire`, `DisplayHeader_ST7789`, `ExternalProgrammingHeader`, `OptionalExpansionHeader`

# Power12V Sheet Spec

## Purpose

Convert the protected 19-24V supported input into a stable 12V rail for the fan using a proven non-isolated module regulator.

## Nets

- `VIN_PROTECTED`: protected input from `InputProtection`
- `12V`: regulated 12V output rail
- `GND`: power ground return

## Module Selection

| Parameter | Value |
|-----------|-------|
| Module family | K78xx-compatible non-isolated module regulator |
| Model | K7812 (or K7812-500, K7812-1000 variant) |
| Topology | Integrated switching regulator module |
| Input voltage range | 18V – 36V (using protected 19-24V supported input; 17-18V not guaranteed with current Schottky reverse-polarity stage) |
| Output voltage | 12V fixed |
| Output current | 1.0A nominal (sufficient for 12V fan rail with margin) |
| Efficiency | ~78–82% typical |
| Package | SIP-3 module form factor |

**Rationale:** Pre-designed module eliminates risk of design iteration on discretes (inductors, catch diodes, feedback networks). Proven by manufacturer. High first-pass success rate.

---

## Decoupling & Input/Output Filtering

### Input Side (VIN_PROTECTED)
| Component | Value | Notes |
|-----------|-------|-------|
| C12_IN_BULK | 100µF, 50V | Bulk decoupling, placed close to module VIN pin |
| C12_IN_BYP | 1µF ceramic, 50V | Input bypass in parallel with C12_IN_BULK |

### Output Side (12V)
| Component | Value | Notes |
|-----------|-------|-------|
| C12_OUT_BULK | 47uF, 25V | Bulk decoupling at K7812 VOUT; improves fan startup and PWM transient stability |
| C12_OUT_BYP | 100nF ceramic, 50V | Output bypass at K7812 VOUT to reduce switching noise and ripple |

Datasheet note: K7812-1000R3 family tables indicate a high allowable capacitive load (up to ~680uF for positive-output variants), so adding 47uF + 100nF at the output remains comfortably within typical limits.

---

## Design Rules

- Place module close to input and output connectors to minimize trace inductance
- Keep all decoupling capacitors adjacent to module pins (≤ 10mm trace runs preferred)
- Use ground plane for low-impedance return paths
- Follow module datasheet pin assignments for VIN, GND, and VOUT exactly
- Mount on flat area with adequate spacing for thermal vias if needed

---

## Net Connections

| Module Pin | Net | Notes |
|------------|-----|-------|
| VIN | VIN_PROTECTED | Input from InputProtection sheet |
| VOUT | 12V | Regulated 12V output to fan stage |
| GND (in/out) | GND | Common ground return |

---

## Review Gate

- Confirm module datasheet lists K7812 as compatible with the supported 19-24V input range (K7812 typical range 18-36V)
- Confirm output regulation spec is ±5% or better across full input and load range
- Verify PCBWay assembly supports selected SIP-3 module footprint (confirm with EMS lead time check)
- Measure 12V output voltage under load and thermal behavior during board bringup

# Sheet Spec: Fan4Wire

**KiCad sheet:** `Fan4Wire`  
**Purpose:** 4-pin PWM fan interface — power, ground, PWM open-collector sink drive, and tachometer input conditioning.  
**Input nets:** `12V`, `3V3`, `GND`, `FAN_PWM` (from ESP32S3Core), `FAN_TACH` (to ESP32S3Core)  
**Reference:** Intel 4-Wire PWM Fan Control Specification Rev 1.3

---


## Fan Connector (J_FAN)

| Parameter         | Value                                                      |
|-------------------|------------------------------------------------------------|
| Connector type    | 4-pin 2.54mm pitch single-row pin header (through-hole, right-angle) |
| Pinout (silkscreen)| 1: GND (black), 2: 12V (yellow), 3: TACH (green), 4: PWM (blue) |
| Pin 1             | GND                                                        |
| Pin 2             | 12V (fan power)                                            |
| Pin 3             | TACH (open-collector out from fan, active low)             |
| Pin 4             | PWM (25kHz control input to fan)                           |

**Standard Intel 4-pin fan pinout (for silkscreen and docs):**

| Pin | Signal | Color  | Function           |
|-----|--------|--------|--------------------|
| 1   | GND    | Black  | Ground             |
| 2   | 12V    | Yellow | +12V (fan power)   |
| 3   | TACH   | Green  | Tachometer sense   |
| 4   | PWM    | Blue   | PWM control input  |

**Silkscreen recommendation:**
- Label pin numbers and signals on PCB silkscreen: "1:GND 2:12V 3:TACH 4:PWM"
- Place pin 1 marker (dot or triangle) on silkscreen.

Use the project default connector: hanxia HX PZ2.54-1x4P WZ right-angle 2.54mm 1x4 THT pin header (`J_FAN`, LCSC `C32713263`).

---

## PWM Drive — Open-Collector Sink (Intel 4-Wire Compatible)

The fan PWM pin is a logic input with pull-up inside the fan (typically to 5V). Controller side must behave as an open-collector/open-drain sink and must not drive the pin high.

**Topology: NPN open-collector stage**

```
ESP32 FAN_PWM_GPIO ──[R_BASE 2.2kΩ]── Base (Q1: MMBT3904)
                   │
                 [R_BPD 100kΩ]
                   │
                  GND

Q1 Emitter ── GND
Q1 Collector ── FAN_PWM_OC ── Fan pin 4 (PWM)
```

When GPIO is high, Q1 sinks the PWM pin low. When GPIO is low, Q1 is off and fan internal pull-up provides logic high. This is an inverting stage; firmware should invert PWM duty convention if needed.

Do not add an external pull-up to 12V on fan PWM pin.

| Component | Value | Notes |
|-----------|-------|-------|
| Q1 (MMBT3904) | NPN transistor, SOT-23 | Open-collector sink stage for PWM pin |
| R_BASE | 2.2kΩ, 0603 | Limits base current from ESP32 GPIO |
| R_BPD | 100kΩ, 0603 | Base pull-down keeps Q1 off during reset |

**MMBT3904 specs:** common low-cost NPN in SOT-23, suitable for low-current logic sinking.

---

## Tachometer Input Conditioning

Fan tachometer output is open-collector (NPN), active-low, ~2 pulses per revolution.

**Conditioning circuit:**

```
Fan pin 3 (TACH) ──[R_TACH_PU 10kΩ]── 3V3
                │
               [R_SERIES 1kΩ]
                │
              FAN_TACH ── ESP32 GPIO4 (input)
                │
      [D_TACH_CLAMP Schottky]
            cathode → 3V3, anode → FAN_TACH
```

- `R_TACH_PU` (10kΩ to 3V3): pull-up for the open-collector tach signal, keeps logic level high when fan is not pulling low.
- `R_SERIES` (1kΩ): series protection resistor — limits GPIO current in fault conditions and adds mild RC filtering with GPIO input capacitance.
- `D_TACH_CLAMP` (Schottky to 3V3): clamps over-voltage at the MCU-side tach node if a fan tach output does not behave as open-collector at 3.3V levels.
- ESP32 internal pull-down must be **disabled** — external pull-up is sufficient. Do not add a parallel pull-down.

| Component | Value | Notes |
|-----------|-------|-------|
| R_TACH_PU | 10kΩ, 0603 | Pull-up to 3V3 |
| R_TACH_SERIES | 1kΩ, 0603 | Series protection |
| D_TACH_CLAMP | Schottky diode, SOD-123 | Cathode to 3V3, anode to FAN_TACH (MCU side) |

**Frequency:** At 1000 RPM with 2 pulses/rev: ~33Hz. At 3000 RPM: ~100Hz. Well within ESP32 GPIO interrupt range.

---

## Decoupling / Bulk Cap on 12V Fan Power

Fan motor startup draws inrush current. Add a local bulk cap at the 12V power pin on the fan connector.

| Component | Value | Notes |
|-----------|-------|-------|
| C_FAN_BULK | 47µF, 25V electrolytic, SMD | Local to J_FAN pin 2 (25V minimum recommended for 12V rail margin) |
| C_FAN_HF | 100nF MLCC, 25V, 0402 | HF bypass at J_FAN pin 2 |

---

## Net Summary

| Net | Direction | Notes |
|-----|-----------|-------|
| 12V | Input | Fan power (pin 2 of connector) |
| GND | Input | Fan return (pin 1 of connector) |
| FAN_PWM | Input (from ESP32) | 3.3V GPIO drives transistor base, 25kHz |
| FAN_TACH | Output (to ESP32) | Conditioned 3.3V tach signal, ~33–200Hz |
| FAN_PWM_OC | Internal | Open-collector node from Q1 collector to fan PWM pin 4 |

---

## Layout Rules

1. **Q1 placed close to fan connector PWM pin** to keep the open-collector node short.
2. **C_FAN_BULK placed immediately at J_FAN 12V pin.**
3. **FAN_TACH signal trace** kept away from fan PWM open-collector node to avoid capacitive coupling.
4. **R_TACH_PU close to fan connector** — minimizes stub length on the open-collector net.
5. **D_TACH_CLAMP close to ESP32 GPIO input side** (after R_TACH_SERIES) so clamp current does not flow through long traces.
6. **R_BASE and R_BPD** placed close to Q1 base.

---

## Checklist Before KiCad Capture

- [ ] Confirm MMBT3904 LCSC MPN and stock (preferred candidate: `C181119`; alternates: `C111113`, `C916374`)
- [ ] Verify target fan (Arctic P12 Pro PST CO) follows Intel 4-wire PWM input behavior with internal pull-up
- [ ] Confirm PWM inversion requirement in firmware (NPN sink stage is inverting)
- [ ] Check maximum fan tachometer pull-up voltage is 3V3 compatible (most PC fans: yes)

---

## Dependencies

- **Depends on:** `ESP32S3Core` (FAN_PWM, FAN_TACH nets), `Power12V` (12V net), `Power3V3` (3V3 for pull-ups)
- **Consumed by:** none (leaf sheet)

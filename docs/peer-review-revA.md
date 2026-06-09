# Peer Review — ESP32 Fan Controller Rev A

**Scope:** Maker/community build. Not production-grade.  
**Date:** May 18, 2026  
**Reviewer notes:** Maker-friendly design with above-average documentation quality.

**Migration note (May 2026):** This review predates the switch from on-board ProgrammingUSB to ExternalProgrammingHeader. USB/CH9102F-specific comments are historical context, not active Rev A requirements.

---

## Overall Impression

This is a well-documented, conservatively designed single-fan controller board. The documentation quality is above-average for a maker project — clear net naming, locked BOM with LCSC codes, consistent architecture docs, and explicit design intent. The choice of module regulators over discrete bucks is the right call for a first revision.

---

## Positive Findings

| Area | Comment |
|------|---------|
| Architecture | Parallel-fed module regulators from VIN_PROTECTED — simple, avoids cascading dropout issues. Good. |
| BOM discipline | 44/46 lines LOCKED with concrete LCSC codes and alternates listed. Excellent for a maker project. |
| GPIO assignment | Clean separation — strapping pins protected, PSRAM GPIOs excluded, expansion header uses only safe pins. |
| Fan interface | Standard Intel 4-pin spec followed, open-collector topology correct, R_BPD keeps transistor off at boot. |
| Tach conditioning | Pull-up + series R + clamp diode is textbook and appropriate. |
| Programming interface | Active Rev A uses an external 1x6 programming header; legacy USB-C/CH9102F review points are historical only. |
| Test points | Comprehensive coverage of all key rails and debug signals. |
| Documentation | Signal hierarchy diagram makes net review easy. Capture order is logical. |

---

## Issues & Recommendations

### 1. Tach Clamp Diode Polarity (Resolved)

**Location:** [docs/signal-hierarchy.md](signal-hierarchy.md)

**Issue:**
```
D_TACH_CLAMP Schottky (anode 3V3, cathode FAN_TACH)
```

This is **backwards** for an overvoltage clamp. A clamp diode protecting a 3.3V GPIO should have:
- **Cathode to 3V3** (clamps upward overshoots to VCC + Vf)
- **Anode to the signal** (FAN_TACH MCU side)

With anode-to-3V3 and cathode-to-signal, the diode is forward-biased whenever the signal is below 3V3, which would clamp the tach signal low and interfere with readings.

**Resolution (May 19, 2026):** Corrected in project docs and BOM notes to **cathode to 3V3, anode to FAN_TACH**. Keep this orientation during schematic capture.

---

### 2. K7812 Input Voltage Range vs. Design Spec (Low — Verify)

**Issue:** The K7812-1000R3 datasheet typically specifies a **minimum input of 15V** (some variants 18V) for a 12V output. With only ~7–12V headroom from 19–24V input, you're nominally fine. However:

At 19V in with Schottky drop (~0.4V), you're at **18.6V** — just barely above the 18V floor specified in [docs/sheet-specs/Power12V.md](sheet-specs/Power12V.md).

The SS34 forward drop at 1A can reach 0.5–0.55V at elevated temperatures. Worst case at 19V input − 0.55V drop = 18.45V.

**Recommendation:** Confirm the K7812 minimum VIN from the actual EVISUN datasheet (not the generic K78xx family). At 19V input minus worst-case Schottky drop, you may be marginal. This is **acceptable for a maker board** (most laptop bricks are 19.5V or 20V), but document the risk in the requirements or bring-up notes.

---

### 3. No Output Capacitor on Power12V Sheet (Low — Layout Note)

**Issue:** The [docs/sheet-specs/Power12V.md](sheet-specs/Power12V.md) spec says output caps are "optional — place at load sheet (Fan4Wire)." The BOM does include C_FAN_BULK (47µF/25V) at the fan connector. This is fine, but:

Some integrated modules require a minimum output capacitance at the VOUT pin for stability.

**Recommendation:** Confirm the K7812 module datasheet doesn't require a minimum output capacitance at its VOUT pin for stability. If it does, add a 10µF ceramic right at the module output in addition to the remote bulk at J_FAN.

---

### 4. SS34 as Reverse-Polarity Protection (Acceptable — Know the Trade-off)

**Issue:** The SS34 (40V, 3A Schottky) works for reverse-polarity blocking, but:
- Drops ~0.4–0.55V at load, wasting power and heat
- At 24V × 1.5A total load ≈ 0.8W dissipation in the diode

**Rationale:** For a maker board this is fine. For future revisions, a P-channel MOSFET reverse-polarity circuit would eliminate the drop — but that adds complexity inappropriate for Rev A.

**No action needed for Rev A.**

---

### 5. Fuse Rating vs. Total Load (Low — Already Correct)

**Verification:** F1 is 2A. Combined worst-case:
- 12V rail: 1A from K7812 (drawing ~1A × 12V / 19V / 0.8 eff ≈ 0.79A from VIN)
- 3.3V rail: 1A from K7803 (drawing ~1A × 3.3V / 19V / 0.75 eff ≈ 0.23A from VIN)
- **Total VIN current ≈ 1.0A worst case**

2A fuse with fast-acting rating is reasonable — provides ~2× headroom for inrush.

**Verdict:** Correct as-is.

---

### 6. USB VBUS Not Connected to Anything (Acceptable — Document Clearly)

**Issue:** VBUS from USB-C is not used for board power. This is correct for a self-powered device, but it's easy to miss.

**Recommendation:** Add a silkscreen note or update [README.md](../README.md):
```
"Board requires external 19–24V DC power (laptop brick, etc.). USB is for 
programming and serial logging only — cannot power the board from USB alone."
```

Makers will try to power from USB alone and wonder why nothing works.

---

### 7. Backlight Pin on Display Header (Minor — Implementation Needs Clarification)

**Issue:** The display header spec [docs/sheet-specs/DisplayHeader_ST7789.md](sheet-specs/DisplayHeader_ST7789.md) mentions BLK pin with options (tie to 3V3 or GPIO-controlled) but:
- The BOM doesn't show a connection or solder jumper for BLK
- GPIO map doesn't reserve a GPIO for backlight control

**Recommendation:** Tie BLK directly to 3V3 on the header (simplest for Rev A) or add a solder jumper for "always on" default. Most ST7789 breakouts expect this pin driven high. If left floating, some displays won't light up.

Update the [docs/sheet-specs/DisplayHeader_ST7789.md](sheet-specs/DisplayHeader_ST7789.md) backlight section to show the selected option.

---

### 8. Expansion Header GPIO List Inconsistency (Minor — Doc Cleanup)

**Issue:** Multiple GPIO lists across three documents:

- [docs/gpio-map.md](gpio-map.md) lists expansion candidates as 12 GPIOs
- [docs/sheet-specs/OptionalExpansionHeader.md](sheet-specs/OptionalExpansionHeader.md) recommends only 8 specific GPIOs
- The 2×5 header BOM (10 pins) only maps 8 GPIOs + 3V3 + GND

All the functionality works, but the multiple lists could confuse someone implementing the schematic.

**Recommendation:** Consolidate to a single authoritative list. Create a pinout table in [docs/sheet-specs/OptionalExpansionHeader.md](sheet-specs/OptionalExpansionHeader.md) showing exactly which GPIOs go on J_EXP (all 10 pins) and reference it from [docs/gpio-map.md](gpio-map.md).

---

### 9. CH9102F Internal Oscillator (Verify — Likely Fine)

**Issue:** The CH9102F uses an internal oscillator in most configurations (no external crystal needed). The BOM doesn't include a crystal for U_USBBRIDGE, which is correct if using the internal-oscillator variant.

**Recommendation:** Verify against the CH9102F datasheet that the selected SOP-16 variant doesn't require an external clock source. Likely fine — this is standard in the maker community.

---

### 10. ESP32 Bulk Capacitance (Minor — Nice-to-Have)

**Issue:** The ESP32S3Core sheet spec mentions 10µF + 100nF at pin 2, which matches the BOM (C_BULK + C_DEC). Espressif reference designs use **22µF bulk** close to the module for Wi-Fi TX stability. The 10µF is close but borderline.

**Recommendation:** Consider bumping C_BULK to 22µF (same 0603/0805 form factor available in X5R/X7R). Low risk either way for a maker board, but the extra 12µF costs nothing and matches Espressif reference designs more closely.

---

## Summary Table

| Category | Rating | Notes |
|----------|--------|-------|
| Architecture & Topology | **Good** | Conservative, appropriate for Rev A |
| Power Design | **Good** | Module approach eliminates most first-pass risk |
| Signal Integrity | **Good** | Series damping on SPI, proper fan drive topology |
| Protection | **Good** | TVS, fuse, ESD on USB, polyfuse on expansion |
| BOM Completeness | **Excellent** | 44/46 lines LOCKED with alternates |
| Documentation | **Excellent** | Unusually thorough for a maker project |
| GPIO Safety | **Good** | Strapping pins properly excluded |
| Potential Bugs | **No open critical bug** | Tach clamp polarity issue #1 has been resolved in docs/BOM |

---

## Bottom Line

**This design is ready for schematic capture and layout.**

- **High value:** Confirm K7812 minimum VIN margin at 19V input after Schottky drop (issue #2).
- **Nice to have:** Document USB power behavior, consolidate GPIO lists, bump ESP32 bulk cap (issues #6, #8, #10).

Everything else is either minor documentation cleanup or acceptable trade-offs for a maker/community-grade board.

---

## Sign-Off

Reviewed for maker/community build scope. Checked against:
- [docs/requirements.md](requirements.md)
- [docs/architecture.md](architecture.md)
- [docs/schematic-plan.md](schematic-plan.md)
- [docs/signal-hierarchy.md](signal-hierarchy.md)
- [docs/gpio-map.md](gpio-map.md)
- [docs/bom-revA.csv](bom-revA.csv)
- All sheet specs under `docs/sheet-specs/`

**Ready to proceed with KiCad schematic capture.**

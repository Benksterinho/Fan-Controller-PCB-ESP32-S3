# Fan Controller Rev A — Complete Recommendations

> Generated from AI-assisted peer review session (2026-05-27).
> Feed this file to GitHub Copilot to update project documentation.

---

## 1. Fixes Already Applied (Verified)

These items were identified during review and have been implemented in the current schematics and docs.
No further action needed — listed here for traceability.

- [x] **GPIO3 pull-up (R1, 10KΩ to 3V3)** — Strapping pin has no internal pull. Floating = undefined JTAG state and potential random boot failures. Pull HIGH selects USB Serial/JTAG (matches external programmer setup). Verified in schematic screenshot.
- [x] **C_BULK upgraded to 22µF 16V X5R 1206 (CL31A226KOHNNNE)** — Previous 10µF was marginal for WiFi TX bursts (~355 mA peak). Espressif reference uses 22µF. Verified in ESP32S3Core parts list.
- [x] **Backlight transistor driver added (Q_BL)** — Display backlight draws ~60 mA (per display datasheet). GPIO max source current is ~40 mA typical. Added MMBT3904 open-collector sink stage with R_BL_BASE (2.2k) and R_BL_PD (100k). Verified in DisplayHeader_ST7789 parts list and schematic screenshot.
- [x] **On-board USB-UART (CH9102F) removed** — Eliminates high-speed USB differential pair routing (highest-risk layout challenge for first PCB). Removes ~20 components. 07_ProgrammingUSB.md archived. 07_ExternalProgrammingHeader.md is now the active programming interface guide.
- [x] **External programming header added** — 1x6 pin header (2.54mm): GND, 3V3, UART_TX, UART_RX, EN_RESET, GPIO0_BOOT. Compatible with M5Stack ESP32 downloader, FTDI cables, and generic USB-TTL adapters.
- [x] **Tactical switches (SW_RESET + SW_BOOT) kept** — Hardware recovery must never depend on working software. Essential for first boot (blank flash), firmware crash recovery, and educational value. Space cost (~25 mm²) is negligible after USB removal.

---

## 2. Documentation Fixes Needed (Must Do)

These items exist correctly in the schematic but are **missing or inconsistent** in the markdown documentation. Update docs to match schematic.

- [x] **Display pin swap documented** — DISP_BL is documented on GPIO40, DISP_RST on GPIO16, DISP_DC on GPIO9, and DISP_CS on GPIO10 across mapping docs.
  - To the parts/wiring section: `U3 IOxx (GPIOxx) -> DISP_BL label (output)`
  - To the architecture rules GPIO mapping list
  - To the hierarchical labels list

- [x] **Add DISP_BL to 11_RootIntegration_CrossSheet.md** — Root display interface section now includes DISP_BL cross-sheet mapping.
  - `ESP32S3Core DISP_BL -> DisplayHeader_ST7789 DISP_BL`

- [x] **Add R_JTAG (GPIO3 pull-up) to 04_ESP32S3Core.md** — Parts table and wiring instructions now include R_JTAG and GPIO3 strapping behavior.
  - Parts table entry: `R_JTAG | 10k 0603 | Pulls GPIO3 HIGH — required because GPIO3 has no internal pull. Selects USB Serial/JTAG source.`
  - Wiring: `R_JTAG one side -> 3V3` / `R_JTAG other side -> U3 IO3 (GPIO3)`

- [x] **Add DISP_BL to 12_EndToEnd_ProjectFlow.md net naming rules** — Quick net naming list now includes DISP_BL.

---

## 3. Architecture Decisions Made (Document in Project README)

These are deliberate design trade-offs. Document the rationale so judges and users understand they are intentional.

- [x] **USB-UART removed for Rev A** — Rationale documented in project README architecture trade-offs.

- [x] **Backlight driven via open-collector NPN (not direct GPIO)** — Rationale documented in project README and display sheet-spec notes.

- [x] **Vin tiers** — Three tiers documented in project README as canonical guidance.
  - 19–24V: recommended, full headroom for both regulators
  - 16–18V: may work, K7812 near dropout depending on load
  - Below 16V: at your own risk, K7812 cannot regulate

---

## 4. Hardware Recommendations (Open for Rev A or Rev B)

These are non-blocking improvements. Decide per item whether to include in Rev A layout or defer.

### For Rev A (if space/time allows)

- [x] **Consider adding TP11 for DISP_BL** — Decision made: deferred to Rev B by default to avoid Rev A layout/risk churn; can be re-opened if board space is clearly available.

### For Rev B

- [ ] **Expansion header (I2C + spare GPIOs)** — Already documented in 10_OptionalExpansionHeader.md. ~500 mA headroom available on 3.3V rail. Adds significant maker value (temp sensors, I2C peripherals).
- [ ] **12V power indicator LED** — No visual feedback on 12V rail currently. Helpful for debugging power-up sequence.
- [x] **SPI_MISO removed for Rev A** — Display MISO path is not implemented and R_MISO is removed from Rev A documentation/build intent.
- [ ] **Reintroduce USB-C** — Either CH9102F USB-UART bridge or ESP32-S3 native USB Serial/JTAG. Only after Rev A layout experience validates routing capability.
- [ ] **Additional bulk cap near ESP32** — Optionally add a second 10–22µF MLCC in parallel for lower ESR and better WiFi TX transient response.

---

## 5. Firmware / Documentation-Only Items (No Hardware Change)

These require no schematic or layout changes — only firmware awareness and user-facing documentation.

- [x] **Fan PWM duty cycle is inverted** — Documented in firmware README bring-up notes.
  ```c
  fan_duty = 1.0 - desired_speed;
  ```

- [x] **Backlight PWM duty cycle is inverted** — Documented in firmware README bring-up notes.
  ```c
  bl_duty = 1.0 - desired_brightness;
  ```

- [x] **GPIO35/36/37 reserved on N16R8** — Documented in firmware README and ESP32S3Core/gpio-map docs.

- [x] **GPIO46 must not be driven HIGH at reset** — Warning documented in firmware README.

- [x] **GPIO19/20 = USB D-/D+ on ESP32-S3** — Trade-off warning documented in firmware README.

- [x] **UART TX/RX crossover warning** — Documented in firmware README bring-up notes.
  - Adapter RX -> board UART_TX
  - Adapter TX -> board UART_RX
  - This is a universal source of confusion for beginners.

---

## 6. Electrical Budget Summary (Reference)

### 3.3V Current Budget (K7803-1000R3: 1A rated)

| Consumer | Typical | Worst Case |
|----------|---------|------------|
| ESP32-S3 (WiFi TX + CPU) | ~200 mA | ~400 mA |
| Display logic (ST7789 SPI) | ~8 mA | ~10 mA |
| Display backlight (via Q_BL) | ~25 mA | ~60 mA |
| Pull-ups, LEDs, misc | ~5 mA | ~10 mA |
| **Total** | **~238 mA** | **~480 mA** |

Headroom: ~500 mA available for expansion.

### GPIO Current Budget (DISP_BL path)

| Without Q_BL | With Q_BL |
|--------------|-----------|
| ~60 mA (overcurrent!) | ~1.2 mA (safe) |

---

## 7. Scorecard (Current State)

| Category | Score | Notes |
|----------|-------|-------|
| Schematic Correctness | 9/10 | All electrical issues resolved |
| Documentation Consistency | 7.5/10 | Four doc gaps identified above |
| Architecture | 9/10 | Clean hierarchy, proper signal ownership |
| Protection & Safety | 8.5/10 | Three-layer input + USB ESD removed (no longer needed) |
| Debug/Observability | 9/10 | 10 test points + buttons + external header |
| Educational Value | 9/10 | Clear structure, maker-friendly, learnable |
| Contest Readiness | 8.5/10 | Fix doc items above, then ready for layout |

**Overall: 8.5/10** — up from 7.8 at initial review.

---

## 8. Pre-Layout Checklist

Before starting PCB routing, confirm:

- [x] All doc items in Section 2 are updated
- [x] DISP_BL GPIO assignment is consistent across all files
- [ ] ERC passes with no real errors
- [ ] Footprints assigned to all symbols
- [ ] Critical pinouts verified against datasheets
- [ ] BOM is complete with LCSC part numbers
- [ ] Project saved and committed to git

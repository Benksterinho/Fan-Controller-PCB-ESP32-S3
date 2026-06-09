# BOM RevA Status

This file is synchronized to [docs/bom-revA.csv](docs/bom-revA.csv) and reflects the current Rev A sourcing state.

## Summary
- Total BOM line items: 44
- LOCKED: 42
- SOURCED: 0
- PENDING: 0

## Architecture Change Note (2026-05-28)
- Active Rev A programming interface migrated from on-board `ProgrammingUSB` to `ExternalProgrammingHeader`.
- USB bridge-related line items were removed from active BOM.
- Legacy ProgrammingUSB verification data below is retained as archived reference only.

## ProgrammingUSB Interactive Verification (2026-05-21, Archived)
- Live interactive checks on fat.lcsc.com were used as the highest-confidence pass for changed ProgrammingUSB entries.
- Results:
  - C2858418 (CH9102F, QFN-24-EP 4x4): manual browser check later confirmed in-stock quantity.
  - C130723 (NCD0402G1 LED, 0402): product page resolves and is In-Stock.
  - C23186 (5.1k 0603 target): later manual browser check confirmed product page, package 0603, and in-stock quantity.
  - C99198 (10k 0603 target): manual browser check confirmed product page, package 0603, and in-stock quantity.
- Action taken in CSV:
  - Corrected historical wrong mapping: replaced `C2845310` (tactile switch) with `C2858418` for the CH9102F bridge row.
  - Replaced both 10k rows with C99198 and promoted to LOCKED after manual verification.
  - Promoted C2858418 to LOCKED after manual verification.
  - Promoted C23186 to LOCKED after manual verification.
  - Kept C130723 as LOCKED.

## Verification Gate Run (2026-05-21, v1)
- Gate definition source: docs/sourcing-checklist.md (`Verification Gate (v1)`).
- Evidence source preference used: browser-rendered `fat.lcsc.com` snapshots.
- Notes:
  - Direct curl checks now frequently return challenge pages (`Challenge Validation`), so they were used only as a secondary signal.
  - Browser snapshots provided stable `MPN`, `Packaging`, and stock/not-found signals.

### Applied Results
- C2858418 (CH9102F): Identity PASS (`MPN=CH9102F`, `Packaging=QFN-24-EP(4x4)`), Availability PASS (`In-Stock: 4,742`) -> `LOCKED`.
- C969151 (CP2102N-A02-GQFN24R): Identity PASS (`MPN=CP2102N-A02-GQFN24R`, `Packaging=QFN-24-EP(4x4)`), Availability PASS (`In-Stock: 9,595`) -> validated alternate candidate.
- C23186 (5.1k target): Identity PASS (`MPN=0603WAF5101T5E`, `Packaging=0603`), Availability PASS (`In-Stock: 3,559,200`) -> `LOCKED`.
- C99198 (10k target): Identity PASS (`MPN=RC0603JR-0710KL`, `Packaging=0603`), Availability PASS (`In-Stock: 2,616,700`) -> `LOCKED`.
- C130723 (LED): Identity PASS (`MPN=NCD0402G1`, `Packaging=0402`), Availability PASS (`In-Stock: 1,690`) -> `LOCKED`.

## Three-Action Execution Run (2026-05-21)
- Scope requested: execute all three actions (find replacements, verify with gate, patch/recount).
- Discovery source: JLC API (same source family used by kicad-jlcpcb-tools).
- Candidate outcomes:
  - 10k 0603 final selection: `C99198` (manual browser verification confirms in-stock quantity).
  - 5.1k 0603 candidate retained: `C23186` (API response: in-stock, package 0603).
  - CH9102F bridge candidate: `C2858418` (manual browser verification confirms in-stock quantity).
- Browser gate constraints during run:
  - `fat.lcsc.com` returned `Page Not Found` for resistor candidate pages (both short and slug URLs).
  - `www.lcsc.com` product pages repeatedly returned CloudFront `502` in this environment.
- Action taken:
  - Updated both 10k rows in CSV to `C99198` and promoted to `LOCKED` after manual browser verification.
  - Updated `C23186` to `LOCKED` after manual browser verification.
  - Updated `C2858418` to `LOCKED` after manual browser verification.
  - Current counts: `LOCKED 49`, `PENDING 0`.

## Locked Highlights
- Input protection path is locked, including F1:
  - Littelfuse 0466002.NRHF, LCSC C3105
- Fan tach clamp is locked:
  - onsemi MBR0520LT1G, LCSC C23848
- Fan4Wire is lock-clear after fresh root ERC with 0 errors:
  - J_FAN/Q1/R_BASE/R_BPD/R_TACH_PU/R_TACH_SERIES/D_TACH_CLAMP/C_FAN_BULK/C_FAN_HF
- DisplayHeader_ST7789, ExternalProgrammingHeader, and Fan4Wire connector orientation update (2026-06-07):
  - J_DISP switched to right-angle THT header C32713267 (hanxia HX PZ2.54-1x8P WZ)
  - J_PROG switched to right-angle THT header C42453955 (hanxia HX PZ2.54-1x6P WZ-P)
  - J_FAN switched to right-angle THT header C32713263 (hanxia HX PZ2.54-1x4P WZ)
  - Display/program support passives remain unchanged: C2909394, C95177, C307331, C181119, C4190, C25803
- Core power, ESP32 module, display interface, and USB bridge support parts are locked in the CSV.

## Remaining Open Items (PENDING)
- None in the current CSV.

## Notes
- The previous version of this status file contained outdated counts and stale pending sections.
- The CSV remains the source of truth for lock state and LCSC codes.
- Fresh root ERC now passes with 0 errors; remaining ERC items are warnings only.

## Cap Rationalization Review (2026-06-04)

Board layout is getting crowded from electrolytic caps. All caps remain LOCKED and placed as-designed;
this section records which ones are candidates for removal or downsize before finalising placement.
No changes made yet — review during layout completion.

### Candidates to Drop

| Ref | Value | Footprint | Reason |
|-----|-------|-----------|--------|
| C12_IN_BULK | 100uF/50V | BD8mm can | C_IN_BULK already buffers VIN_PROTECTED upstream. Drop if trace from C_IN_BULK to U1 input is <4cm and wide (≥0.5mm). |
| C33_IN_BULK | 100uF/50V | BD8mm can | Same argument. Both K7803 and K7812 share VIN_PROTECTED. One upstream bulk cap (C_IN_BULK) is sufficient with tight routing. |
| C_FAN_BULK | 47uF/25V | BD6.3mm can | Optional local bulk at J_FAN. Drop if J_FAN is placed within ~3cm of C12_OUT_BULK with a wide/direct 12V copper path. Keep if fan connector is far from the power stage. |

### Candidates to Downsize

| Ref | Current | Proposed | Reason |
|-----|---------|----------|--------|
| C33_IN_BYP | 10uF/50V X5R 1210 | 1uF/50V X7R 0603 (C15849 — same as C12_IN_BYP) | 1210 is massively oversized for a bypass cap on the module input. 1uF 0603 does the same HF bypass job, saves large board area, and consolidates to an already-stocked part. |

### Keep Unconditionally

- **C_IN_BULK** (100uF/50V can, InputProtection) — first bulk reservoir on VIN_PROTECTED, always required.
- **C12_OUT_BULK** (47uF/25V, BD6.3mm) — required for K7812 stability and fan startup/PWM load transients.
- **C33_OUT_BULK** (100uF/10V, BD6.3mm) — required for K7803 output stability and ESP32 WiFi TX transients.
- **C12_IN_BYP**, **C33_OUT_BYP**, **C12_OUT_BYP**, **C_FAN_HF**, **C_IN_BYP**, **C_DISP_VCC** — all 0402/0603 HF bypass caps, tiny, always keep.
- **C_DEC**, **C_BULK**, **C_EN** — ESP32 decoupling per Espressif reference design, always keep.

### Decision Gate

Before finalising placement: confirm J_FAN distance to C12_OUT_BULK, and measure trace length from C_IN_BULK to U1/U2 input pins on the PCB.
If both routing conditions are met, removing C12_IN_BULK + C33_IN_BULK + replacing C33_IN_BYP saves 2 large BD8mm cans and one 1210 from the board.
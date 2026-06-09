# End-to-End Schematic Capture How-To (Beginner Version)

Scope: complete project flow from blank subsheets to commit-ready schematic.

## What this guide does
This guide gives you one linear checklist from project setup to final commit.

Think of it as:
- Build sheets one by one
- Validate each sheet locally
- Integrate at root
- Run full ERC and lock

## Quick net naming rules (use exactly these)
- VIN_PROTECTED
- 12V
- 3V3
- GND
- FAN_PWM
- FAN_TACH
- UART_TX
- UART_RX
- EN_RESET
- GPIO0_BOOT
- SPI_SCK
- SPI_MOSI
- DISP_CS
- DISP_DC
- DISP_RST
- DISP_BL

## Quick LCSC reference
1. InputProtection: F1 C3105, D1 C16015, D_TVS_IN C135062, C_IN_BULK C72480
2. Power12V: U1 C5369648, caps C72480/C15849/C3029614/C307331
3. Power3V3: U2 C50396472, caps C72480/C380537/C2887276/C92487
4. Core interfaces: ESP32-S3 module C2913202, external programming header, tach clamp C23848

## Phase 1: project preparation
1. Open project in KiCad and confirm there are no parse errors.
2. Set grid to 50 mil for symbol capture.
3. Create or verify root-sheet skeleton.
4. Ensure sheet names and file names match the how-to files exactly.

## Phase 2: subsheet capture order
1. Complete 01_InputProtection.md
2. Complete 02_Power12V.md
3. Complete 03_Power3V3.md
4. Complete 05_Fan4Wire.md
5. Complete 06_DisplayHeader_ST7789.md
6. Complete 07_ExternalProgrammingHeader.md
7. Complete 08_TestPoints.md
8. Complete 10_OptionalExpansionHeader.md only if needed
9. Complete 04_ESP32S3Core.md last, after signal names are fixed

Archived reference:
- 07_ProgrammingUSB.md remains for historical reference only and is not part of active Rev A integration.

Rule during this phase:
- Run sheet-level local sanity checks after each sheet.

## Phase 3: root integration
1. Follow 11_RootIntegration_CrossSheet.md.
2. Sync sheet pins for every subsheet.
3. Wire power and signal backbones at root.
4. Run full-project ERC and fix real errors.

## Phase 4: part-data quality gate
1. Annotate all symbols.
2. Fill required fields:
- Value
- Footprint
- MPN
- LCSC part number
- Datasheet URL
3. Verify critical pinouts and footprints against datasheets.

## Phase 5: pre-PCB gate
1. Assign footprints to all symbols.
2. Check package orientation and pin-1 assumptions.
3. Re-run ERC after footprint updates.
4. Save project and create a dated backup snapshot.

## Phase 6: commit-ready finish
1. Review working tree and keep only intended files.
2. Run final full-project ERC and export report.
3. Commit with a clear message.

## Common mistakes to avoid
1. Using inconsistent net names between sheets.
2. Skipping Sync Sheet Pins after child-sheet changes.
3. Running only standalone-sheet ERC and assuming integration is clean.
4. Committing generated lock/history/report artifacts.

## Final checks (2-minute checklist)
1. All required subsheets are captured and integrated.
2. Root-sheet nets are consistent with child-sheet labels.
3. Full-project ERC has no unresolved real errors.
4. Footprints and core part metadata are complete.
5. Git status contains only intentional changes.

## Done definition
1. Schematic is capture-complete.
2. Cross-sheet wiring is validated.
3. ERC is clean or only has justified warnings.
4. Project is commit-ready and documented.

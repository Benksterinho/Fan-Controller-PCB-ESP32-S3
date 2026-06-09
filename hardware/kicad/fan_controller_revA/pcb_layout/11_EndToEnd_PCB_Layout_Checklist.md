# End-to-End PCB Layout Checklist (Rev A)

Scope: practical board-placement and early-routing flow after schematic completion.

Use this as the single execution checklist in PCB Editor.

## Absolute beginner controls (quick reference)
1. M move selected footprint/group.
2. R rotate selected footprint/group.
3. E open properties.
4. X route track.
5. V drop via while routing.
6. B refill all zones.
7. Del delete selected item.
8. Ctrl+S save.

If a shortcut differs in your KiCad version, open Preferences -> Hotkeys and search by command name.

## Prerequisites
1. All required subsheets are complete in schematic.
2. ERC is clean or only has intentional warnings.
3. Footprints are assigned for all populated parts.
4. Board outline and mounting constraints are defined.

## Phase 1: sync and prepare
1. In Schematic Editor, save all sheets.
2. Run Update PCB from Schematic with full synchronization.
3. Open PCB Editor and inspect newly added/moved footprints.
4. Open and keep nearby: 00_SheetSelection_AfterUpdate.md.
5. Set board rules baseline: net classes, clearances, via sizes, copper layers.

Beginner detail:
1. Use menu path Tools -> Update PCB from Schematic.
2. After update, do not start routing immediately.
3. First complete rough placement of all clusters by sheet.

## Phase 2: floorplan by subsheet clusters
1. Place InputProtection cluster using 01_InputProtection_layout.md.
2. Place Power12V cluster using 02_Power12V_layout.md.
3. Place Power3V3 cluster using 03_Power3V3_layout.md.
4. Place ESP32S3Core cluster using 04_ESP32S3Core_layout.md.
5. Place Fan4Wire cluster using 05_Fan4Wire_layout.md.
6. Place DisplayHeader_ST7789 cluster using 06_DisplayHeader_ST7789_layout.md.
7. Place ExternalProgrammingHeader cluster using 07_ExternalProgrammingHeader_layout.md.
8. Place TestPoints cluster using 08_TestPoints_layout.md.
9. Place OptionalExpansionHeader cluster if used, via 10_OptionalExpansionHeader_layout.md.

Cluster rule:
- Select footprints by Sheetname/Sheetfile first; fallback to RefDes list when needed.

Beginner detail:
1. Filter one sheet at a time, move that group, then clear filter.
2. Save after each cluster to avoid losing layout progress.

## Phase 3: lock anchors and validate access
1. Lock board-edge connectors after orientation is finalized.
2. Confirm cable/tool access for J_PWR, J_FAN, J_DISP, J_PROG, and J_EXP if populated.
3. Confirm button and test-point finger/probe access.
4. Confirm ESP32 antenna keepout remains clear of copper and metal.

## Phase 4: route power first
1. Route VIN entry/protection path first.
2. Route VIN_PROTECTED distribution to both regulator clusters.
3. Route 12V trunk to fan cluster.
4. Route 3V3 trunk to ESP32, display, programming header, and optional expansion.
5. Place stitching vias and return paths to keep current loops compact.

Beginner detail:
1. Press X to route and finish each segment with double-click.
2. Use wider tracks for power nets than for logic nets.
3. Press B after each power-net block to repour zones.

## Phase 5: route control and interfaces
1. Route EN_RESET and GPIO0_BOOT to programming header with short direct paths.
2. Route UART_TX/UART_RX pair cleanly.
3. Route SPI_SCK, SPI_MOSI, DISP_CS, DISP_DC, and DISP_RST to display header. Keep display MISO/SDO unconnected on Rev A.
4. Route FAN_PWM and FAN_TACH with attention to noise separation.
5. Keep sensitive feedback and control traces away from high-current fan power path.

## Phase 6: copper and integrity pass
1. Pour ground zones and repour.
2. Inspect neck-downs and bottlenecks on VIN_PROTECTED, 12V, and 3V3.
3. Verify decoupling capacitor return paths are short and local.
4. Check for unintended copper near ESP32 antenna keepout.

## Phase 7: DRC and manufacturability
1. Run DRC and clear real violations.
2. Resolve courtyard/silkscreen overlaps at connectors and buttons.
3. Confirm reference designators and polarity markers are readable.
4. Verify test points are not blocked by tall components or cable overhang.

Beginner detail:
1. Run DRC from Inspect -> Design Rules Checker.
2. Double-click each violation to jump to board location.
3. Re-run DRC after every fix batch until clean.

## Phase 8: final review gate
1. Ratsnest is clean and no footprint remains unplaced.
2. Critical nets have intentional routing and width choices.
3. Optional parts are clearly marked DNP where intended.
4. Save, generate updated reports, and prepare commit.

## Quick done definition
1. All subsheet clusters are placed and integrated.
2. Power and control routing priorities were followed.
3. DRC passes with no unresolved real errors.
4. Board is ready for fabrication-output generation.

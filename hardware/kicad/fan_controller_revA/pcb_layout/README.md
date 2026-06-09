# PCB Layout Subsheet Guide Index

This folder contains PCB Editor placement/routing guides mapped to the schematic subsheets.

Use this flow after every schematic update:
1. In Schematic Editor, run Update PCB from Schematic.
2. In PCB Editor, refresh ratsnest and run DRC after major placement steps.
3. Work one subsheet cluster at a time using the matching file below.

## Absolute beginner quick start
1. Open PCB Editor and enable the right sidebar panels so Search/Filter tools are visible.
2. Open 00_SheetSelection_AfterUpdate.md and select one subsheet cluster first.
3. Move selected footprints with M, rotate with R, and place with left click.
4. Start routing with X, click to place corners, and double-click to finish a track.
5. Press B to refill copper zones after major routing edits.
6. Run DRC from the Inspect menu after each phase.

## Common PCB Editor shortcuts
1. M: Move selected footprint or item.
2. R: Rotate selected footprint or item.
3. E: Open properties for selected item.
4. Del: Delete selected item.
5. X: Route tracks.
6. V: Drop a via while routing.
7. B: Refill all zones.
8. Ctrl+S: Save.

If a shortcut differs in your KiCad version, open Preferences -> Hotkeys and search by command name.

## Files
- 00_SheetSelection_AfterUpdate.md
- 01_InputProtection_layout.md
- 02_Power12V_layout.md
- 03_Power3V3_layout.md
- 04_ESP32S3Core_layout.md
- 05_Fan4Wire_layout.md
- 06_DisplayHeader_ST7789_layout.md
- 07_ExternalProgrammingHeader_layout.md
- 08_TestPoints_layout.md
- 10_OptionalExpansionHeader_layout.md
- 11_EndToEnd_PCB_Layout_Checklist.md

## Recommended layout order
1. 11_EndToEnd_PCB_Layout_Checklist.md
2. 00_SheetSelection_AfterUpdate.md
3. 01_InputProtection_layout.md
4. 02_Power12V_layout.md
5. 03_Power3V3_layout.md
6. 04_ESP32S3Core_layout.md
7. 05_Fan4Wire_layout.md
8. 06_DisplayHeader_ST7789_layout.md
9. 07_ExternalProgrammingHeader_layout.md
10. 08_TestPoints_layout.md
11. 10_OptionalExpansionHeader_layout.md

## Placement strategy summary
1. Place power entry and protection near board input edge.
2. Keep regulator loops compact before placing digital logic.
3. Place ESP32 module for RF keepout first, then fan/display/programming connectors.
4. Reserve board edges for external connectors and test points.
5. Route high-current and noisy nets before sensitive digital feedback nets.

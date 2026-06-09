# Subsheet How-To Index

Use one file per schematic sheet while capturing symbols and wiring.

These guides are written for a fully manual KiCad workflow. You do not need an AI-generated starting schematic. If a sheet does not exist yet, create it from the root sheet first, then follow the matching guide.

## Files
- 01_InputProtection.md
- 02_Power12V.md
- 03_Power3V3.md
- 04_ESP32S3Core.md
- 05_Fan4Wire.md
- 06_DisplayHeader_ST7789.md
- 07_ExternalProgrammingHeader.md
- 07_ProgrammingUSB.md
- 08_TestPoints.md
- 10_OptionalExpansionHeader.md
- 11_RootIntegration_CrossSheet.md
- 12_EndToEnd_ProjectFlow.md

## Recommended workflow
1. Open the root sheet in KiCad.
2. Create the next child sheet manually if it does not exist yet.
3. Name the sheet and file exactly as the guide expects.
4. Open that child sheet and follow the matching how-to file step by step.
5. Run ERC after each subsheet.
6. Return to root sheet and sync sheet pins.
7. Run full-project ERC before footprint assignment.

## Shortcut hints
1. `A` adds a symbol.
2. `W` starts a wire.
3. `L` adds a net label.
4. `P` adds a power symbol.
5. `R` rotates the selected item.
6. `M` moves the selected item.
7. `G` drags while keeping wires attached.
8. `E` opens item properties.
9. `Del` deletes the selected item.
10. `Ctrl+S` saves.
11. `Esc` cancels the current tool.

If a shortcut differs in your KiCad version, use the toolbar or context-menu equivalent and keep the workflow moving.

Note:
- `07_ProgrammingUSB.md` is archived reference content only.
- `07_ExternalProgrammingHeader.md` is the active Rev A programming-interface guide.

# KiCad Blank-Project Quickstart (First Manual Subsheet)

Use this when starting from a blank or unreliable project state.

Goal for this page: create the root sheet, create the first child sheet (`InputProtection`), place the first parts, and run ERC once.

## 0) Open project and schematic editor

1. Open KiCad Project Manager.
2. Open project file: `hardware/kicad/fan_controller_revA/fan_controller_revA.kicad_pro`.
3. Open Schematic Editor.

## 1) Clean root sheet state

1. If root sheet has random leftover symbols/wires from failed imports, delete them.
2. Save (`Ctrl+S`).
3. Keep root file name as `fan_controller_revA.kicad_sch`.

Shortcut notes:
- `Esc` cancels current tool.
- `M` moves selected item.
- `Del` deletes selected item.

## 2) Create first child sheet (`InputProtection`)

1. In root sheet, add a hierarchical sheet symbol.
2. Set sheet name: `InputProtection`.
3. Set sheet file: `InputProtection.kicad_sch`.
4. Open the child sheet by entering the sheet symbol.

If KiCad asks to create the file, confirm.

## 3) Place minimum symbols on InputProtection

Use [subschematic-guide.md](subschematic-guide.md) and [01_InputProtection.md](../hardware/kicad/fan_controller_revA/howto_subsheets/01_InputProtection.md) as source of truth.

1. Place input connector (`J_PWR`).
2. Place TVS diode (`D_TVS_IN`) from VIN_RAW to GND.
3. Place reverse-polarity diode (`D1` SS36) in series path.
4. Place fuse (`F1`) in series path.
5. Place bypass capacitor (`C_IN_BYP`) from protected VIN to GND.

Shortcut notes:
- `A` add symbol.
- `R` rotate selected symbol.
- `E` edit properties/value/reference.

## 4) Wire and label the sheet

1. Draw wires for the power chain:
   - connector VIN -> fuse -> diode -> protected output.
2. Add power and net labels:
   - `VIN_RAW`
   - `VIN_PROTECTED`
   - `GND`
3. Add hierarchical labels for those same nets on this child sheet.

Shortcut notes:
- `W` wire.
- `L` net label.
- `P` power symbol.

## 5) Return to root and sync sheet pins

1. Go back to root sheet.
2. Select the `InputProtection` sheet symbol.
3. Run Sync Sheet Pins.
4. Confirm root sheet now shows sheet pins for `VIN_RAW`, `VIN_PROTECTED`, and `GND`.

## 6) Run first ERC pass

1. Run ERC on the child sheet first.
2. Fix hard errors (unconnected mandatory pins, power issues, missing labels).
3. Run ERC at root once.

Do not continue to the next sheet until this first sheet is clean enough to trust.

## 7) Save checkpoint

1. Save all sheets.
2. Optional but recommended: commit after first clean `InputProtection` pass.

Suggested next step:
Proceed to [02_Power12V.md](../hardware/kicad/fan_controller_revA/howto_subsheets/02_Power12V.md), then [03_Power3V3.md](../hardware/kicad/fan_controller_revA/howto_subsheets/03_Power3V3.md).
# Select Subsheet Footprints After Full Schematic Update

Use this guide after Update PCB from Schematic so you can place one subsheet at a time in PCB Editor.

## Baseline sync flow
1. Save all schematic sheets.
2. Run Update PCB from Schematic.
3. Choose full update and keep existing placement only where intentional.
4. Open PCB Editor and confirm all new footprints exist.

## Absolute beginner: where to click first
1. In PCB Editor, turn on the right sidebar panels if hidden.
2. Open the Search/Filter area and set object type to Footprints.
3. Click once on any footprint, then press E to confirm metadata fields are present.
4. In the properties/details view, verify fields include Sheetname and Sheetfile.
5. Return to Search/Filter and use those fields to select one full subsheet cluster.

## Absolute beginner: move a selected cluster
1. After filtering, press Ctrl+A in the filtered list if needed to select all matches.
2. Press M to move the selected footprints as one group.
3. Move cursor to the target area and left click to place.
4. Press R before placing if the group orientation should rotate.
5. Repeat until each subsheet cluster has a rough floorplan position.

## Method A: select by sheet metadata (preferred)

KiCad footprints imported from hierarchical sheets carry two metadata fields: **Sheetname** and **Sheetfile**. You use these to select every footprint in one subsheet at once.

### Step-by-step: using Inspect → Board Statistics / Net Inspector is NOT the route. Use the Selection Filter + right-click menu instead.

#### Option A1 — Right-click "Select" menu (fastest, no typing)
1. Click on any footprint that you know belongs to the target subsheet (e.g. click D1 for InputProtection).
2. Right-click → **Select** → **All Footprints in Same Hierarchical Sheet**.
3. All footprints from that subsheet are now selected (highlighted).
4. Press **M** to move them as a group, left-click to place.
5. Press **Ctrl+S** to save.

> Shortcut summary: click one footprint → right-click → Select → All Footprints in Same Hierarchical Sheet → M → left-click → Ctrl+S

#### Option A2 — Inspect menu query (KiCad 7/8 with query filter)
1. Menu bar: **Inspect** → **Board Statistics** is not relevant here. Use instead:
2. Menu bar: **Edit** → **Find** (shortcut **Ctrl+F**).
3. In the Find toolbar at the bottom of the canvas, type the subsheet name e.g. `Fan4Wire`.
4. Change the search scope drop-down from "Reference" to **Sheet Name** (or "Hierarchical Sheet").
5. Click **Select All** or press **Enter** repeatedly to step through, then **Ctrl+A** to select all matches.
6. Press **M** to move the selected group.

> If the scope drop-down does not have "Sheet Name", use Option A1 or A3 instead.

#### Option A3 — Selection Filter + footprint properties filter (KiCad 8)
1. Open **View** → **Selection Filter** (or press **F6** / look for the panel bottom-right).
2. Ensure **Footprints** checkbox is ticked; untick all others to avoid grabbing tracks or zones.
3. On the canvas, hold **Ctrl** and rubber-band select the entire board area to grab all footprints.
4. With everything footprint-selected, open **Edit** → **Find** (**Ctrl+F**), type the subsheet name, and use **Select** to narrow.
   - Alternatively after step 3: right-click → **Select** → **Filter Selection** → filter field **Sheetname** = `Fan4Wire`.
5. Deselect footprints that do not match (Shift-click to remove individual items if needed).
6. Press **M** to move.

#### Option A4 — Scripting console one-liner (power users, KiCad 8)
1. Menu bar: **Tools** → **Scripting Console** (shortcut **Ctrl+`** on some builds).
2. Paste and run (replace `Fan4Wire` with the target sheet name):
   ```python
   import pcbnew; b=pcbnew.GetBoard()
   fps=[f for f in b.GetFootprints() if f.GetSheetname()=="Fan4Wire"]
   for f in fps: f.SetSelected()
   pcbnew.Refresh()
   ```
3. Return to the canvas; the matching footprints are now selected.
4. Press **M** to move as a group.

### Step-by-step example: select the Fan4Wire cluster (Option A1 recommended)
1. In PCB Editor, click any known Fan4Wire part — e.g. Q_FAN (the gate-drive transistor).
2. Right-click → **Select** → **All Footprints in Same Hierarchical Sheet**.
3. Confirm the status bar at the bottom shows multiple footprints selected.
4. Press **M**, move the group to the fan-connector corner, left-click to drop.
5. Press **Ctrl+S**.

### Sheet name values for this project
| Subsheet | Sheetname field | Sheetfile field |
|---|---|---|
| InputProtection | InputProtection | InputProtection.kicad_sch |
| Power12V | Power12V | Power12V.kicad_sch |
| Power3V3 | Power3V3 | Power3V3.kicad_sch |
| ESP32S3Core | ESP32S3Core | ESP32S3Core.kicad_sch |
| Fan4Wire | Fan4Wire | Fan4Wire.kicad_sch |
| DisplayHeader_ST7789 | DisplayHeader_ST7789 | DisplayHeader_ST7789.kicad_sch |
| ExternalProgrammingHeader | ExternalProgrammingHeader | ExternalProgrammingHeader.kicad_sch |
| TestPoints | TestPoints | TestPoints.kicad_sch |
| OptionalExpansionHeader | OptionalExpansionHeader | OptionalExpansionHeader.kicad_sch |

## Method B: select by reference list (fallback)
If Sheetname filtering is unavailable, use each subsheet guide reference list and multi-select by RefDes.

Practical tips:
1. Use Find to jump to each RefDes.
2. Add each footprint to the selection and move as one cluster.
3. Lock anchor parts after initial floorplan placement.
4. Good anchors to lock early are board-edge connectors and large modules.

## Method C: schematic cross-probing (fallback)
1. In Schematic Editor, open one subsheet.
2. Select symbols in that subsheet.
3. Use cross-probe to highlight linked footprints in PCB Editor.
4. Move highlighted footprints as a group.

## Quick troubleshooting
1. If nothing highlights, verify Update PCB from Schematic completed without skipped items.
2. If Sheetname field appears missing, test with Sheetfile or use RefDes fallback.
3. If only one footprint moves, you likely lost group selection; undo and reselect full filter result.
4. If ratsnest lines look chaotic, do not route yet; finish rough placement of all clusters first.

## Useful shortcuts during selection and placement
1. M: Move selected item/group.
2. R: Rotate selected item/group.
3. E: Open selected item properties.
4. Del: Remove accidentally placed item.
5. Ctrl+S: Save after each cluster placement.

## Sanity checks before routing
1. Every selected cluster belongs to exactly one subsheet.
2. Connectors are oriented for cable access and board edge clearance.
3. No footprint is left unplaced outside board outline.
4. Ratsnest lines mostly remain local to each cluster before final integration.

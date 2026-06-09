# OptionalExpansionHeader PCB Layout Guide

Target subsheet: OptionalExpansionHeader

## Footprints in this cluster
- J_EXP
- F_EXP
- R_EXP1
- R_EXP2
- R_EXP3
- R_EXP4
- R_EXP5
- R_EXP6
- R_EXP7
- R_EXP8

## Select this subsheet after schematic update

### Fastest method: right-click → Select → All Footprints in Same Hierarchical Sheet
1. Click any known OptionalExpansionHeader footprint — e.g. **J_EXP** — on the canvas.
2. Right-click → **Select** → **All Footprints in Same Hierarchical Sheet**.
3. Confirm J_EXP, F_EXP, and the resistor bank are highlighted.
4. Press **M** to move the group to a board-edge expansion region, left-click to place.
5. Press **Ctrl+S** to save.

### Fallback: select RefDes list above as one cluster.

Key shortcuts: **M** move · **R** rotate · **E** properties · **Ctrl+S** save · **Ctrl+Z** undo.
4. Rotate with R so pin numbering follows your harness direction.

## Placement intent
1. Place J_EXP on board edge for expansion-cable access.
2. Place F_EXP close to 3V3 entry to the header.
3. Place resistor bank near header pins for clean GPIO breakout.
4. Keep optional expansion zone away from ESP32 antenna keepout and fan high-current loop.

## Routing priorities
1. Route fused 3V3 and GND to header first.
2. Route each approved GPIO through its series resistor before reaching J_EXP.
3. Keep expansion traces orderly to avoid accidental swaps.

Beginner routing steps:
1. Route 3V3 into F_EXP first, then from F_EXP to J_EXP power pin.
2. Route GND to J_EXP with a direct path.
3. Route one GPIO at a time through its matching R_EXP footprint.
4. Rename or highlight routed nets as you go to avoid line swaps.

## Placement done criteria
1. Fused power path to header is obvious and short.
2. Eight GPIO lines pass through resistor bank with minimal crossings.
3. Header pinout is readable and safe for field use.

# Power12V PCB Layout Guide

Target subsheet: Power12V

## Footprints in this cluster
- U1
- C12_IN_BULK
- C12_IN_BYP
- C12_OUT_BULK
- C12_OUT_BYP

## Select this subsheet after schematic update

### Fastest method: right-click → Select → All Footprints in Same Hierarchical Sheet
1. Click any known Power12V footprint — e.g. **U1** — on the canvas.
2. Right-click → **Select** → **All Footprints in Same Hierarchical Sheet**.
3. Confirm U1 and all support capacitors are highlighted.
4. Press **M** to move, **R** to rotate for best pin direction, left-click to place.
5. Press **Ctrl+S** to save.

### Fallback: select the RefDes list above as one group.

Key shortcuts: **M** move · **R** rotate · **E** properties · **Ctrl+S** save · **Ctrl+Z** undo.

## Placement intent
1. Place U1 close to the protected-input distribution path from InputProtection.
2. Keep C12_IN_BULK and C12_IN_BYP tight to U1 VIN/GND pins.
3. Keep C12_OUT_BULK and C12_OUT_BYP tight to U1 VOUT/GND pins.
4. Leave space for thermal relief and airflow around U1.

## Routing priorities
1. Route compact input loop: VIN_PROTECTED -> U1 -> input caps -> GND return.
2. Route compact output loop: U1 VOUT -> output caps -> GND return.
3. Keep 12V trunk short from U1 output toward Fan4Wire.
4. Keep ground return low impedance and avoid narrow choke points.

Beginner routing steps:
1. Press X and route VIN_PROTECTED into U1 first.
2. Route U1 to nearest input caps next.
3. Route VOUT to output caps, then to 12V trunk.
4. Press B to repour zones and inspect current path shape.

## Placement done criteria
1. Input/output capacitor loops around U1 are visibly compact.
2. 12V handoff direction toward fan cluster is obvious.
3. No long detours in VIN_PROTECTED or 12V high-current paths.

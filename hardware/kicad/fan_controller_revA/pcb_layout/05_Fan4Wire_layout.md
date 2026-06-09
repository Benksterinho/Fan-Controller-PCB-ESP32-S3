# Fan4Wire PCB Layout Guide

Target subsheet: Fan4Wire

## Footprints in this cluster
- J_FAN
- Q1
- R_BASE
- R_BPD
- R_TACH_PU
- R_TACH_SERIES
- D_TACH_CLAMP
- C_FAN_BULK
- C_FAN_HF

## Select this subsheet after schematic update

### Fastest method: right-click → Select → All Footprints in Same Hierarchical Sheet
1. Click any known Fan4Wire footprint — e.g. **J_FAN** or **Q1** — on the canvas.
2. Right-click → **Select** → **All Footprints in Same Hierarchical Sheet**.
3. Confirm J_FAN, Q1, and tach parts are highlighted.
4. Press **M** to move the cluster to the board-edge fan-connector area, **R** to rotate until connector pins match cable direction, left-click to place.
5. Press **Ctrl+S** to save.

### Fallback: select RefDes list above as one cluster.

Key shortcuts: **M** move · **R** rotate · **E** properties · **Ctrl+S** save · **Ctrl+Z** undo.

## Placement intent
1. Put J_FAN on board edge for cable strain relief and access.
2. Place C_FAN_BULK and C_FAN_HF near J_FAN 12V/GND pins.
3. Place Q1 + R_BASE + R_BPD as a tight PWM driver cell.
4. Place tach conditioning parts together (R_TACH_PU, R_TACH_SERIES, D_TACH_CLAMP).

## Routing priorities
1. Route 12V and GND high-current path to fan connector first.
2. Route PWM sink path short: Q1 collector to J_FAN PWM pin.
3. Route FAN_TACH as a quieter trace back to ESP32 area.
4. Keep tach conditioning close to each other and away from 12V switching currents.

Beginner routing steps:
1. Route 12V/GND first using wider tracks than logic signals.
2. Route PWM path next and keep it direct.
3. Route tach path last and avoid running parallel with 12V for long distance.
4. Press B to repour zones and confirm clean returns.

## Placement done criteria
1. Fan connector and power decoupling are physically coupled.
2. PWM and tach subcircuits are separated enough to reduce coupling.
3. 12V fan path is wide, short, and easy to inspect.

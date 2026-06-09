# Power3V3 PCB Layout Guide

Target subsheet: Power3V3

## Footprints in this cluster
- U2
- C33_IN_BULK
- C33_IN_BYP
- C33_OUT_BULK
- C33_OUT_BYP

## Select this subsheet after schematic update

### Fastest method: right-click → Select → All Footprints in Same Hierarchical Sheet
1. Click any known Power3V3 footprint — e.g. **U2** — on the canvas.
2. Right-click → **Select** → **All Footprints in Same Hierarchical Sheet**.
3. Confirm U2 plus all input/output capacitors are selected.
4. Press **M** to move, **R** to rotate so VIN side faces input path, left-click to place.
5. Press **Ctrl+S** to save.

### Fallback: select the RefDes list above as one group.

Key shortcuts: **M** move · **R** rotate · **E** properties · **Ctrl+S** save · **Ctrl+Z** undo.

## Placement intent
1. Place U2 near VIN_PROTECTED distribution but separated from noisy fan-current zone.
2. Place C33_IN_BULK and C33_IN_BYP tight to U2 VIN/GND.
3. Place C33_OUT_BULK and C33_OUT_BYP tight to U2 VOUT/GND.
4. Reserve clean 3V3 fan-out corridor toward ESP32, display, and programming header.

## Routing priorities
1. Keep VIN input loop short and wide enough for regulator current.
2. Keep 3V3 output loop compact before branching to loads.
3. Route main 3V3 trunk first, then branch to subsystems.
4. Keep digital clock/control traces away from regulator input loop where possible.

Beginner routing steps:
1. Press X and route VIN_PROTECTED to U2.
2. Connect input capacitors directly to VIN/GND near U2 pins.
3. Route U2 output to output capacitors first, then extend 3V3 trunk.
4. Refill zones with B and verify no thin bottleneck on 3V3 trunk.

## Placement done criteria
1. 3V3 source and decoupling form a compact power island.
2. 3V3 trunk leaves island cleanly toward digital clusters.
3. Return current path to ground plane is direct and low impedance.

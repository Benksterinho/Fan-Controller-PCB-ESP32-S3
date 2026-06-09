# ExternalProgrammingHeader PCB Layout Guide

Target subsheet: ExternalProgrammingHeader

## Footprints in this cluster
- J_PROG

## Select this subsheet after schematic update

### Fastest method: right-click → Select → All Footprints in Same Hierarchical Sheet
1. Click **J_PROG** on the canvas.
2. Right-click → **Select** → **All Footprints in Same Hierarchical Sheet**.
3. Press **M** to move to a board edge, **R** until pin-1 orientation is readable from the silkscreen, left-click to place.
4. Press **Ctrl+S** to save.

### Fallback: select J_PROG by RefDes.

Key shortcuts: **M** move · **R** rotate · **E** properties · **Ctrl+S** save · **Ctrl+Z** undo.
4. Press E to verify reference and orientation before routing.

## Placement intent
1. Place J_PROG on a board edge with easy pogo/cable access.
2. Keep pin-1 orientation obvious on silkscreen.
3. Keep route corridor open toward ESP32 control nets and UART pins.

## Routing priorities
1. Route EN_RESET and GPIO0_BOOT cleanly and short to ESP32 support network.
2. Route UART_TX/UART_RX as a neat pair to avoid crossovers.
3. Keep GND reference near header and avoid long looping return.

Beginner routing steps:
1. Route GND and 3V3 header pins first.
2. Route EN_RESET and GPIO0_BOOT next while space is open.
3. Route UART pair together and avoid unnecessary zig-zag.
4. Save with Ctrl+S after this cluster is complete.

## Placement done criteria
1. Header orientation is obvious and user-proof.
2. Bring-up signals are directly routable without unnecessary vias.
3. Silkscreen labels are readable with enclosure installed.

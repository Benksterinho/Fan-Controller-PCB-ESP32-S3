# InputProtection PCB Layout Guide

Target subsheet: InputProtection

## Footprints in this cluster
- J_PWR
- F1
- D1
- D_TVS_IN
- C_IN_BYP
- C_IN_BULK

## Select this subsheet after schematic update

### Fastest method: right-click → Select → All Footprints in Same Hierarchical Sheet
1. Click any known InputProtection footprint — e.g. **D1** or **F1** — on the canvas.
2. Right-click → **Select** → **All Footprints in Same Hierarchical Sheet**.
3. Status bar at the bottom should show multiple footprints selected (J_PWR, F1, D1, TVS, caps).
4. Press **M** to move the group, left-click to place.
5. Press **Ctrl+S** to save.

### Fallback: select RefDes list above as a group.

Key shortcuts: **M** move · **R** rotate · **E** properties · **Ctrl+S** save · **Ctrl+Z** undo.

## Placement intent
1. Put J_PWR on a board edge with screwdriver access.
2. Place D_TVS_IN immediately behind connector entry to clamp surges early.
3. Keep F1 and D1 in short series path from VIN_RAW to VIN_PROTECTED.
4. Place C_IN_BYP near connector/protection input side.
5. Place C_IN_BULK near VIN_PROTECTED handoff toward regulator area.

## Routing priorities
1. Route input current path first: J_PWR -> F1 -> D1 -> VIN_PROTECTED.
2. Use short, wide copper for high-current entry path and return.
3. Keep TVS path short and direct to ground reference.
4. Avoid routing sensitive digital traces through input-protection zone.

Beginner routing steps:
1. Press X to start routing from J_PWR VIN pad.
2. Click at each corner point to shape the path.
3. Double-click to finish the track.
4. Press B after finishing this area to refill copper zones.

## Placement done criteria
1. Protection chain is physically linear and readable.
2. VIN_RAW copper is short and constrained to entry area.
3. VIN_PROTECTED exits cleanly toward both power regulators.

## Silkscreen visibility recommendation (space-saving)
1. Keep visible on F.SilkS: J_PWR1, F1, D1, D_TVS_IN1, C_IN_BULK1.
2. Hide on F.SilkS when crowded: C_IN_BYP1 (keep it visible on F.Fab).
3. Add short functional text near the block: VIN IN, VIN PROT, GND.

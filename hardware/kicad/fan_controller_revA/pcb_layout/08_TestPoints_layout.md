# TestPoints PCB Layout Guide

Target subsheet: TestPoints

## Footprints in this cluster
- TP1 VIN_PROTECTED
- TP2 12V
- TP3 3V3
- TP4 GND
- TP5 FAN_PWM
- TP6 FAN_TACH
- TP7 UART_TX
- TP8 UART_RX
- TP9 EN_RESET
- TP10 GPIO0_BOOT

## Select this subsheet after schematic update

### Fastest method: right-click → Select → All Footprints in Same Hierarchical Sheet
1. Click any test point footprint — e.g. **TP1** — on the canvas.
2. Right-click → **Select** → **All Footprints in Same Hierarchical Sheet**.
3. Confirm all TP1–TP10 footprints are highlighted.
4. Press **M** to move the group near board edges for probe access, left-click to place.
5. Press **Ctrl+S** to save.

### Fallback: select TP1..TP10 by RefDes range.

Key shortcuts: **M** move · **R** rotate · **E** properties · **Ctrl+S** save · **Ctrl+Z** undo.
4. Rotate with R to keep labels readable from one board orientation.

## Placement intent
1. Put TP1..TP4 near board edge with probe clearance.
2. Keep TP4 GND in a very accessible location for scope clips.
3. Group related digital test points by function (fan, UART, boot/reset).
4. Avoid putting test pads under connectors or near mounting hardware.

## Routing priorities
1. Keep test-point stubs short; they are taps, not routing hubs.
2. Place digital test points close enough for probing but away from noisy fan-power area.
3. Ensure silkscreen labels remain readable after assembly.

Beginner routing steps:
1. Route functional net first, then add short branch to the test point.
2. Avoid placing TP branch in the middle of a critical high-current path.
3. Run DRC after test-point routing to catch accidental shorts.

## Placement done criteria
1. All ten test points are reachable by probe tips.
2. Labels match net function and are visible in final assembly.
3. Probing one point does not mechanically block adjacent critical pads.

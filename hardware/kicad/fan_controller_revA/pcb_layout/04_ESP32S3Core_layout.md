# ESP32S3Core PCB Layout Guide

Target subsheet: ESP32S3Core

## Footprints in this cluster
- U3
- C_BULK
- C_DEC
- R_EN
- R_BOOT
- R_JTAG
- C_EN
- SW_RESET
- SW_BOOT

## Select this subsheet after schematic update

### Fastest method: right-click → Select → All Footprints in Same Hierarchical Sheet
1. Click any known ESP32S3Core footprint — e.g. **U3** — on the canvas.
2. Right-click → **Select** → **All Footprints in Same Hierarchical Sheet**.
3. Confirm U3 and all support capacitors, resistors, and switches are highlighted.
4. Press **M** to move, **R** to rotate so antenna side faces board edge, left-click to place.
5. Press **Ctrl+S** to save.

### Fallback: select RefDes list above, then move as one cluster.

Key shortcuts: **M** move · **R** rotate · **E** properties · **Ctrl+S** save · **Ctrl+Z** undo.

## Placement intent
1. Place U3 so antenna edge faces board edge with no copper under keepout.
2. Place C_DEC closest to the U3 3V3 pin entry, with C_BULK immediately behind it on the same 3V3 branch.
3. Keep EN and BOOT support parts (R_EN, C_EN, SW_RESET, R_BOOT, SW_BOOT) near their target pins.
4. Keep strap support R_JTAG local to U3 IO3 with no extra branch stubs.
5. Place reset and boot buttons at an accessible board-edge area.
6. Reserve a solid local ground region around the module body so the side GND pads and bottom GND pad array connect into the same low-impedance return.

## Detailed placement order
1. Lock U3 orientation first: antenna to the board edge, pin 1 corner easy to inspect, enough room on the non-antenna sides for decoupling and strap parts.
2. Place C_DEC first. It should be the closest 3V3 bypass part to the module supply pin, with the shortest possible loop from 3V3 pin to capacitor to ground.
3. Place C_BULK next to C_DEC, still very close to U3, but let C_DEC be the innermost part.
4. Place R_EN and C_EN as a tight pair near EN. Keep the EN node compact so it is not a long noise-sensitive trace.
5. Place R_BOOT near GPIO0. Put SW_BOOT near the board edge, but do not force the GPIO0 trace to wander around the button footprint.
6. Place R_JTAG near IO3 and keep that net local. Do not use this area as a convenient routing lane for unrelated signals.
7. Place SW_RESET near the same accessible edge area as SW_BOOT so bring-up and recovery are straightforward.

## Bottom GND pad handling under U3
1. U3 includes a bottom GND pad array on pad 41. Treat that area as part of the main RF and return structure, not as optional mechanical copper.
2. Do not route signal traces through the pad-41 area on the top layer.
3. Tie the pad-41 array into a solid top-layer ground region that also joins the module side GND pads. Avoid thin neck-down traces between ground pads.
4. Give C_DEC and C_BULK a very short ground return into this local ground region so their current loop closes at the module, not through a longer plane detour.
5. Add several nearby GND stitching vias around the pad array to connect top-layer ground to the bottom-layer ground plane. Place them just outside the pad field or in open gaps allowed by the footprint and assembly process.
6. If your manufacturer does not support filled/capped via-in-pad, do not drop open vias directly in the exposed pad paste area. Use adjacent stitching vias instead.
7. Keep solder-mask, paste, and copper settings from the library footprint unless you have a datasheet-backed reason to change them.
8. Do not use thermal relief between the bottom GND pad region and the local top-layer ground copper unless assembly or rework constraints force it. Lowest practical impedance matters more here.
9. Keep the bottom GND pad region out of the antenna keepout. The pad field should connect strongly to ground, but the antenna zone must stay clear of copper.

## Antenna and keepout details
1. Keep all copper, vias, pours, and test pads out of the antenna keepout area at the antenna end of U3.
2. Do not place buttons, tall headers, shielding cans, or cable metal near the antenna end.
3. If the board edge is directly under the antenna end, prefer a clean board edge over decorative cut-ins or dense silkscreen in that area.
4. Do not route the 3V3 trunk, fan-current returns, or dense SPI bundles across the antenna keepout boundary.

## Routing priorities
1. Route 3V3 and GND to U3 first with the shortest practical paths and with the decoupling loop completed before branching 3V3 elsewhere.
2. Finish the local ground strategy around U3 early: side GND pads, bottom pad array, and nearby stitching vias.
3. Route critical control nets EN_RESET and GPIO0_BOOT cleanly to the programming-header area.
4. Route the SPI group as a tidy bundle toward the display header cluster, with similar path length and minimal unnecessary layer changes.
5. Keep FAN_TACH away from PWM and switching-noise corridors.
6. Keep strap pins and reserved pins free from accidental coupling or opportunistic reuse.

Beginner routing steps:
1. Start with X from the U3 3V3 pin to C_DEC, then from that same local node to C_BULK.
2. Connect C_DEC and C_BULK grounds directly into the local U3 ground region before routing other signals.
3. Pour or route the U3 local top-layer ground so pad 1, pad 40, and pad-41 array all share a short return.
4. Drop nearby GND stitching vias once the local ground shape is clear.
5. Route EN_RESET and GPIO0_BOOT before less critical IO.
6. Route SPI lines as a grouped bundle in the same general corridor.
7. Add vias with V only when needed; fewer vias are easier to debug, except for deliberate GND stitching around U3.
8. Press B after major routing steps so you can verify the antenna keepout and local ground continuity are still correct.

## Quick review checklist for refs and placement
1. The guide uses the new uppercase shortened names: C_BULK, C_DEC, R_EN, R_BOOT, R_JTAG, C_EN, SW_RESET, SW_BOOT.
2. C_DEC is closer to the U3 power entry than C_BULK.
3. R_EN plus C_EN are tight to EN, and R_BOOT is tight to GPIO0.
4. R_JTAG is local to IO3 with no functional load attached.
5. The U3 bottom GND pad array is tied into a solid local ground region with nearby stitching vias.
6. No copper or vias violate the antenna keepout.

## Placement done criteria
1. RF keepout is preserved and not overlapped by copper or metal.
2. Decoupling caps are tightly coupled to U3 supply pins and return directly into the local module ground.
3. The bottom GND pad array and side GND pads form one clean, low-impedance ground structure.
4. Boot/reset and programming control nets are short and unambiguous.

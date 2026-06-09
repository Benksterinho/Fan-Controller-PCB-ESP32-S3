# DisplayHeader_ST7789 PCB Layout Guide

Target subsheet: DisplayHeader_ST7789

## Footprints in this cluster
- J_DISP
- R_SCK
- R_MOSI
- R_CS
- R_DC
- R_RST
- C_DISP_VCC
- Q_BL
- R_BL_BASE
- R_BL_PD

## Select this subsheet after schematic update

### Fastest method: right-click → Select → All Footprints in Same Hierarchical Sheet
1. Click any known DisplayHeader_ST7789 footprint — e.g. **J_DISP** — on the canvas.
2. Right-click → **Select** → **All Footprints in Same Hierarchical Sheet**.
3. Confirm J_DISP and any associated resistors/caps are highlighted.
4. Press **M** to move, **R** to rotate so header pins align with display ribbon access, left-click to place.
5. Press **Ctrl+S** to save.

### Fallback: select RefDes list above and move as a cluster.

Beginner click path:
1. Filter Footprints by Sheetname equals DisplayHeader_ST7789.
2. Verify J_DISP and resistor bank parts are selected.
3. Press M to move to intended board edge.
4. Press R until connector orientation matches cable exit direction.

## Placement intent
1. Place J_DISP on board edge with cable exit in mind.
2. Place series resistors as a short row between ESP32 route-in and connector pins.
3. Place C_DISP_VCC close to J_DISP power pins.
4. Place Q_BL + R_BL_BASE + R_BL_PD together near BLK route.

## Routing priorities
1. Route SPI/control traces from ESP32 area to resistor row, then to header.
2. Keep SCK/MOSI/CS/DC/RST traces short and clean.
3. Leave display MISO/SDO unconnected on Rev A.
4. Route BLK sink path with short return to ground reference.

Beginner routing steps:
1. Route from ESP32-side nets into resistor row first.
2. From resistor row, route short final links to J_DISP pins.
3. Keep crossings low; if crossing is needed, add one via and return quickly.
4. Refill zones with B and ensure BLK return has a clear ground path.

## Placement done criteria
1. Connector and resistor bank are aligned for clean breakout.
2. Local VCC bypass sits at the connector power entry.
3. SPI/control traces are not detouring through noisy power zones.

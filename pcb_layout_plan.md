## PCB Layout Master Plan (Rev A, KiCad 10.x)

This is the concise authoritative plan for Rev A PCB layout.
It is aligned to current schematic decisions and PCBWay assembly goals.
For a first-time-user walkthrough, use the beginner guide:
- [docs/pcb_layout_beginner_kicad10.md](docs/pcb_layout_beginner_kicad10.md)

### 0. Scope and Gate

Scope:
- KiCad 10.x workflow only
- Rev A hardware as locked by current schematics
- PCBWay PCB + assembly manufacturing target

Start gate:
- ERC has 0 blocking errors
- Schematic lock tag exists
- Footprints assigned for all assembled parts

KiCad workflow gate:
- when updating PCB from schematic, review ECO change summary before applying
- apply updates only when footprint/net adds-removes are intentional

### 1. Hard Constraints
- Board outline target stays below 99x99 mm for PCBWay pricing tier control.
- ESP32-S3 antenna keep-out is mandatory:
  - no copper on any layer under antenna region
  - no traces or vias in keep-out
  - keep-out extends at least 5 mm beyond the module antenna element in all directions
  - keep-out faces board edge/air space
  - reference: ESP32-S3-WROOM-1 datasheet module dimensions figure
- Use a single unified GND plane (no split domains).
- Use a 2-layer stack for Rev A (F.Cu + B.Cu), with bottom copper prioritized as continuous GND.
  - After routing on the top layer, fill unused F.Cu areas with a GND copper pour to improve return paths and thermal dissipation.
- Through-via default for Rev A is 0.60 mm via / 0.30 mm drill; use 0.50 mm / 0.25 mm only in dense areas after DRC and PCBWay capability checks.
- Keep thermal relief enabled on zone connections (KiCad default) to ensure pads connected to GND planes remain solderable.
- Use current Rev A architecture assumptions from [docs/architecture.md](docs/architecture.md) (TBD if not yet created):
  - external programming header (no onboard USB-UART routing work)
  - K7812/K7803 module-based power stages

### 1b. PCBWay Manufacturing Constraints (DFM)

Configure KiCad design rules to respect PCBWay standard-process minimums.
These values must be entered in Board Setup -> Design Rules -> Minimum Values before routing begins.

| Parameter                | PCBWay Standard Min | KiCad Setting Location              |
|--------------------------|---------------------|--------------------------------------|
| Min trace width          | 0.127 mm (5 mil)   | Design Rules -> Minimum Values       |
| Min trace spacing        | 0.127 mm (5 mil)   | Design Rules -> Minimum Values       |
| Min drill hole           | 0.30 mm             | Design Rules -> Minimum Values       |
| Min annular ring         | 0.13 mm             | Design Rules -> Minimum Values       |
| Min solder mask bridge   | 0.10 mm             | Design Rules -> Minimum Values       |
| Copper to board edge     | 0.30 mm             | Design Rules -> Minimum Values       |
| Component body to edge   | >= 1.00 mm          | Placement discipline (not enforced by DRC) |

Implementation gate:
- enter these values before the first route is placed
- re-run DRC after entry to catch any existing violations

### 2. Placement Order (Lock As You Go)
- Place and lock board-edge connectors:
  - power input connector
  - external programming header (1x6)
  - fan connector
  - display header
- Component body to edge rule: keep all component bodies at least 1 mm from board edges to allow for panelization and V-scoring tooling.
- Place and lock ESP32 module with antenna keep-out validated (>= 5 mm clear zone beyond antenna element, no copper on any layer).
- Place K7812/K7803 modules and their in/out capacitors within 10 mm of module pins.
  - HF bypass capacitors go closest to the module power pin.
  - Bulk capacitors placed next, still within the 10 mm envelope.
- Place fan power/driver block near fan connector.
- Place display interface block and backlight transistor path (Q_BL, R_BL_BASE, R_BL_PD) near display header.
- Place test points in accessible probe-friendly locations.
- Place at least 2 fiducial marks (1 mm circle, bare copper, no solder mask) on opposite corners of the board for PCBWay pick-and-place machine alignment.

### 3. Routing Order
- Route power nets first:
  - VIN_PROTECTED
  - 12V rail
  - 3V3 rail
  - return paths to GND plane
- Route boot/programming control nets:
  - EN_RESET
  - GPIO0_BOOT
  - UART_TX, UART_RX
- Route functional signals:
  - fan (FAN_PWM, FAN_TACH)
  - display SPI/control (SPI_SCK, SPI_MOSI, DISP_CS, DISP_DC, DISP_RST, DISP_BL)
- Add stitching vias around module grounds and decoupling locations.
- Minimize unnecessary layer changes, especially on power and high-current return paths.

Rev A starter trace-width defaults (external 1 oz copper assumption):
- VIN_PROTECTED trunk: >= 1.00 mm
- 12V trunk: >= 0.80 mm
- 3V3 trunk: >= 0.60 mm
- short local power branches: >= 0.30 mm
- low-current logic/control: 0.20-0.25 mm

Calculator gate:
- verify trunk power widths with SaturnPCB or an IPC-2152-style calculator
- keep implemented widths equal or wider than calculated minimums

Implementation gate (KiCad):
- define net classes in Board Setup -> Design Rules -> Net Classes
- assign VIN_PROTECTED, 12V, and 3V3 to dedicated power classes so widths are enforced during routing

Net-to-class baseline:
- VIN_PROTECTED -> PWR_VIN
- 12V -> PWR_12V
- 3V3 -> PWR_3V3
- short rail branches/decoupling feeds -> PWR_BRANCH
- control/data (EN_RESET, GPIO0_BOOT, UART_*, FAN_*, SPI_*, DISP_*) -> SIG

Routing hygiene gate:
- refill zones after major placement/routing changes and immediately before DRC
- for power trunk layer transitions, avoid single-via bottlenecks

### 4. Silkscreen

After routing and before DRC:
- Label all connectors: J_PWR, J_FAN, J_DISP, J_PROG
- Add pin-1 indicators on all headers
- Mark power input polarity (+/-) at J_PWR
- Label UART TX/RX on programming header (prevents the #1 beginner wiring mistake)
- Add board name and revision text (e.g., "Fan Controller Rev A")
- Ensure no silkscreen text overlaps pads, vias, or fiducial marks
- Keep silkscreen text size readable (>= 0.8 mm height, >= 0.15 mm line width)

### 5. Review Gates
- Mechanical and usability review:
  - connector cable access is practical
  - programming header is service-accessible
  - buttons/test points are physically reachable
  - component bodies are at least 1 mm from board edges
  - fiducial marks are present on opposite corners
- Electrical review:
  - no long high-current loops
  - bulk/decoupling caps remain close to intended pins
  - no trace/via violations in antenna keep-out
  - power trunk widths satisfy plan defaults or calculator-derived minimums
  - thermal relief is enabled on GND zone connections
- Silkscreen review:
  - all connectors labeled
  - pin-1 and polarity indicators present
  - no text over pads or vias
  - board name and revision visible
- DRC review in KiCad 10.x:
  - run cleanup pass (remove abandoned segments/duplicate edits), refill zones, then DRC
  - triage DRC in order: unconnected items, then clearance, then width/constraint class violations
  - no blocking DRC violations
- Bring-up readiness review:
  - first-power checks defined for VIN_PROTECTED, 12V, and 3V3
  - project includes a quick troubleshooting matrix for common rail/routing failures

### 6. Manufacturing Output (PCBWay)

Generate and verify:
- Gerbers and drills (with explicit export preset and drill map)
  - include copper, solder mask, silkscreen, paste, edge cuts
  - verify F.Paste layer shows stencil openings only on SMD pads
  - through-hole pads should not have paste apertures
- BOM with sourceable MPN/LCSC references
- CPL/position file
- Assembly notes aligned with [docs/pcbway-assembly.md](docs/pcbway-assembly.md) (TBD if not yet created)
- Orientation verification artifacts (pin-1/polarity and 3D check)
- Fiducial marks present and included in Gerber output

Contest-ready output package (recommended):
- hero image, annotated PCB image, and architecture diagram
- functional demo artifact (short video or GIF)
- source archive/repo link plus license/attribution notes

Final gate before fabrication upload:
- layout review complete
- files exported from final committed PCB state
- package checked against PCBWay assembly requirements
- fiducials verified in Gerber viewer

### 7. Explicit Non-Goals For Rev A

Do not reintroduce in this phase:
- Onboard USB-UART routing/features
- Rev B optionals (unless explicitly approved in a separate change)

### 8. Referenced Documents

- [docs/architecture.md](docs/architecture.md) (TBD if not yet created)
- [docs/pcbway-assembly.md](docs/pcbway-assembly.md) (TBD if not yet created)
- [docs/bom-revA-status.md](docs/bom-revA-status.md) (TBD if not yet created)
- [docs/sourcing-checklist.md](docs/sourcing-checklist.md) (TBD if not yet created)
- ESP32-S3-WROOM-1 datasheet (module dimensions and antenna keep-out figure)

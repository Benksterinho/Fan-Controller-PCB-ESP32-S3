## PCB Layout Cookbook (KiCad 10.x, Rev A)

This is a recipe-style, beginner-safe PCB layout guide.
Follow every recipe in order.
Do not skip "Stop and check" blocks.

Master plan reference:
- [pcb_layout_plan.md](../pcb_layout_plan.md)

---

### Recipe 0: Prep The Workspace

#### Ingredients
- KiCad 10.x installed and open
- Schematic lock state with ERC = 0 blocking errors
- Board-size target: below 99x99 mm

#### Steps
- Open the project in KiCad 10.x.
- Confirm you are not using legacy project files from older KiCad versions.
- Confirm this Rev A rule: no onboard USB-UART routing tasks.

#### Stop and check
- You can open schematic and PCB editor without migration prompts.
- You can proceed only if ERC is clean of blocking errors.

---

### Recipe 1: Generate PCB From Schematic

#### Steps
- Open PCB Editor.
- Run KiCad 10.x "Update PCB from Schematic" flow.
- Save PCB immediately as checkpoint 01.

#### ECO safety gate (do this every update)
- In the update dialog, review what will be added, removed, or changed.
- If footprints are being removed unexpectedly, stop and verify schematic intent first.
- Apply the update only after the changes list looks intentional.
- Save a new checkpoint right after update (for example: checkpoint 02).

#### Stop and check
- All footprints are present.
- No missing-footprint warnings are shown.
- You can explain every footprint/net change from the update summary.

---

### Recipe 2: Draw Board Outline And Fiducials

Do this before real placement so you do not spread parts beyond your target size.

#### Steps
- Draw Edge.Cuts board outline.
- Keep max dimensions below 99x99 mm.
- Add mounting holes now if needed.
- Add at least 2 fiducial marks on opposite corners of the board:
  - Use a 1 mm diameter fiducial with exposed bare copper (solder mask opening, no paste).
  - Purpose: pick-and-place machine alignment for PCBWay assembly (PCBA).
  - Place fiducials at least 5 mm from board edges.

#### Beginner click path: board outline
1. Open PCB Editor.
2. In the layer selector, choose Edge.Cuts.
3. Use Place -> Draw Rectangle (or draw lines/arcs if shape is custom).
4. Click start and end points to create the outline.
5. If using lines/arcs, ensure every segment endpoint touches the next endpoint.
6. Use Inspect -> Measure Tool and check width and height are both below 99 mm.
7. Save.

#### Beginner click path: fiducials
1. Press A in PCB Editor to add a footprint.
2. Search for fiducial footprints and choose a 1 mm copper fiducial variant.
3. Place one fiducial near top-left area and one near bottom-right area.
4. Keep at least 5 mm clearance from board edge and from tall components.
5. Open footprint properties and confirm it is copper-only marker style (no paste).

#### Stop and check
- Outline is closed.
- Dimensions are within target.
- Two fiducial marks are present on opposite corners.

---

### Recipe 3: Configure PCBWay Manufacturing Constraints (DFM)

This recipe prevents you from designing features the fab cannot produce.
Enter these values BEFORE placing or routing anything.

#### Steps
- Open PCB Editor.
- Go to File -> Board Setup...
- In the left panel, open Design Rules -> Minimum Values.
- Set units to mm in the Board Setup window before entering values.
- Enter PCBWay standard-process minimums:

| Parameter              | Value        |
|------------------------|--------------|
| Min track width        | 0.127 mm     |
| Min track spacing      | 0.127 mm     |
| Min drill hole         | 0.30 mm      |
| Min annular ring       | 0.13 mm      |
| Min solder mask bridge | 0.10 mm      |
| Copper to board edge   | 0.30 mm      |

- Apply and close.
- These are absolute minimums. Your actual routing should use wider values from the net-class setup (Recipe 11).

#### Beginner click path: entering values safely
1. In Board Setup, type each value exactly as shown in the table.
2. Press Enter after each field so KiCad applies the new value.
3. Click Apply before closing the dialog.
4. Re-open Board Setup once to confirm values were saved.

#### Placement rule (not enforced by DRC — you must check manually)
- Keep all component bodies at least 1 mm from board edges to allow for panelization and V-scoring tooling.

#### Beginner verification pass (2 minutes)
1. Run Inspect -> Design Rules Checker.
2. If violations appear, double-click each row to jump to the location.
3. Fix only real violations now, then run DRC again.

#### Stop and check
- Board Setup minimum values match the table above.
- Run DRC once to confirm no pre-existing violations.

---

### Recipe 4: Place And Lock Fixed Connectors

#### Steps
- Place power input connector.
- Place external programming header (1x6).
- Place fan connector.
- Place display header.
- Lock all four.

#### Placement rules
- Programming header must be cable-accessible with fingers/tools.
- Fan connector should match real cable exit direction.
- Display header must not be blocked by tall parts.
- Keep all component bodies at least 1 mm from board edges.

#### Stop and check
- You can imagine plugging each cable without collisions.
- No connector body is closer than 1 mm to the board edge.

---

### Recipe 5: Place ESP32 Module And Antenna Keep-Out

#### Steps
- Place ESP32-S3 near board edge for antenna clearance.
- Create antenna keep-out region in PCB editor:
  - The keep-out must extend at least 5 mm beyond the module's antenna element in all directions.
  - No copper on any layer (F.Cu and B.Cu) inside the keep-out.
  - No traces, no vias, no copper pour inside the keep-out.
  - The antenna side must face the board edge or open air — never toward other components.
  - Reference: ESP32-S3-WROOM-1 datasheet, module dimensions figure (Section 7.1).
- Lock module position.

#### Stop and check
- Keep-out is visible and active on both layers.
- Nothing intersects keep-out.
- Antenna faces board edge.

---

### Recipe 6: Place Power Modules + Passives (HF and Bulk)

Rev A uses module regulators, not discrete buck stages.

#### Steps
- Place K7812 (12V module) and K7803 (3.3V module).
- Place each module's input and output capacitors within 10 mm of module pins.
- Place HF bypass capacitors closest to the relevant power pin first.
- Place bulk capacitors next, still inside the 10 mm envelope.
- Keep module-cap loops short and direct.

#### Passive placement rules (apply everywhere)
- HF bypass capacitors go closest to the power pin they support.
- Bulk capacitors support local energy storage and stay near the same node.
- If a passive is farther than 10 mm from its intended module pin, move it before routing.
- Ground side of both HF and bulk capacitors should connect quickly to the GND plane.

#### Stop and check
- Every module in/out cap is within 10 mm.
- HF bypass is closer than bulk for each local power node.

---

### Recipe 7: Place Fan And Display Functional Blocks

#### Steps
- Fan block near fan connector:
  - fan driver transistor stage
  - fan protection passives
  - fan local HF and bulk caps
- Display block near display header:
  - SPI series resistors
  - backlight path (Q_BL, R_BL_BASE, R_BL_PD)
- Program/reset support block near programming header.

#### Stop and check
- Blocks are physically near the connectors they serve.
- No long detours are required for key signals.

---

### Recipe 8: Place Decoupling And Test Points

#### Steps
- Place decoupling capacitors close to IC/module power pins.
- Place test points where a probe can contact from top side.
- Keep test points away from connector overhang and tall-part shadows.

#### Passive placement rules
- Decoupling capacitor trace from pin should be short and direct.
- Decoupling GND return should hit plane quickly with minimal loop area.

#### Stop and check
- Probe access is realistic on the assembled board.

---

### Recipe 9: Build Ground Planes And Stitching

#### Steps
- Create one solid GND plane on the bottom layer (B.Cu) — this is the primary continuous ground reference.
- After top-layer routing is complete, add a GND copper pour on F.Cu as well:
  - This fills unused top-layer space with ground copper.
  - Improves ground return paths, reduces EMI, improves thermal dissipation.
- Do not split GND domains on either layer.
- Add stitching vias near:
  - ESP32 GND pins
  - decoupling capacitor GND pads
  - high-current return transitions
  - between top and bottom GND pours in open areas

#### Beginner click path: create B.Cu GND plane
1. Select B.Cu as active layer.
2. Use Place -> Add Filled Zone.
3. Set Net to GND.
4. Keep zone connection style as Thermal Relief.
5. Draw a polygon close to the whole board boundary (inside Edge.Cuts).
6. Double-click to close the zone.
7. Press B to refill zones.

#### What "Press B to refill zones" means (beginner)
- In KiCad, copper pours (zones) are not always redrawn instantly after edits.
- Pressing B tells KiCad to recalculate and redraw all filled zones on all copper layers.
- Think of it as "rebuild copper pours now" after you changed traces, vias, pads, or zone boundaries.

Example:
1. You route a new trace through an area that used to be open copper.
2. The zone still looks old for a moment (or shows stale gaps/islands).
3. Press B.
4. KiCad refills copper around the new geometry using your current clearance and net rules.

When to press B:
- Right after adding or editing a zone.
- After moving parts that affect copper clearance.
- After adding vias/tracks near a filled zone.
- Immediately before running DRC.

If B seems to do nothing:
1. Confirm the zone is assigned to a net (for example GND).
2. Confirm zone visibility is enabled for the active layer.
3. Check you are not inside a keep-out or excluded region.
4. Run DRC to catch rule conflicts that prevent expected fill.

#### Beginner click path: add F.Cu helper GND pour
1. Select F.Cu as active layer.
2. Place another filled zone with Net = GND.
3. Draw around available top-layer free area.
4. Press B to refill and check there are no unexpected isolated islands.

#### Solid copper plane setup details (2-layer workflow)
1. Treat B.Cu as the primary continuous GND plane for this board.
2. Keep B.Cu GND zone priority lower number (higher priority) than any local copper zones that should not cut the main return path.
3. In zone properties, keep Remove Islands enabled so tiny isolated copper fragments are removed.
4. Keep thermal relief enabled for most pads so hand-solder and rework remain practical.
5. If a high-current pad needs stronger copper tie, use a short explicit trace to the pad first, then let zone thermal connect.
6. Keep antenna keep-out rules active on both F.Cu and B.Cu so zones do not refill into that area.
7. Refill with B after every zone edit, then run DRC.

#### How to verify the plane is truly solid
1. Toggle to B.Cu only and inspect for long neck-downs or split regions under power paths.
2. Use Highlight Net on GND and confirm broad continuity across the board.
3. Check around connectors, regulators, and fan-current paths for uninterrupted return routes.
4. If you see bottlenecks in GND, move traces/components before routing more signals.

#### Beginner click path: stitching vias
1. Start routing a short GND segment with X.
2. Press V to drop a via connected to GND.
3. Place vias near decoupling caps and near power return transitions.
4. Refill zones with B and verify top and bottom GND copper connect through vias.

#### Thermal relief
- Keep thermal relief enabled on zone connections (this is the KiCad default).
- Thermal relief uses small spoke connections between pads and copper pours.
- This ensures GND-connected pads are still solderable (without thermal relief, heat sinks into the ground plane and makes soldering very difficult).
- Do not disable thermal relief unless you have a specific engineering reason.

#### Stop and check
- GND continuity is clean on both layers.
- No accidental islands remain.
- Thermal relief is enabled (check zone properties).
- No copper is present in ESP32 antenna keep-out on either layer.

#### Zone refill cadence (KiCad workflow)
- Refill zones after each major placement step.
- Refill zones after each major routing pass.
- Refill zones immediately before every DRC run.
- If a route unexpectedly fails, refill zones first, then retry.

---

### Recipe 10: Set Layer Stack And Via Rules

#### Fabrication assumptions gate

Confirm these before routing any trunk power net:
- 2-layer board (F.Cu + B.Cu).
- External copper is 1 oz.
- Standard through-via process (no blind/buried/microvias).

If any item changes, recalculate power widths before continuing.

#### Steps
- Use a 2-layer board for Rev A: F.Cu (top) and B.Cu (bottom).
- Keep bottom copper as the primary continuous GND plane.
- Route most short/local signals on top first, then drop to bottom only when needed.
- Use standard through vias only for Rev A (no blind/buried/microvias).
- Start with default via size 0.60 mm and drill 0.30 mm.
- If a dense area requires it, use 0.50 mm / 0.25 mm only after confirming DRC is clean and the PCBWay capability is acceptable.

#### Beginner click path: force 2-layer setup
1. Open File -> Board Setup.
2. Open Board Stackup.
3. Set copper layers to 2.
4. Ensure only F.Cu and B.Cu are active copper layers.
5. Apply and close.

#### Beginner click path: via defaults
1. Open Board Setup -> Design Rules -> Net Classes.
2. In the default class, set preferred via size to 0.60 mm and drill to 0.30 mm.
3. If needed, add a secondary via preset at 0.50/0.25 for dense escape areas.
4. Apply and close.

#### Via guardrails
- Never place vias inside the ESP32 antenna keep-out.
- Avoid unnecessary layer hopping on power and high-current return paths.
- Place stitching vias close to decoupling GND pads and power-return transitions.

#### Via handling details (what to do and why)
- Use one default via style across most of the board: 0.60/0.30 mm.
- Use 0.50/0.25 mm only in dense escape regions; do not mix many via sizes without need.
- For trunk power transitions between layers:
  - 3V3 trunk: use at least 2 vias in parallel.
  - 12V and VIN_PROTECTED trunks: use at least 3 vias in parallel where space allows.
- Keep via clusters close to the transition point; do not run a long thin segment to a distant via.
- Never neck a wide power trace into a single-via bottleneck.
- For signal vias, minimize count on critical nets and keep nearby GND continuity.
- Add GND stitching vias near decoupling capacitors and at return-path changes.

#### Beginner click path: place parallel vias on a power transition
1. Route the power trace on F.Cu until the layer-change point.
2. Press V once to place the first via.
3. Continue routing a short segment on B.Cu.
4. Exit route, start again on the same net, and place additional vias next to the first.
5. Tie all vias into the same wide copper region on both layers.
6. Press B to refill zones and confirm vias connect into the GND/power copper as intended.

#### Stop and check
- Board is explicitly configured as 2-layer.
- Default vias are consistent across the board.
- No via appears in the antenna keep-out.
- Layer transitions on trunk power nets use parallel vias, not single-via bottlenecks.

---

### Recipe 11: Configure Net Classes In KiCad (Click Path)

#### Steps
- Open PCB Editor.
- Go to File -> Board Setup....
- In the left panel, open Design Rules -> Net Classes.
- Create classes and set track widths:
  - PWR_VIN width 1.00 mm
  - PWR_12V width 0.80 mm
  - PWR_3V3 width 0.60 mm
  - PWR_BRANCH width 0.30 mm
  - SIG width 0.20-0.25 mm
- Set clearances (starter values):
  - PWR_VIN, PWR_12V: >= 0.25 mm
  - PWR_3V3, PWR_BRANCH: >= 0.20 mm
  - SIG: >= 0.20 mm
  - Copper to board edge (global rule): >= 0.30 mm
- Set preferred via for these classes to the cookbook default (0.60/0.30 mm).
- Assign nets to classes (same dialog, net assignment section):
  - VIN_PROTECTED -> PWR_VIN
  - 12V -> PWR_12V
  - 3V3 -> PWR_3V3
  - remaining low-current nets -> SIG
- Apply and close Board Setup.
- Save project and commit this rule setup before large routing changes.

#### Beginner click path: assign nets without mistakes
1. In Net Classes panel, select one net class first (for example PWR_3V3).
2. In the unassigned-net list, click net 3V3 and assign it to PWR_3V3.
3. Repeat for VIN_PROTECTED and 12V using their power classes.
4. Assign all remaining control/data nets to SIG.
5. Click Apply, then close.

#### Beginner validation
1. Start a short route on VIN_PROTECTED with X and confirm width auto-loads to class value.
2. Start a short route on SPI_SCK and confirm narrower SIG width is auto-selected.
3. Undo the test traces if they were only for checking behavior.

#### Net to class mapping (single source of truth)

Use this table during routing review and DRC triage.

| Net or group | Net class |
|---|---|
| VIN_PROTECTED trunk and primary feed | PWR_VIN |
| 12V trunk to fan power path | PWR_12V |
| 3V3 trunk feeding digital sections | PWR_3V3 |
| short local rail branches and local decoupling feeds | PWR_BRANCH |
| control and data: EN_RESET, GPIO0_BOOT, UART_*, FAN_PWM, FAN_TACH, SPI_*, DISP_* | SIG |

#### Stop and check
- Starting a route on each net shows the expected width automatically.
- You are no longer manually changing width for every power trace.
- DRC run after net-class setup reports no new clearance errors.

---

### Recipe 12: Route In Safe Order

#### Router setup (KiCad 10.x)

Before routing the first critical net:
- Open router settings in PCB Editor.
- Use an interactive mode that avoids accidental disconnects and keeps clearances visible.
- Keep net-class-driven widths active (do not manually override unless necessary).
- Route one short test segment on a power net and one on a signal net to confirm expected behavior.

#### Steps
- Route power nets first: VIN_PROTECTED, 12V, 3V3, returns.
- Route programming/control: EN_RESET, GPIO0_BOOT, UART_TX, UART_RX.
- Route fan control: FAN_PWM, FAN_TACH.
- Route display SPI/control: SPI_*, DISP_*.
- Route remaining low-risk nets.

#### Power width defaults (Rev A starter values)

Assumption: external layers, 1 oz copper, conservative temperature rise target.
- VIN_PROTECTED main feed: >= 1.00 mm
- 12V main feed to fan connector: >= 0.80 mm
- 3V3 main feed trunk: >= 0.60 mm
- Short branch feeds to local loads/decoupling: >= 0.30 mm
- Low-current logic/control signals: 0.20-0.25 mm

#### Calculator check (SaturnPCB/IPC style)
- Treat the table above as minimums, not maximums.
- If board area allows, make power traces wider than minimum.
- If you use SaturnPCB (or an IPC-2152 calculator), verify at least the VIN_PROTECTED, 12V, and 3V3 trunks and keep the chosen widths equal or wider than calculator output.

#### Guardrails
- Keep high-current traces short and appropriately wide.
- Keep sensitive control lines away from noisy high-current loops.
- Never route into antenna keep-out.
- For any power-net layer change, use at least 2 vias in parallel on the transition.

#### Net-focus workflow (prevents routing mistakes)
- Highlight the net before routing dense areas.
- Route one net to completion before jumping to unrelated nets.
- Use local ratsnest visibility to reduce visual noise and prevent wrong endpoints.
- After each completed power trunk, do a quick continuity sanity check in the PCB editor.

#### Via transition cheat sheet (beginner-safe)
- 3V3 trunk layer transition: use at least 2 vias in parallel.
- 12V and VIN_PROTECTED trunk transitions: use at least 3 vias in parallel where space allows.
- Never neck a wide power trace into a single-via bottleneck.
- Keep via groups close to the source/load side of the transition, not far down the route.

#### Return-path sanity pass
- For each trunk (VIN_PROTECTED, 12V, 3V3), visually confirm there is a nearby, continuous GND return path.
- Avoid narrow GND neck-down regions under high-current power segments.
- If the route crosses a split/slot/void in GND, reroute the signal or repair the return path first.

---

### Recipe 13: Add Silkscreen Labels

This recipe runs after routing and before DRC.
Good silkscreen is critical for usability, debugging, and contest scoring.

#### Steps
- Label all connectors with their reference designators:
  - J_PWR (power input)
  - J_FAN (fan connector)
  - J_DISP (display header)
  - J_PROG (programming header)
- Add pin-1 indicators on all headers (dot or arrow at pin 1).
- Mark power input polarity at J_PWR: "+" and "-" or "VIN" and "GND".
- Label UART signals on programming header:
  - Mark TX and RX next to the corresponding header pins.
  - This prevents the #1 beginner wiring mistake.
- Add board name and revision:
  - Example: "Fan Controller Rev A"
  - Place on a visible area of the board (not under components).
- Optionally add your name, project URL, or license text.

#### Silkscreen quality rules
- Minimum text height: 0.8 mm.
- Minimum text line width: 0.15 mm.
- No silkscreen text may overlap pads, vias, or fiducial marks.
- No silkscreen text may enter the antenna keep-out region.
- Keep text orientation consistent (prefer horizontal, readable from one direction).

#### Silkscreen-friendly reference policy (recommended)
- Keep KiCad references unique for EDA/BOM integrity, but avoid printing every tiny part on silkscreen.
- Show references on silkscreen for: connectors, regulators/modules, polarized parts (diodes/electrolytics), transistors, ICs, test points.
- Hide references on silkscreen for dense 0402/0603 passives when space is tight.
- Keep hidden references visible on F.Fab so assembly/debug still has full part identity.
- Add short functional text near blocks (for example: VIN IN, VIN PROT, FAN OUT, PROG) instead of long per-part names everywhere.

#### Beginner click path: hide tiny passive references safely
1. Click a tiny passive footprint in PCB Editor.
2. Press E to open Footprint Properties.
3. In Text items, find Reference.
4. Disable visibility on F.SilkS (or mark the reference as hidden).
5. Leave Value/Reference available on F.Fab for assembly readability.
6. Repeat for dense 0402/0603 passives in crowded zones.

#### Stop and check
- Every connector is labeled.
- Pin-1 is marked on all headers.
- Power polarity is marked at J_PWR.
- TX/RX labeled on programming header.
- Board name and revision are visible.
- No text overlaps pads or vias.
- Tiny passive references are hidden only where crowding exists; critical parts remain labeled.

---

### Recipe 14: Avoid These Common Mistakes
- Wrong UART cable assumption:
  - Adapter RX -> Board UART_TX
  - Adapter TX -> Board UART_RX
- Using boot strapping pins as general-purpose loaded lines.
- Treating backlight as direct GPIO drive instead of transistor path.
- Moving connectors late and causing route churn.
- Forgetting board-size target while spreading components.
- Letting HF or bulk caps drift far from their intended pins.
- Placing components closer than 1 mm to board edges.
- Forgetting fiducial marks (required for PCBA).
- Leaving silkscreen text over pads or vias.

---

### Recipe 15: Pre-DRC Checklist

Do this before running DRC:
- Connectors are accessible.
- ESP32 antenna keep-out is clean (>= 5 mm, both layers, no copper).
- Module passives meet placement rules (HF closest, bulk nearby, all inside 10 mm where required).
- Test points are reachable.
- Ground plane is unified (both layers).
- Power trunk widths meet or exceed cookbook defaults (or your calculator result).
- Thermal relief is enabled on zone connections.
- Fiducial marks are present on opposite corners.
- Silkscreen labels are complete and not overlapping pads.
- Component bodies are at least 1 mm from board edges.

If any check fails, fix placement first.

---

### Recipe 16: DRC + Final Visual Pass

#### Steps
- Run cleanup pass before DRC:
  - remove obvious routing stubs or abandoned segments
  - remove accidental duplicate vias/segments in dense edits
  - refill zones
- Run DRC in KiCad 10.x in this order:
  - unconnected items first
  - then clearance violations
  - then width/constraint class violations
- Fix all blocking issues.
- Perform final visual checks:
  - no suspicious long power loops
  - no undersized high-current traces
  - no keep-out violations
- Run a quick DFM sanity check:
  - no silkscreen text crossing pads
  - no tiny solder-mask slivers between dense pads
  - annular rings look robust on through-hole parts
  - copper features respect board-edge clearance target (0.30 mm copper, 1 mm component bodies)

#### Stop and check
- Layout is clean enough to produce manufacturing outputs.

---

### Recipe 17: Build PCBWay Assembly Package

#### Steps
- Export Gerbers using this starter preset:
  - include copper (F.Cu, B.Cu), solder mask (F.Mask, B.Mask), silkscreen (F.Silkscreen, B.Silkscreen), paste (F.Paste), edge cuts (Edge.Cuts)
  - use mm units and standard Gerber format supported by PCBWay
  - keep aperture handling default unless fab asks otherwise
- Export drill files:
  - Excellon drill output
  - include plated and non-plated holes correctly
  - include drill map for quick human review
- Export BOM with sourceable part numbers.
- Export CPL/position file.
- Prepare assembly notes.
- Run orientation gate before upload:
  - verify pin-1/polarity for connectors, diodes, electrolytics, and modules
  - verify fan header silkscreen pin order matches project convention
  - do one full 3D viewer pass for orientation and mechanical collisions
- Verify paste layer (F.Paste):
  - stencil openings should appear only on SMD pads
  - through-hole pads should NOT have paste apertures
  - if paste appears on through-hole pads, check footprint paste settings
- Verify fiducial marks are visible in Gerber viewer.

Cross-check with:
- [docs/pcbway-assembly.md](pcbway-assembly.md) (TBD if not yet created)
- [docs/bom-revA-status.md](bom-revA-status.md) (TBD if not yet created)
- [docs/sourcing-checklist.md](sourcing-checklist.md) (TBD if not yet created)

#### Stop and check
- No missing placements for assembled parts.
- Connector orientations are intentional.
- Board size remains within target.
- Paste layer is correct (SMD only).
- Fiducials visible in Gerber output.
- All files come from the same final PCB revision.

---

### Recipe 18: Bring-Up Checklist (First Power-On)

#### Steps
- Visual inspection before power:
  - confirm no obvious shorts, bridges, or reversed polarized parts
  - confirm module and connector orientation one more time
- Apply input power with current-limited bench supply if available.
- Measure test points in this order:
  - VIN_PROTECTED: should track input minus expected protection drop
  - 12V: near nominal 12V
  - 3V3: near nominal 3.3V
- If rails are valid, check basic functional signs:
  - programming header rail presence
  - fan connector power pin behavior
  - display header power pin behavior

#### If a check fails
- Remove power immediately.
- Re-check polarity and solder bridges around power modules and protection stage.
- Re-check net class assignment and any last-minute reroutes on VIN_PROTECTED, 12V, 3V3.
- Fix, then repeat from step 1.

---

### Recipe 19: Troubleshooting Matrix (Beginner Quick Guide)

| Symptom | Most likely cause | First place to check |
|---|---|---|
| 3V3 missing | wrong module orientation or short on 3V3 rail | Power3V3 placement area and nearby passives |
| 12V low or unstable | input drop, wrong module wiring, heavy short | InputProtection path, K7812 area, fan power route |
| DRC keeps reporting clearance errors | net classes or clearances not applied consistently | Board Setup net classes and changed tracks in dense zones |
| Route cannot complete where expected | stale zone fill or conflicting keep-out | refill zones, then inspect keep-out and class constraints |
| UART/programming unstable | RX/TX swapped or noisy routing near power paths | programming header routing and connector pin mapping |
| Pick-and-place misalignment | missing or misplaced fiducials | verify fiducial marks in Gerber output and on physical board |

---

### Recipe 20: Done Definition

You are ready to submit when:
- DRC has no blocking issues.
- All recipe stop-checks pass.
- Manufacturing package is complete and consistent.
- Silkscreen is complete and readable.
- Fiducials are present.
- Final state is committed/tagged in git.

---

### Appendix A: KiCad 10 Quick Keys For This Cookbook

Use only the keys you need for this flow:
- X: start routing.
- V: place a via while routing.
- M: move selected item.
- R: rotate selected item.
- B: refill all zones.
- D: drag track segment while preserving connectivity.
- Del: delete selected segment/item.

If a key behaves differently in your setup, verify in KiCad Preferences before relying on it.

---

### Appendix B: PCBWay 2026 Contest Submission Pack

This appendix aligns your project package with the official contest evaluation areas.

#### Official selection criteria (from contest page)
- Project Design: 30%
- Project Documentation: 30%
- Practical Impact: 25%
- Project Popularity: 15%

#### Timeline checkpoints
- Submission period: April 15, 2026 to June 15, 2026.
- Review period: June 16, 2026 to June 29, 2026.
- Results announcement: July 6, 2026.

#### Contest-ready package checklist
- Project Design evidence
  - one architecture image showing power, control, and interfaces
  - one annotated PCB image highlighting critical layout decisions
  - one short list of key engineering tradeoffs and why they were chosen
- Project Documentation evidence
  - clear project description (problem, target user, solution)
  - full source files (KiCad project, schematics, PCB)
  - BOM, CPL, Gerbers, drills, assembly notes
  - functional demo materials (photos and short video)
- Practical Impact evidence
  - define the real-world problem solved
  - define measurable value (cost, reliability, usability, safety, power)
  - include bring-up and test results that demonstrate usable behavior
- Popularity plan
  - publish early enough for feedback and iteration
  - share updates with meaningful progress, not only final images
  - answer project comments and questions quickly and clearly

#### Contest upload asset checklist (literal files to prepare)
- Hero project image (clear board photo or high-quality render).
- One annotated PCB layout image showing critical decisions.
- One architecture/block diagram image.
- Short demo video or GIF showing real function.
- Source archive or repository link with KiCad project files.
- Manufacturing archive containing Gerbers, drills, BOM, CPL, and assembly notes.
- License/open-source declaration and attribution notes.

#### Judge-friendly project page structure
- Problem statement in 3 to 5 lines.
- Design goals and pass/fail requirements.
- System architecture block diagram.
- Hardware implementation notes linked to layout screenshots.
- Validation results (ERC, DRC, electrical checks, bring-up outcomes).
- Manufacturing outputs and assembly readiness.
- Open-source files and license declaration.

#### Integrity and originality gate
- If the design builds on prior work, credit the original project clearly.
- List what is original in this entry (features, layout strategy, documentation quality, validation depth).
- Keep a short change log of improvements made during contest period.
- Do not reuse third-party work without attribution and permission where required.

#### Pre-submit final gate
- Re-run ERC/DRC and confirm clean state.
- Verify all uploaded files are from the same final revision.
- Verify project page includes at least one functional demonstration artifact.
- Verify the project is submitted under the 2026 KiCad PCB Design Contest tag/category.

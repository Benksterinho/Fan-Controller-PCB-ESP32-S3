# PCB Layout Next 45 Min Execution Sheet (Rev A)

Use this as the exact short-cycle plan for your current board state.
Goal: finish the core power + control backbone without over-optimizing too early.

## Current-state assumptions from latest snapshot

1. InputProtection is placed bottom-left.
2. 12V and 3V3 regulator clusters are placed near bottom-center.
3. ESP32 module is placed upper-center with antenna keepout visible.
4. Display and fan-related parts are still partially spread on the right side.
5. Some nets are still long/unrouted and some components are outside their final local cluster.

If any assumption is wrong, still follow the same order below.

---

## 0) Fast preflight (3 min)

1. Save project.
2. Confirm board stack is 2-layer (F.Cu + B.Cu).
3. Confirm net classes are active (VIN_PROTECTED, 12V, 3V3 trunks wider than signal nets).
4. Confirm ESP32 antenna keepout still blocks copper/vias.
5. Create or keep a B.Cu GND zone now (working pass), then refill zones.

Stop check:
- You can route while seeing realistic return-path constraints.

---

## 1) Placement tighten pass (8 min)

Do not route yet; fix placement first.

1. Pull fan-control parts (Q1, gate/base resistor chain, tach filter parts) close to J_FAN1.
2. Keep fan power decoupling close to J_FAN1 and the 12V handoff.
3. Keep display series parts close to the display header side, not deep under ESP32.
4. Keep programming support passives close to J_PROG1 and EN/BOOT path origin.
5. Keep all bodies at least 1 mm from board edge.

Stop check:
- Each functional cluster is physically local and ratsnest lines are shorter/more vertical than diagonal.

---

## 2) Route power backbone only (12 min)

Route these nets to completion before touching SPI/UART/control:

1. J_PWR1 -> F1 -> D1 -> VIN_PROTECTED.
2. VIN_PROTECTED trunk split to U1 input and U2 input.
3. U1 output -> 12V trunk toward fan cluster.
4. U2 output -> 3V3 trunk toward ESP32 + display/programming feed points.

Rules during this pass:
1. Keep trunks short, direct, and class-width controlled.
2. For any trunk layer change, use parallel vias:
   - 3V3: at least 2 vias.
   - VIN_PROTECTED and 12V: at least 3 vias where space allows.
3. Do not neck a wide trunk into one via.
4. Refill zones after each trunk block.

Stop check:
- VIN_PROTECTED, 12V, and 3V3 trunks are continuous and obviously intentional.

---

## 3) Lock surge and return quality (8 min)

1. Keep TVS return to GND very short and wide.
2. Add 2-3 nearby GND stitching vias around TVS and input-cap GND pads.
3. Ensure input bulk and bypass capacitor GND pins hit GND copper quickly.
4. Inspect B.Cu for GND neck-downs below power-entry and regulator loops.

Stop check:
- No obvious long skinny return path in the input protection area.

---

## 4) Route must-have control nets (10 min)

Only these first:

1. EN_RESET and GPIO0_BOOT from programming header to ESP32 support network.
2. UART_TX and UART_RX to programming header.
3. FAN_PWM and FAN_TACH between ESP32 and fan cluster.

Hold off for next cycle:
1. Full display SPI cleanup.
2. Silkscreen beautification.
3. Final via-stitching density pass.

Stop check:
- Board has complete power plus bring-up control path.

---

## 5) End-of-cycle gate (4 min)

1. Refill all zones.
2. Run DRC.
3. Fix only blocking/real errors found in nets touched this cycle.
4. Save checkpoint file name suggestion: revA_layout_checkpoint_power_control.

Done definition for this 45-minute sheet:
1. Power entry and rail trunks are routed and reviewable.
2. TVS/cap GND return quality is enforced with short paths and local vias.
3. Programming and fan basic control nets are connected.
4. DRC has no new unresolved critical errors from this cycle.

---

## Quick next cycle (preview)

1. Finish display SPI/control routing and resistor placement polish.
2. Add top-side GND helper pour and tune stitching in quiet/open areas.
3. Final silkscreen labels, pin-1 markers, and readability pass.
4. Full DRC cleanup and manufacturability review.
# Sheet Spec: ExternalProgrammingHeader

**KiCad sheet:** `ExternalProgrammingHeader`  
**Purpose:** Minimal external programming/debug interface for ESP32-S3 using an external downloader/USB-TTL adapter.  
**Input nets:** `3V3`, `GND`, `UART_TX` (from ESP32), `UART_RX` (to ESP32), `EN_RESET`, `GPIO0_BOOT`  
**Output:** 1x6 2.54mm right-angle through-hole header exposed for cable access

---

## Interface Decision (Rev A)

Rev A uses an external programming interface instead of an on-board USB-UART bridge. This removes high-speed USB routing, ESD/CC support circuitry, and bridge IC complexity from the PCB.

**Benefits:**
- Simpler layout and lower routing risk
- Fewer parts and faster bring-up iterations
- Compatible with many external programming adapters

**Trade-offs:**
- Requires an external cable/adapter for flashing and serial logs
- Correct cable wiring must be documented and followed

---

## Connector

| Parameter | Value |
|-----------|-------|
| Type | Pin header, 1x6, 2.54mm pitch, right-angle, through-hole |
| Reference | `J_PROG` |
| Locked LCSC part | C42453955 (hanxia HX PZ2.54-1x6P WZ-P) |
| Assembly | THT (can be populated or left unpopulated for bench pads only) |
| Placement | Accessible board edge or service area |

Suggested pin order:

| Pin | Net | Direction | Notes |
|-----|-----|-----------|-------|
| 1 | GND | Power return | Common ground |
| 2 | 3V3 | Power (optional target reference/power) | Use carefully if adapter powers target |
| 3 | UART_TX | Output from ESP32 | Connect to adapter RX |
| 4 | UART_RX | Input to ESP32 | Connect to adapter TX |
| 5 | EN_RESET | Control input (active-low) | External reset/program control |
| 6 | GPIO0_BOOT | Control input (active-low) | External boot strap control |

---

## External Downloader Wiring

Adapter to board mapping:
- Adapter RX -> `UART_TX`
- Adapter TX -> `UART_RX`
- Adapter GND -> `GND`
- Adapter 3V3 (optional) -> `3V3`
- Adapter reset/RTS control (if available) -> `EN_RESET`
- Adapter boot/DTR control (if available) -> `GPIO0_BOOT`

Manual flashing fallback (if control lines are unavailable):
1. Hold BOOT button
2. Press and release RESET
3. Release BOOT
4. Start upload/flash sequence

---

## Electrical Rules

1. `EN_RESET` and `GPIO0_BOOT` are active-low control nets.
2. Keep existing pull-up and button networks on ESP32S3Core unchanged.
3. Do not add heavy loads to `GPIO0_BOOT`.
4. Keep UART traces short and direct to reduce noise pickup.
5. If 3V3 is exposed, clearly document whether the external adapter is allowed to power the board.

---

## Net Summary

| Net | Direction | Notes |
|-----|-----------|-------|
| `UART_TX` | Output from ESP32 | UART0 TX (GPIO43) |
| `UART_RX` | Input to ESP32 | UART0 RX (GPIO44) |
| `EN_RESET` | Input to ESP32 reset path | Active-low reset control |
| `GPIO0_BOOT` | Input to ESP32 boot strap path | Active-low boot control |
| `3V3` | Input/reference rail | Optional target power/reference |
| `GND` | Return | Shared ground |

---

## Layout Rules

1. Place `J_PROG` where probe clips/cables can be attached without blocking other connectors.
2. Keep silk pin-1 marker and pin labels clear and readable.
3. Route `EN_RESET` and `GPIO0_BOOT` away from noisy/high-current switching traces.
4. Keep UART traces away from fan 12V power loop where practical.

---

## Checklist Before KiCad Capture

- [ ] Confirm `J_PROG` pin numbering and silkscreen labels match cable convention
- [ ] Verify `UART_TX`/`UART_RX` directions are not swapped
- [ ] Verify `EN_RESET` and `GPIO0_BOOT` are exported at root and reachable from header
- [ ] Verify BOOT and RESET buttons remain functional for manual flashing
- [ ] Confirm external adapter workflow is documented for bring-up

---

## Dependencies

- **Depends on:** `ESP32S3Core` (UART and control nets), `Power3V3` (`3V3`)
- **Consumed by:** none (leaf sheet)

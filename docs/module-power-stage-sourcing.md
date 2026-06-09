# Module-Based Power Stage (Option B)

This document is the final implementation reference for Rev A power architecture.

## Scope

- 12V rail from K7812-1000R3 module
- 3.3V rail from EVISUN K7803-1000R3 module
- Both modules fed directly from `VIN_PROTECTED` in parallel
- No discrete buck controllers, no external inductor tuning, no feedback divider design

## Locked Architecture

1. `InputProtection` exports `VIN_PROTECTED`
2. `Power12V` and `Power3V3` both take `VIN_PROTECTED` as input
3. `Power12V` outputs `12V` for the fan power path
4. `Power3V3` outputs `3V3` for ESP32-S3 and logic loads

## Locked Components (from BOM)

| Block | Ref | Part | LCSC | Status |
|---|---|---|---|---|
| InputProtection | D1 | SS36 | C16015 | LOCKED |
| Power12V | U1 | K7812-1000R3 | C5369648 | LOCKED |
| Power12V | C12_IN_BULK | 100uF/50V | C72480 | LOCKED |
| Power12V | C12_IN_BYP | 1uF/50V X7R | C15849 | LOCKED |
| Power12V | C12_OUT_BULK | 47uF/25V | C3029614 | LOCKED |
| Power12V | C12_OUT_BYP | 100nF/50V X7R | C307331 | LOCKED |
| Power3V3 | U2 | EVISUN K7803-1000R3 | C50396472 | LOCKED |
| Power3V3 | C33_IN_BULK | 100uF/50V | C72480 | LOCKED |
| Power3V3 | C33_IN_BYP | 10uF/50V X5R 1210 | C380537 | LOCKED |
| Power3V3 | C33_OUT_BULK | 100uF/10V low-ESR | C2887276 | LOCKED |
| Power3V3 | C33_OUT_BYP | 10uF/16V X5R 0603 | C92487 | LOCKED |

Backup module list (pin-compatible, verified):
- Power12V U1 backup: C969026 (K7812-1000R3, DEXU)
- Power3V3 U2 backup: C5369647 (K7803-1000R3, YLPTEC)

Source of truth: `docs/bom-revA.csv`

## Footprint Guidance

- Use SIP-3 module footprints that match supplier mechanical drawings
- If no exact library footprint exists, create a custom footprint from datasheet dimensions
- Keep symbol and footprint naming consistent with BOM entries

## Layout Rules

1. Place module input/output decoupling caps within 10 mm of corresponding pins
2. Keep VIN and GND returns short and wide
3. Keep high-current fan 12V routing separate from sensitive logic routing
4. Use a continuous ground plane and avoid narrow ground bottlenecks near module returns
5. Keep clear access to power connector and fan connector edges

## Bring-Up Checks

1. Verify `VIN_PROTECTED` under expected input range (19-24V)
2. Verify `12V` regulation under fan load
3. Verify `3V3` during Wi-Fi bursts and display activity
4. Confirm no brownouts on ESP32-S3 during fan startup + Wi-Fi TX
5. Log measured ripple and thermal behavior for Rev A validation notes

## Risk Watch

- K7803 stock depth can vary by vendor lot; prefer C50396472 primary with C5369647 as pre-vetted backup and re-check stock before order freeze
- With SS36 Schottky reverse-polarity protection, 17-18V input operation is not guaranteed. Prefer 19-24V adapters.
- If module substitution is required, re-check pinout, input range, and current rating before BOM update

## References

- `docs/architecture.md`
- `docs/schematic-plan.md`
- `docs/sheet-specs/Power12V.md`
- `docs/sheet-specs/Power3V3.md`
- `docs/bom-revA.csv`
- `docs/bom-revA-status.md`

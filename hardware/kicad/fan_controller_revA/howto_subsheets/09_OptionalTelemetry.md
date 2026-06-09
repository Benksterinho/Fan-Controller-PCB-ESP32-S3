# OptionalTelemetry How-To (Deferred for Rev A)

Target sheet: future revision only

## What this sheet does
This sheet is reserved for future telemetry features. It is not part of active Rev A capture.

Think of it as:
- Rev A: do not capture
- Future revision: optional diagnostics connector with clear net ownership

## Current Rev A rule
OptionalTelemetry is deferred from Rev A.

Do not add this sheet to the active Rev A root hierarchy. If bring-up later proves telemetry is needed, add it in a new revision with a fresh GPIO and BOM review.

## Future parts to place (only if enabled)
1. Telemetry connector/header
2. Optional line protection or series resistors
3. Optional pull-ups if I2C is used and this sheet owns them

## Future wiring template (only if enabled)
1. Power pins
- 3V3
- GND

2. Interface choice
- I2C option: SDA + SCL
- UART option: TX + RX
- Or both, if you explicitly allocate pins and labels

3. Labeling
- Add hierarchical labels for every exported telemetry net
- Use explicit direction in naming and documentation

## Architecture rules you must keep
1. Do not consume reserved/boot-critical pins.
2. Do not overload existing mandatory interfaces.
3. Keep telemetry optional and removable for production builds.

## Exit criteria (future revision only)
- Telemetry interface is fully labeled and documented.
- ERC has no real wiring errors.
- Net ownership and GPIO assignment are reviewed before merge.

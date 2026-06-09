# OptionalTelemetry

Onboard rail telemetry is deferred from Rev A.

Reasoning:
- Test points already cover bring-up and fault-checking needs.
- Removing telemetry keeps Rev A GPIO allocation, firmware scope, and BOM complexity tighter.
- If real diagnostic value appears during bring-up or later use, rail telemetry can return in a future revision.

Rev A guidance:
- Do not include an `OptionalTelemetry` sheet in the active schematic hierarchy.
- Do not reserve GPIOs or BOM lines for telemetry in Rev A.
- Keep this topic as a future-revision note, not an active capture task.

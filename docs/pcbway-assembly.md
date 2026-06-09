# PCBWay Assembly Notes

## Assembly strategy

- The design should be buildable as PCBWay PCBA without user soldering
- Favor SMT parts for the core circuitry
- Use through-hole connectors only when PCBWay can assemble them as part of the order
- Avoid any part that depends on the end user hand-soldering a critical connection

## Manufacturing constraints

- Keep the PCB outline under 99x99 mm
- Keep connector placement compatible with automated assembly and inspection
- Provide a clean BOM with sourceable MPNs and clear alternates

## Review gates

- Verify every required part can be sourced for PCBWay assembly
- Verify connector footprints and orientations before locking the board
- Verify the final outline stays under the target size after keep-outs are applied

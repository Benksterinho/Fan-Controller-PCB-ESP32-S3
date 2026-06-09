# InputProtection Sheet Spec

## Purpose

Protect the board from reverse polarity, overcurrent, and input transients before the DC/DC power modules.

## Nets

- `VIN_RAW`: raw 19-24V supported input from the screw terminal connector (17-18V not guaranteed with current Schottky reverse-polarity stage)
- `VIN_PROTECTED`: protected input rail feeding both the 12V and 3.3V module stages in parallel
- `GND`: system ground

## Required Functions

- Reverse-polarity protection
- Input fuse or resettable protection element
- TVS clamp for transient suppression
- Input filtering with bulk and high-frequency decoupling

## Recommended Input Caps (Rev A)

- `C_IN_BYP`: 100nF/50V ceramic close to connector/diode output for high-frequency noise suppression
- `C_IN_BULK`: 100uF/50V electrolytic bulk reservoir across `VIN_PROTECTED` to `GND` for hot-plug and load-step stability
- Keep bulk capacitor polarity correct: `+` to `VIN_PROTECTED`, `-` to `GND`

## Fuse Policy (Rev A)

- Use a 2A/125V 1206 input fuse
- Fast-acting is acceptable for Rev A maker use
- Keep reverse-polarity and TVS protection; rely on external PSU current limiting for rare edge cases
- Current BOM selection is Littelfuse 0466002.NRHF (LCSC C3105), 2A/63V, 1206.

## Design Rules

- Place protection parts close to the input connector
- Keep the high-current loop short and wide
- Use only parts that are comfortable at the expected input voltage and current
- Do not let this sheet become a catch-all for unrelated power or logic parts

## Review Gate

- Confirm the protected rail stays stable under startup and unplug/replug events
- Run basic cold/warm startup checks to confirm normal operation
- Confirm the protection parts are PCBWay-assemblable and stockable in China

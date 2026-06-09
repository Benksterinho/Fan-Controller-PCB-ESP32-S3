# Sourcing Checklist

Use this checklist before schematic freeze and again before BOM lock.

## What to verify

- Preferred part number
- Preferred LCSC `C` code
- Package footprint match
- Lifecycle status
- Stock at PCBWay-supported sourcing flow
- Stock at LCSC
- Minimum order quantity
- Price trend versus the alternate
- Whether an alternate stays footprint-compatible
- Search query and URL used to find the part (for reproducibility)

## Check sequence

1. Verify the preferred part is active.
2. Verify the preferred package is actually available.
3. Verify the alternate is available only if the preferred part is weak on stock or price.
4. Confirm the part does not force a layout or schematic change.
5. Record the result in the BOM shortlist draft.
6. Record the selected `C` code and at least one alternate `C` code.

## Verification Gate (v1)

Apply this gate before changing any row to `LOCKED`.

1. Identity gate:
	- Product page title resolves (not challenge/not-found).
	- `MPN` matches required part identity.
	- `Packaging` matches the KiCad footprint family and pitch.
2. Availability gate:
	- Page shows `In-Stock` with a positive quantity.
	- `Out of Stock` or `Part not found` is an automatic fail for `LOCKED`.
3. Reproducibility gate:
	- Capture verification date and exact URL used.
	- Keep at least one alternate candidate for critical lines.
4. Conflict handling:
	- If scripted curl output and browser output disagree, trust browser-rendered output.
	- If browser output shows challenge/blocked state, keep row as `PENDING`.

## Row status policy

- `LOCKED`: passes Identity + Availability + Reproducibility gates.
- `PENDING`: any gate fails or cannot be verified in current session.
- `SOURCED`: procurement-confirmed state after supplier order confirmation.

## Decision rule

- Keep the preferred part if it is active, available, and reasonably priced.
- Use the alternate only if it preserves layout risk and does not force a major redesign.
- If both are weak, pause schematic freeze and choose a different family before layout starts.

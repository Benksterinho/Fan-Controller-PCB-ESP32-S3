# Fan Controller Rev A - KiCad Notes

Generated schematic files were removed. This folder now serves as the capture guide and naming reference.

## Files

- howto_subsheets/ (step-by-step capture guides)
- pcb_layout/ (subsheet-specific PCB Editor placement and routing guides)
- README.md (capture order and shared net naming)

## Capture Order

1. InputProtection
2. Power12V
3. Power3V3
4. ESP32S3Core
5. Fan4Wire
6. DisplayHeader_ST7789
7. ExternalProgrammingHeader
8. TestPoints
9. OptionalExpansionHeader

## Global Net Names To Reuse

- VIN_PROTECTED
- 12V
- 3V3
- GND
- EN_RESET
- GPIO0_BOOT
- UART_TX
- UART_RX
- FAN_PWM
- FAN_TACH
- SPI_SCK
- SPI_MOSI
- DISP_CS
- DISP_DC
- DISP_RST

## Notes

- Recreate all schematic sheets from docs/sheet-specs/*.md.
- Keep net names aligned with this README to avoid cross-sheet drift.
- Onboard rail telemetry is deferred from Rev A; add it only in a later revision if bring-up shows a real need.

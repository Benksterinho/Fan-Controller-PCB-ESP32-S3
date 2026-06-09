# What You Need to Build This

This section lists all the essential items required to assemble, program, and use the ESP32 Fan Controller Rev A.

## Required Parts (not included in PCB assembly)

- **Power Supply:** 19-24V DC, 2A+ recommended (laptop brick or equivalent, 5.5x2.1mm or screw terminal). 17-18V operation is not guaranteed with the current Schottky reverse-polarity stage.
- **4-Pin PWM Fan:** Standard PC 12V 4-wire PWM fan (Intel pinout)
- **Display:** ST7789 SPI display module (1.3", 1.54", or 2.0"; 3.3V logic)
- **USB-UART Adapter:** 3.3V logic adapter (or ESP32 downloader) with TX/RX/GND and optional EN/BOOT control lines
- **Dupont Cables/Crimps:** Male/female Dupont leads for display/programming header extension and bring-up wiring

## Power Requirement for Programming

- **Important:** Programming/logging is via the external 1x6 programming header (`J_PROG`) and an external USB-UART adapter.
- The board must be powered from the external **19-24V supported input** during programming and serial logging.
- External programmer cable alone will not power the board logic rail in the current Rev A design unless you intentionally inject 3V3.

## Optional/Recommended

- **Screwdriver:** For terminal block
- **Jumper wires:** For expansion header or test points
- **Breadboard/Prototyping tools:** For experimenting with expansion GPIOs
- **Case or standoffs:** For mounting the assembled board

## Assembly/Bringup Checklist

- Confirm all required parts are present
- Connect power supply to screw terminal
- Connect fan to 4-pin header (verify pinout: 1-GND, 2-12V, 3-TACH, 4-PWM)
- Connect display to SPI header (verify pinout per display module)
- Connect external USB-UART adapter to `J_PROG` (TX/RX crossed, common GND, optional EN/BOOT)
- (Optional) Connect expansion header or telemetry as needed

See the BOM and sheet-specs for detailed part numbers and pinouts.

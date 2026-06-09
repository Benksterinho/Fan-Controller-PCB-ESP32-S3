# Requirements

## Core

- Input: 19-24V DC supported for Rev A with current Schottky reverse-polarity stage; 17-18V operation is not guaranteed
- Outputs: 12V fan rail and 3.3V logic rail
- Controller: ESP32-S3 running CircuitPython
- Fan: one 4-pin PWM fan channel with tach feedback
- Display: external 3.3V SPI ST7789 header
- Connectivity: Wi-Fi telemetry from a Linux host

## Optional

- Maker expansion header for safe unused ESP32-S3 GPIOs

## Constraints

- Board outline should remain below 99x99 mm
- Prefer parts with strong public reference designs
- Prefer full PCBWay assembly with no user soldering

# JumboNAS Fan Controller PCB (ESP32-S3, Rev A)

This repository contains the hardware design and supporting documentation for a maker-friendly 4-wire PWM fan controller board built around ESP32-S3.

The PCB is intended for projects that need to power and control a standard 12 V PC fan, read tach feedback, and optionally show status on an SPI display, with room for future telemetry and expansion.

## What This Board Is For

- Controlling one 12 V 4-wire PWM fan from an ESP32-S3.
- Reading fan tach pulses for RPM measurement.
- Running standalone logic in CircuitPython on ESP32-S3.
- Driving an external ST7789 SPI display header.
- Exposing safe optional GPIOs through an expansion header.

Typical use cases:

- DIY NAS or homelab cooling controller.
- Smart enclosure or cabinet fan controller.
- General embedded fan-control bring-up platform.

## Hardware Overview

- Input power: 19-24 V DC recommended.
- Reverse polarity and surge protection at board input.
- 12 V rail: K7812 SIP module regulator.
- 3.3 V rail: K7803 SIP module regulator.
- Controller: ESP32-S3-WROOM-1-N16R8.
- Fan interface: Intel-style 4-pin PWM fan header.
- Programming/debug: external 1x6 UART header.

Input note:

- 19-24 V is the supported operating range for Rev A.
- 17-18 V may work in some conditions but is not guaranteed with the current Schottky protection stage.

## Project Status

Rev A is an actively developed hardware revision focused on first-pass manufacturability and bring-up reliability.

Current priorities:

- Keep BOM practical for PCBWay/LCSC assembly.
- Keep board size within cost-efficient limits (target under 99 x 99 mm).
- Use conservative, proven power-stage choices for a first hardware spin.

## Repository Layout

- [docs](docs): design rationale, requirements, BOM, sourcing, and build notes.
- [docs/sheet-specs](docs/sheet-specs): per-subsheet intent and signal definitions.
- [hardware/kicad/fan_controller_revA](hardware/kicad/fan_controller_revA): KiCad project files for Rev A.
- [firmware](firmware): reserved area for CircuitPython firmware and bring-up utilities.

## If You Want To Build One

1. Read [docs/requirements.md](docs/requirements.md) for scope and constraints.
2. Read [docs/architecture.md](docs/architecture.md) for system-level decisions.
3. Review [docs/bom-revA.csv](docs/bom-revA.csv) and [docs/bom-revA-status.md](docs/bom-revA-status.md).
4. Open the KiCad project in [hardware/kicad/fan_controller_revA](hardware/kicad/fan_controller_revA).
5. Follow manufacturing notes in [docs/pcbway-assembly.md](docs/pcbway-assembly.md).

## Key Docs

- [docs/architecture.md](docs/architecture.md)
- [docs/requirements.md](docs/requirements.md)
- [docs/current-budget.md](docs/current-budget.md)
- [docs/schematic-plan.md](docs/schematic-plan.md)
- [docs/gpio-map.md](docs/gpio-map.md)
- [docs/bom-revA.csv](docs/bom-revA.csv)
- [docs/bom-revA-status.md](docs/bom-revA-status.md)
- [docs/sourcing-checklist.md](docs/sourcing-checklist.md)
- [docs/pcbway-assembly.md](docs/pcbway-assembly.md)

## Safety And Scope

This is a low-voltage maker hardware project and is provided as-is. Validate thermal behavior, fan startup behavior, and input transient behavior in your own enclosure and power environment before unattended use.

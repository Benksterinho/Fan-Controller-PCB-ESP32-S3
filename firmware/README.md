# Firmware Area

This folder is reserved for CircuitPython firmware, bring-up scripts, and test utilities for the ESP32-S3 controller.

Initial firmware goals:

- Fan PWM control
- Tach readback
- Wi-Fi telemetry receiver
- ST7789 display updates
- Optional expansion-header support

## Bring-Up Notes And Gotchas

### PWM Inversion Behavior

Fan PWM and display backlight use NPN open-collector stages, so logic is inverted at the controlled node.

```c
fan_duty = 1.0f - desired_speed;
bl_duty = 1.0f - desired_brightness;
```

### GPIO Warnings

- GPIO35/GPIO36/GPIO37 are reserved on N16R8 modules (internal PSRAM interface) and must not be reassigned.
- GPIO46 must not be driven HIGH during reset; doing so can force Download Boot instead of normal SPI boot.
- GPIO19/GPIO20 correspond to native USB D-/D+ on ESP32-S3; using them on expansion functions blocks future native USB reuse.

### External UART Wiring Reminder

Cross TX/RX between adapter and board:

- Adapter RX -> Board UART_TX
- Adapter TX -> Board UART_RX

This crossover is expected and is a common bring-up mistake.

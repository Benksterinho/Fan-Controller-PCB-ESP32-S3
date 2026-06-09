# Fan Controller Rev A — Recommended Improvements

## 🔴 Fix Before Gerbers

- **Add 10KΩ pull-up on GPIO3 to 3.3V** — Strapping pin with NO internal pull. Floating = undefined JTAG state, potential random boot failures. Pull HIGH selects USB Serial/JTAG.

## 🟡 Strongly Recommended

- **Add open-collector NPN stage on DISP_BL (reuse fan PWM topology)** — Use MMBT3904 + 2.2KΩ base resistor + 100KΩ base pull-down. Keeps GPIO drive current low and supports PWM dimming for higher-current backlight inputs.
- **Bump C_BULK near ESP32 from 10µF to 22–47µF** — WiFi TX bursts peak at ~355 mA. Espressif reference uses 22µF. Reduces 3.3V rail voltage droop.

## 🟢 Nice-to-Have (Rev B)

- **Expansion header (I2C + spare GPIOs)** — ~500 mA headroom on 3.3V rail available for peripherals (temp sensors, etc.)
- **12V power indicator LED** — No visual feedback on 12V rail currently; helpful for power-up debugging.
- **SPI_MISO path removed in Rev A** — Keep display MISO/SDO unconnected and omit R_MISO for Rev A; revisit only in a future revision if readback is required.

## 📝 Documentation Items

- **PWM is inverted on open-collector channels** — MMBT3904 open-collector stages invert duty logic (fan PWM and display BL sink). Document for firmware users.
- **Vin tiers** — 19–24V ✅ recommended / 16–18V ⚠️ may work / <16V 🔴 at your own risk.
- **GPIO35/36/37 reserved on N16R8** — Internally connected to Octal SPI PSRAM, not available for external use.
- **GPIO46 must not be driven HIGH at reset** — Would enter Download Boot instead of SPI Boot.

# Signal Hierarchy — Rev A Fan Controller

How power and signals flow from input to every load. Read top-to-bottom when wiring nets in KiCad.

---

## Power Path

```
Screw Terminal J_PWR (19-24V supported in, 2-pin, 5.08mm pitch; 17-18V not guaranteed with current Schottky reverse-polarity stage)
  │
  ├─→ [D1 SS36]  ← reverse-polarity block
  │         │
  │     [F1 2A]   ← overcurrent fuse
  │         │
  │    VIN_PROTECTED ────────────────────────────────────┐
  │         │                                            │
  │    [K7812 module]
  │    Vin → Vout
  │         │
  │        12V ──────────────────────────────────────────┐
  │         │                                            │
  │    [J_FAN pin 2]                               [C_FAN_BULK]
  │    (fan 12V supply)
  │
  └─→ [K7803 module]
     Vin → Vout
     (parallel input from VIN_PROTECTED, not cascaded from 12V)
            │
           3V3 ─────────────────────────────────────────────┐
            │          │            │           │
        [ESP32-S3]  [J_PROG]   [J_DISP VCC] [R_TACH_PU]
        (VDD pin)   (VCC pin)  (display)    (fan tach)
```

---

## Signal Paths

### Fan Control
```
ESP32 GPIO5 (FAN_PWM)
  → [R_BASE 2.2k] → Q1 Base (MMBT3904)
  → Q1 Collector → J_FAN pin 4 (PWM to fan)
     (Q1 Emitter → GND)
  also: [R_BPD 100k] between GPIO5 and GND (keep Q1 off at reset)
```

```
J_FAN pin 3 (TACH from fan, open-collector)
  → [R_TACH_PU 10k] to 3V3
  → [R_TACH_SERIES 1k] → ESP32 GPIO4 (FAN_TACH input)
                          └→ [D_TACH_CLAMP Schottky] (cathode 3V3, anode FAN_TACH)
```

### Display (SPI)
```
ESP32 GPIO6  → [R_SCK 33R]  → J_DISP pin (SCK)
ESP32 GPIO7  → [R_MOSI 33R] → J_DISP pin (MOSI)
ESP32 GPIO10 → [R_CS 33R]   → J_DISP pin (CS)
ESP32 GPIO9  → [R_DC 33R]   → J_DISP pin (DC)
ESP32 GPIO16 → [R_RST 33R]  → J_DISP pin (RST)
ESP32 GPIO40 → DISP_BL      → J_DISP pin (BLK, PWM-capable backlight control)
J_DISP pin (MISO/SDO) is NC (not connected) on Rev A
J_DISP VCC → 3V3 + [C_DISP_VCC 100nF] to GND
J_DISP GND → GND
```

### External Programming Header
```
J_PROG (1x6, 2.54mm)
  pin 1 → GND
  pin 2 → 3V3
  pin 3 → UART_TX  (ESP32 GPIO43)
  pin 4 → UART_RX  (ESP32 GPIO44)
  pin 5 → EN_RESET (active-low reset control)
  pin 6 → GPIO0_BOOT (active-low boot strap control)

External downloader interface:
  Adapter TX  → UART_RX
  Adapter RX  ← UART_TX
  Adapter RTS#/RESET control → EN_RESET (if auto-program capable)
  Adapter DTR#/BOOT control  → GPIO0_BOOT (if auto-program capable)
```

## Net Names (use exactly these in KiCad)

| Net | Voltage | Source sheet | Consumed by |
|-----|---------|-------------|-------------|
| `VIN_PROTECTED` | 19-24V supported (17-18V not guaranteed with current Schottky reverse-polarity stage) | InputProtection | Power12V, Power3V3 |
| `12V` | 12V | Power12V | Fan4Wire |
| `3V3` | 3.3V | Power3V3 | ESP32S3Core, ExternalProgrammingHeader, DisplayHeader, Fan4Wire (tach pull-up), OptionalExpansionHeader |
| `GND` | 0V | everywhere | everywhere |
| `FAN_PWM` | 3.3V logic | ESP32S3Core | Fan4Wire |
| `FAN_TACH` | 3.3V logic | Fan4Wire | ESP32S3Core |
| `DISP_BL` | 3.3V logic | ESP32S3Core | DisplayHeader_ST7789 |
| `UART_TX` | 3.3V logic | ESP32S3Core | ExternalProgrammingHeader |
| `UART_RX` | 3.3V logic | ExternalProgrammingHeader | ESP32S3Core |
| `EN_RESET` | 3.3V logic | ESP32S3Core | ExternalProgrammingHeader |
| `GPIO0_BOOT` | 3.3V logic | ESP32S3Core | ExternalProgrammingHeader |

# Current Budget

## 12V Rail

- Fan nominal current: 0.33A
- First-pass design target: 1.0A continuous minimum
- Purpose of margin: startup transient, fan swaps, and thermal headroom

## 3.3V Rail

- ESP32-S3 average current: well below 0.3A in typical use
- ESP32-S3 Wi-Fi burst current: plan for short peaks well above average load
- Display, telemetry, and header margin: keep the rail sized to 1.0A continuous minimum
- Purpose of margin: avoid brownouts during Wi-Fi activity and display refresh

## Design Intention

- The goal is first-pass success, not minimum BOM size
- The regulator families should be more than strong enough for the expected loads
- If later measurements show the board is much lighter than expected, a smaller regulator can be considered for a later revision

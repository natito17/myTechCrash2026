---
name: pin-check
description: >
  Verify FPGA and ESP32 pin assignments for CrashTech DE10-Lite projects.
  Use when: checking pin correctness, verifying QSF assignments, validating
  ARDUINO_IO usage, checking for JP1 violations, reviewing pin_config.h.
---

# Pin Check

Verify that all pin assignments in a project are correct and follow CrashTech conventions.

## When to Use

- After Editor creates or modifies QSF files or RTL with pin references
- During Reviewer's checklist pass
- When debugging "pin not working" issues

## Procedure

### 1. Check QSF Pin Assignments

Read the project's `.qsf` file and verify against the DE10-Lite reference:

| Signal | Pin | IO Standard |
|--------|-----|-------------|
| MAX10_CLK1_50 | PIN_P11 | 3.3-V LVTTL |
| ARDUINO_IO[0] | PIN_AB5 | 3.3-V LVTTL |
| ARDUINO_IO[1] | PIN_AB6 | 3.3-V LVTTL |
| ARDUINO_IO[2] | PIN_AB7 | 3.3-V LVTTL |
| ARDUINO_IO[3] | PIN_AB8 | 3.3-V LVTTL |
| ARDUINO_IO[4] | PIN_AB9 | 3.3-V LVTTL |
| ARDUINO_IO[5] | PIN_Y10 | 3.3-V LVTTL |
| ARDUINO_IO[6] | PIN_AA11 | 3.3-V LVTTL |
| ARDUINO_IO[7] | PIN_AA12 | 3.3-V LVTTL |
| ARDUINO_IO[8] | PIN_AB17 | 3.3-V LVTTL |
| ARDUINO_IO[9] | PIN_AA17 | 3.3-V LVTTL |
| ARDUINO_IO[10] | PIN_AB19 | 3.3-V LVTTL |
| ARDUINO_IO[11] | PIN_AA19 | 3.3-V LVTTL |
| ARDUINO_IO[12] | PIN_Y19 | 3.3-V LVTTL |
| ARDUINO_IO[13] | PIN_AB20 | 3.3-V LVTTL |
| ARDUINO_IO[14] | PIN_AB21 | 3.3-V LVTTL |
| ARDUINO_IO[15] | PIN_AA20 | 3.3-V LVTTL |
| ARDUINO_RESET_N | PIN_F16 | 3.3 V SCHMITT TRIGGER |

### 2. Check RTL Pin Usage

In the top-level `.sv` file:

- `ARDUINO_IO[0]` must be used as RX (input from ESP32)
- `ARDUINO_IO[1]` must be used as TX (output to ESP32)
- All unused `ARDUINO_IO` pins must be assigned `1'bz`
- `ARDUINO_RESET_N` must be `1'bz`
- No JP1 pins unless challenge explicitly allows

### 3. Check ESP32 Pin Config

In `main.cpp`:

- Must include `pin_config.h` (correct relative path)
- UART TX to FPGA: GPIO 16 (`PIN_FPGA_TX`)
- UART RX from FPGA: GPIO 17 (`PIN_FPGA_RX`)
- GPIO 0 (boot pin): not held LOW during power-on
- GPIO 6/7/8 (flash pins): never used

### 4. Cross-Check Directions

| ESP32 | Direction | FPGA |
|-------|-----------|------|
| GPIO 16 (TX) | → | ARDUINO_IO[0] (RX) |
| GPIO 17 (RX) | ← | ARDUINO_IO[1] (TX) |

If directions are swapped, flag as CRITICAL error.

## Output

Report each check as PASS or FAIL with specific details for failures.

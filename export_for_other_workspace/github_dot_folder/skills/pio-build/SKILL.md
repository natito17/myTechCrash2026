---
name: pio-build
description: >
  Step-by-step procedure to build ESP32 firmware using PlatformIO CLI.
  Use when: compiling ESP32 code, checking for C++ errors, verifying
  library dependencies, building Arduino firmware.
---

# PlatformIO Build

Build ESP32 firmware using the PlatformIO CLI.

## Prerequisites

- PlatformIO installed (VS Code extension or standalone)
- CLI at `$env:USERPROFILE\.platformio\penv\Scripts\pio.exe`
- Project has `platformio.ini` in the `esp32/` directory
- Source files in `esp32/src/`

## Procedure

### 1. Navigate to the ESP32 project directory

```powershell
cd <project-path>/esp32
```

### 2. Build

```powershell
& "$env:USERPROFILE\.platformio\penv\Scripts\pio.exe" run
```

Or if PlatformIO is on PATH:
```powershell
pio run
```

### 3. Check results

**Success indicators:**
- Output contains `SUCCESS`
- `Building .pio/build/esp32dev/firmware.bin` completes
- 0 errors

**Common errors:**

| Error | Cause | Fix |
|-------|-------|-----|
| `No such file or directory: pin_config.h` | Wrong include path | Fix relative path to `projects/common/esp32/pin_config.h` |
| `'Serial2' was not declared` | Missing Arduino.h | Add `#include <Arduino.h>` |
| `undefined reference to 'setup'` | Missing setup/loop | Add required Arduino functions |
| `library not found` | Missing lib_deps | Add to `platformio.ini` |
| `ESP32Servo.h: No such file` | Using deprecated library | Use raw LEDC instead |

### 4. Report

Report back with:
- Pass/fail status
- Number of errors and warnings
- Flash/RAM usage percentages if shown
- Any deprecation warnings

## Adding Libraries

Edit `platformio.ini` to add dependencies:

```ini
lib_deps =
    adafruit/Adafruit SSD1306@^2.5.7
    adafruit/Adafruit GFX Library@^1.11.5
```

Run `pio run` again after adding libraries — PlatformIO auto-installs them.

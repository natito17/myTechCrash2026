---
name: pio-upload
description: >
  Step-by-step procedure to upload firmware to ESP32 and monitor serial output
  using PlatformIO CLI. Use when: flashing ESP32, uploading firmware, monitoring
  serial output, debugging ESP32 runtime behavior.
---

# PlatformIO Upload & Monitor

Upload firmware to ESP32 and monitor serial output.

## Prerequisites

- Firmware built successfully (`pio run` passed)
- ESP32 DevKit connected via USB
- CP2102 driver installed (auto-detected by PlatformIO)

## Upload Procedure

### 1. Navigate to the ESP32 project directory

```powershell
cd <project-path>/esp32
```

### 2. Upload

```powershell
& "$env:USERPROFILE\.platformio\penv\Scripts\pio.exe" run -t upload
```

### 3. Check results

**Success indicators:**
- `Connecting........_____` followed by programming progress
- `Leaving... Hard resetting via RTS pin...`
- `SUCCESS`

**Common errors:**

| Error | Cause | Fix |
|-------|-------|-----|
| `No serial port found` | Board not connected | Check USB cable |
| `Failed to connect` | Boot pin held LOW or board busy | Release GPIO 0, press EN/reset |
| `Permission denied` | COM port locked by another app | Close Serial Monitor, other terminals |
| `Timed out waiting for packet header` | Upload mode not entered | Hold BOOT, press EN, release BOOT |

## Monitor Procedure

### 1. Start serial monitor

```powershell
& "$env:USERPROFILE\.platformio\penv\Scripts\pio.exe" device monitor
```

Monitor speed defaults to `monitor_speed` in `platformio.ini` (should be 115200).

### 2. Read output

- First 10-20 lines usually contain boot info and startup messages
- Look for the project's startup print (e.g., `"<challenge-id> starting..."`)
- Use Ctrl+C to exit

### 3. Upload + Monitor combined

```powershell
& "$env:USERPROFILE\.platformio\penv\Scripts\pio.exe" run -t upload -t monitor
```

## Notes

- Do NOT hardcode COM ports — PlatformIO auto-detects
- If multiple serial devices connected, PlatformIO may prompt for selection
- The ESP32's USB serial (`Serial`) is separate from the FPGA UART (`Serial2`)

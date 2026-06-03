---
name: new-challenge-scaffold
description: >
  Scaffold a new challenge project with the standard CrashTech directory structure.
  Creates FPGA and ESP32 folders with template files. Use when: starting a new
  challenge, creating project skeleton, setting up a new demo.
---

# New Challenge Scaffold

Create the standard project structure for a new CrashTech challenge or demo.

## When to Use

- Planner is starting a new challenge
- User wants to set up a fresh project skeleton

## Standard Structure

```
projects/<challenge-id>/
├── esp32/
│   ├── platformio.ini
│   └── src/
│       └── main.cpp
└── fpga/
    ├── <challenge-id>.qpf
    ├── <challenge-id>.qsf
    └── src/
        └── <challenge-id>_top.sv
```

## Template Files

### platformio.ini

```ini
[env:esp32dev]
platform = espressif32
board = esp32dev
framework = arduino
monitor_speed = 115200
lib_deps =
```

### main.cpp

```cpp
#include <Arduino.h>
#include "../../../common/esp32/pin_config.h"

void setup() {
    Serial.begin(115200);
    Serial2.begin(FPGA_BAUD, SERIAL_8N1, PIN_FPGA_RX, PIN_FPGA_TX);
    Serial.println("<CHALLENGE-ID> starting...");
}

void loop() {
    // TODO: implement challenge logic
}
```

Adjust the `#include` path based on project depth relative to `projects/common/`.

### .qpf

```
QUARTUS_VERSION = "17.1"
DATE = "2026.05.27"
PROJECT_REVISION = "<challenge-id>"
```

### .qsf

Create with:
- `FAMILY "MAX 10"`, device `10M50DAF484C7G`
- `TOP_LEVEL_ENTITY <challenge-id>_top`
- `PROJECT_OUTPUT_DIRECTORY output_files`
- All SystemVerilog source files
- Full pin assignments (copy from `de10lite-board-and-build` skill reference)

### _top.sv

```systemverilog
module <challenge_id>_top (
    input           MAX10_CLK1_50,
    input   [9:0]   SW,
    input   [1:0]   KEY,
    output  [9:0]   LEDR,
    output  [7:0]   HEX0, HEX1, HEX2, HEX3, HEX4, HEX5,
    inout   [15:0]  ARDUINO_IO,
    inout           ARDUINO_RESET_N
);

    // High-Z all unused Arduino pins
    assign ARDUINO_IO = 16'hzzzz;
    assign ARDUINO_RESET_N = 1'bz;

    // Blank all displays
    assign HEX0 = 8'hFF;
    assign HEX1 = 8'hFF;
    assign HEX2 = 8'hFF;
    assign HEX3 = 8'hFF;
    assign HEX4 = 8'hFF;
    assign HEX5 = 8'hFF;

    // LEDs off
    assign LEDR = 10'b0;

    // TODO: implement challenge logic

endmodule
```

## Procedure

1. Determine the challenge ID (e.g., `uart-loopback`)
2. Create all directories and template files listed above
3. Replace all `<challenge-id>` placeholders with the actual ID
4. Adjust the `pin_config.h` include path based on actual depth
5. Report the file list back to the caller

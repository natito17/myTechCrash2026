---
name: uart-protocol
description: >
  UART protocol reference and implementation guide for FPGA-ESP32 communication
  in CrashTech projects. Use when: implementing UART TX/RX modules in RTL,
  setting up Serial2 on ESP32, debugging communication issues, designing
  custom protocols over UART.
---

# UART Protocol

Reference for FPGA ↔ ESP32 UART communication in CrashTech projects.

## Default Configuration

| Parameter | Value |
|-----------|-------|
| Baud rate | 9600 |
| Data bits | 8 |
| Parity | None |
| Stop bits | 1 |
| Logic level | 3.3V |
| Clock | 50 MHz (FPGA side) |

## Physical Connections

```
ESP32 GPIO 16 (TX) ──→ FPGA ARDUINO_IO[0] (RX)  [PIN_AB5]
ESP32 GPIO 17 (RX) ←── FPGA ARDUINO_IO[1] (TX)  [PIN_AB6]
ESP32 GND ──────────── FPGA Arduino Header GND
```

## FPGA UART RX Module Pattern

Baud rate divisor for 9600 baud at 50 MHz: `50_000_000 / 9600 = 5208`

```systemverilog
// Key parameters
localparam CLKS_PER_BIT = 5208;  // 50MHz / 9600 baud

// State machine: IDLE → START → DATA (8 bits) → STOP → IDLE
// Sample at mid-bit (CLKS_PER_BIT / 2 for start, CLKS_PER_BIT for data)
```

The `demos/internet_clock/fpga/src/uart_rx.sv` file contains a working RX module.

## FPGA UART TX Module Pattern

```systemverilog
// Same CLKS_PER_BIT = 5208
// State machine: IDLE → START → DATA (8 bits, LSB first) → STOP → IDLE
// Hold each bit for CLKS_PER_BIT clock cycles
// TX line idles HIGH
```

## ESP32 Side

```cpp
#include "pin_config.h"

void setup() {
    Serial.begin(115200);                                    // Debug USB
    Serial2.begin(FPGA_BAUD, SERIAL_8N1, PIN_FPGA_RX, PIN_FPGA_TX);  // FPGA UART
}

void loop() {
    // Receive from FPGA
    if (Serial2.available()) {
        uint8_t byte = Serial2.read();
    }
    // Send to FPGA
    Serial2.write(data);
}
```

## Custom Protocol Design

When designing a multi-byte protocol over this UART link:

1. **Use a header byte** to mark packet start (e.g., `0xAA`)
2. **Include a length byte** if variable-length payloads
3. **Keep packets short** — at 9600 baud, 1 byte ≈ 1.04 ms
4. **Add a checksum** for error detection (XOR of payload bytes)
5. **Handle byte-level framing** — the UART gives you bytes, not packets

Example packet format:
```
[HEADER: 0xAA] [LENGTH: N] [PAYLOAD: N bytes] [CHECKSUM: XOR]
```

## Debugging Tips

- Use LEDs to indicate RX activity on FPGA side
- Use `Serial.println()` on ESP32 to echo received data to USB serial
- If no data received: check TX/RX directions (most common error)
- If garbled data: check baud rate divisor calculation
- If intermittent: check GND connection between boards

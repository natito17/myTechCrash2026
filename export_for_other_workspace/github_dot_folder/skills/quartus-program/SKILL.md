---
name: quartus-program
description: >
  Step-by-step procedure to program a DE10-Lite FPGA via USB-Blaster using
  Quartus Prime Lite 17.1. Use when: flashing SOF file to FPGA, programming
  the board, verifying JTAG connection.
---

# Quartus Program

Program a DE10-Lite FPGA with a compiled `.sof` file via USB-Blaster.

## Prerequisites

- Quartus Prime Lite 17.1 installed at `C:\intelFPGA_lite\17.1\`
- DE10-Lite connected via USB
- USB-Blaster driver installed (from `C:\intelFPGA_lite\17.1\quartus\drivers\usb-blaster`)
- A compiled `.sof` file in `output_files/`

## Procedure

### 1. Verify USB-Blaster connection

```powershell
& "C:\intelFPGA_lite\17.1\quartus\bin64\quartus_pgm.exe" -l
```

Should show: `USB-Blaster [USB-0]`

### 2. Program the device

```powershell
cd <project-path>/fpga
& "C:\intelFPGA_lite\17.1\quartus\bin64\quartus_pgm.exe" -c "USB-Blaster [USB-0]" -m JTAG -o "P;output_files/<project_name>.sof"
```

### 3. Check results

**Success indicator:** `Configuration device(s) programmed successfully`

**Common errors:**

| Error | Cause | Fix |
|-------|-------|-----|
| `No USB-Blaster found` | Board not connected or driver issue | Check USB, reinstall driver |
| `JTAG chain broken` | Bad connection | Replug USB, restart board |
| `Programming failed` | Corrupt SOF or wrong device | Recompile, check device setting |

### 4. Verify on hardware

After programming:
- Check that LEDs/7-seg show expected behavior
- If UART challenge: verify ESP32 communication

## Notes

- SRAM-based programming (`.sof`) is volatile — lost on power cycle
- For persistent programming, use `.pof` flash programming (rarely needed for hackathon)

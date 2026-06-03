---
name: quartus-compile
description: >
  Step-by-step procedure to compile an FPGA project using Quartus Prime Lite 17.1
  on the DE10-Lite (MAX 10). Use when: compiling RTL, running Quartus synthesis,
  checking for compile errors, generating SOF file.
---

# Quartus Compile

Compile a DE10-Lite FPGA project using Quartus Prime Lite 17.1.

## Prerequisites

- Quartus Prime Lite 17.1 installed at `C:\intelFPGA_lite\17.1\`
- Project has a valid `.qpf` and `.qsf` in the `fpga/` directory
- All source `.sv` files listed in the `.qsf`

## Procedure

### 1. Navigate to the project's FPGA directory

```powershell
cd <project-path>/fpga
```

### 2. Run full compilation

```powershell
& "C:\intelFPGA_lite\17.1\quartus\bin64\quartus_sh.exe" --flow compile <project_name>
```

Where `<project_name>` matches the `PROJECT_REVISION` in the `.qpf` file (no extension).

### 3. Check results

**Success indicators:**
- Output contains: `Quartus Prime Full Compilation was successful`
- `output_files/<project_name>.sof` exists
- 0 errors (warnings are usually acceptable)

**Common errors:**

| Error | Cause | Fix |
|-------|-------|-----|
| `can't find design entity` | Wrong `TOP_LEVEL_ENTITY` in QSF | Match module name in .sv |
| `can't resolve pin` | Pin assignment mismatch | Check pin names against QSF |
| `multiple drivers` | Signal driven from two places | Fix RTL logic |
| `inferred latch` | Missing `default` in case or `else` in if | Add missing branches |

### 4. Report

Report back with:
- Pass/fail status
- Number of errors and warnings
- Any critical warnings (timing, pin conflicts)
- Confirmation that `.sof` file was generated

## Reference

For full pin assignments and QSF templates, see the `de10lite-board-and-build` skill
in `.copilot/skills/de10lite-board-and-build/SKILL.md`.

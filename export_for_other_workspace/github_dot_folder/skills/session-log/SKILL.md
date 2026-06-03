---
name: session-log
description: >
  Log challenge results to SESSIONS.md at the repo root. Use when: a challenge
  is completed (pass or fail), recording session outcomes, tracking hackathon
  progress.
---

# Session Log

Append challenge results to `SESSIONS.md` at the repo root.

## When to Use

- Manager completes a challenge flow (step 6: DONE)
- User asks for a session summary

## Procedure

1. Check if `SESSIONS.md` exists at the repo root. If not, create it with this header:

```markdown
# CrashTech VLSI 2026 — Session Log

Challenge results and session history.

---
```

2. Append an entry using this exact format:

```markdown
## <YYYY-MM-DD> — <Challenge ID>
- Result: PASS | FAIL | IN PROGRESS
- Type: binary | performance
- Score/metric: <value or N/A>
- Files created: <list>
- Files modified: <list>
- Key decisions: <bullet points>
- Issues encountered: <bullet points>
- Loops needed: <number of Editor→Reviewer loops>
- Duration: <approximate time if known>
```

3. Entries are appended chronologically (newest at bottom).

## Example

```markdown
## 2026-05-27 — challenge-03-uart-loopback
- Result: PASS
- Type: binary
- Score/metric: N/A
- Files created: projects/challenge-03/fpga/src/uart_loopback_top.sv, projects/challenge-03/esp32/src/main.cpp
- Files modified: none
- Key decisions: Used existing uart_rx.sv module from demos/internet_clock
- Issues encountered: Initial TX/RX direction swap caught by Reviewer
- Loops needed: 1
- Duration: ~25 min
```

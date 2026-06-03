# Session Notes — Agent & Skill Architecture

## Decided Agent Lineup

| Agent | File | Role |
|---|---|---|
| **Manager** | `agents/manager.md` | Entry point. Routes challenges to Planner, orchestrates handoffs. Lightweight router. |
| **Planner** | `agents/planner.md` | Decomposes challenge into subtasks. Never writes code. Hands off to Editor. |
| **Editor** | `agents/editor.md` | Writes/modifies SV and C++ code per Planner instructions. Waits for user confirmation before applying. |
| **Reviewer** | `agents/reviewer.md` | Checks syntax, pin correctness, protocol compliance, plan-vs-implementation drift. Can make small fixes. |
| **Toolchain** | `agents/toolchain.md` | Runs Quartus compile/program, PlatformIO build/upload/monitor. Handles all terminal commands. |

## Decided Skill Lineup

| Skill | File | Used By |
|---|---|---|
| **quartus-compile** | `skills/quartus-compile.md` | Toolchain, Editor |
| **quartus-program** | `skills/quartus-program.md` | Toolchain |
| **pio-build** | `skills/pio-build.md` | Toolchain, Editor |
| **pio-upload** | `skills/pio-upload.md` | Toolchain |
| **pin-check** | `skills/pin-check.md` | Reviewer |
| **uart-protocol** | `skills/uart-protocol.md` | Reviewer, Editor |
| **new-challenge-scaffold** | `skills/new-challenge-scaffold.md` | Planner |
| **session-log** | `skills/session-log.md` | Manager |

## Flow

```
Challenge Input → Manager → Planner (decompose)
                              ↓
                           Editor (code, wait for confirmation)
                              ↓
                           Reviewer (check)
                              ↓ (if issues → Editor fix)
                              ↓ (if clean)
                           Toolchain (build/flash)
                              ↓
                           Manager (report result)
```

## Key Design Decisions

- No separate "bridge" agent — folded into Toolchain.
- All agents respect: no code edits without user confirmation, no git push without Avi's explicit permission.
- Default to Arduino header pins, never JP1 unless challenge explicitly requires it.
- Skills are reusable procedures; agents are personas with judgment.

## Open Items

- [ ] Write each agent `.md` file
- [ ] Write each skill `.md` file
- [ ] Review existing `agents/manager.md` and align with this plan

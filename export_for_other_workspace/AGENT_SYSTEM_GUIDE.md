# Agent & Skill System — Implementation Guide

> **Purpose**: Living document tracking the agent/skill architecture for CrashTech VLSI 2026.
> Any new agent (AI or human) should read this file first to understand what exists,
> what was decided, and what's next.

---

## Architecture Overview

Five specialized agents orchestrated by a Manager, backed by eight procedural skills.
All files follow the VS Code Copilot customization standard.

```
Challenge Input → Manager → Planner (decompose)
                              ↓
                           Editor (code, wait for confirmation)
                              ↓
                           Reviewer (check)
                              ↓ (if issues → Editor fix, max 3 loops)
                              ↓ (if clean)
                           Toolchain (build/flash)
                              ↓
                           Manager (report result, log session)
```

### Agent Roster

| Agent | Location | Role | Tools |
|---|---|---|---|
| **Manager** | `.github/agents/manager.agent.md` | Entry point. Routes challenges, orchestrates handoffs, logs sessions. | read, search, agent, todo |
| **Planner** | `.github/agents/planner.agent.md` | Decomposes challenge into subtasks. Never writes code. | read, search |
| **Editor** | `.github/agents/editor.agent.md` | Writes/modifies SV and C++ code per plan. Waits for user confirmation. | read, search, edit |
| **Reviewer** | `.github/agents/reviewer.agent.md` | Checks syntax, pins, protocol compliance, plan-vs-implementation drift. | read, search |
| **Toolchain** | `.github/agents/toolchain.agent.md` | Runs Quartus compile/program, PlatformIO build/upload/monitor. | read, search, execute |

### Skill Roster

| Skill | Location | Used By |
|---|---|---|
| **quartus-compile** | `.github/skills/quartus-compile/SKILL.md` | Toolchain, Editor |
| **quartus-program** | `.github/skills/quartus-program/SKILL.md` | Toolchain |
| **pio-build** | `.github/skills/pio-build/SKILL.md` | Toolchain, Editor |
| **pio-upload** | `.github/skills/pio-upload/SKILL.md` | Toolchain |
| **pin-check** | `.github/skills/pin-check/SKILL.md` | Reviewer |
| **uart-protocol** | `.github/skills/uart-protocol/SKILL.md` | Reviewer, Editor |
| **new-challenge-scaffold** | `.github/skills/new-challenge-scaffold/SKILL.md` | Planner |
| **session-log** | `.github/skills/session-log/SKILL.md` | Manager |

### Pre-Existing Skills (not modified)

These were in the repo before this system was built. They contain domain reference
material and are complementary to the new procedural skills.

| Skill | Location | Content |
|---|---|---|
| esp32-firmware | `.copilot/skills/esp32-firmware/SKILL.md` | ESP32 board specs, pin config, wiring, PlatformIO template |
| de10lite-board-and-build | `.copilot/skills/de10lite-board-and-build/SKILL.md` | DE10-Lite specs, QSF pin assignments, Quartus flow |
| de10lite-addon-peripherals | `.copilot/skills/de10lite-addon-peripherals/SKILL.md` | Addon PCB ADC, joystick, buttons, wheel |
| skill-creator | `.copilot/skills/skill-creator/SKILL.md` | Meta-skill for creating new skills |

---

## Key Design Decisions

1. **Agents at `.github/agents/`** — Using the VS Code standard `.agent.md` format (not the older `.github/copilot/agents/` path).
2. **Skills at `.github/skills/`** — Standard location. Existing `.copilot/skills/` are left untouched.
3. **No separate "bridge" agent** — Terminal operations folded into Toolchain.
4. **No code edits without user confirmation** — Editor always shows a summary and waits.
5. **No git push without Avi's explicit permission** — Enforced across all agents.
6. **Default Arduino header** — ARDUINO_IO[0..15], never JP1 unless challenge requires it.
7. **Skills are procedures, agents are personas** — Skills define step-by-step HOW. Agents decide WHAT and WHEN.
8. **New procedural skills complement existing reference skills** — e.g., `quartus-compile` (procedure) references `de10lite-board-and-build` (reference data).

---

## Migration Note

The old `manager.md` at `.github/copilot/agents/manager.md` predates this system.
The new `manager.agent.md` at `.github/agents/` supersedes it. The old file should
be removed once the new system is validated (requires user confirmation to delete).

---

## Session Log

### Session 1 — 2026-05-27

**What was done:**
- Read and analyzed the architecture plan from `agents_and_skills_settings.md`
- Audited existing agents (1 old manager) and skills (4 reference skills)
- Designed the file layout following VS Code Copilot standards
- Created this guide document
- Created all 5 agent files (Manager, Planner, Editor, Reviewer, Toolchain)
- Created all 8 skill files (quartus-compile, quartus-program, pio-build, pio-upload, pin-check, uart-protocol, new-challenge-scaffold, session-log)

**Decisions made:**
- Use `.github/agents/*.agent.md` (standard) instead of `.github/copilot/agents/` (legacy)
- New skills go to `.github/skills/`, existing `.copilot/skills/` left as-is
- Procedural skills reference existing reference skills rather than duplicating content

**Open items for next session:**
- [ ] Validate that VS Code discovers all 5 new agents in the agent picker
- [ ] Validate that all 8 skills are triggered by description matching
- [ ] Test the full flow: Manager → Planner → Editor → Reviewer → Toolchain on a real challenge
- [ ] Decide whether to remove old `.github/copilot/agents/manager.md` (legacy format, may conflict)
- [ ] Create `SESSIONS.md` at repo root (session-log skill will create it on first use)
- [ ] Verify skill cross-references work (new procedural skills referencing existing reference skills)
- [ ] Tune agent descriptions if skill/subagent discovery isn't firing correctly

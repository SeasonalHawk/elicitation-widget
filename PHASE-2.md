# Elicitation Widget — Phase 2 Planning

**Status:** Draft starting point
**Author:** Kenneth Benavides
**Date:** 2026-06-14

---

## What Phase 1 Delivered

Phase 1 shipped a single-file intake engine (`SKILL.md`) for Claude Desktop on Mac.
It uses `ask_user_input_v0` to render native choice cards. Install once, reuse across
every skill or plugin. Tested on Claude Desktop 1.12603.1 (3df4fd), 2026-06-11.

Phase 1 is complete and released as v4.0.0.

---

## What Phase 2 Is

Phase 2 extends the widget to work in all Claude environments: desktop, web, CLI, and
IDE (Claude Code, Cursor, VS Code). The engine stays in one SKILL.md. Phase 2 adds
internal dispatch modules for each interface type so the right rendering method is
selected automatically (or explicitly) based on the calling skill's declaration.

---

## Architecture

### Single File, Internal Dispatch

One SKILL.md. Multiple named modules inside it. The engine reads the calling skill's
`interface:` field and routes to the correct module. Every module produces the same
payload format. The calling skill never knows which module ran.

```
SKILL.md (v5.0.0)
├── ENGINE          Router + payload assembler (shared)
├── MODULE: native  ask_user_input_v0 — Claude Desktop / Claude.ai
├── MODULE: prose   Numbered text questions — Claude Code CLI, terminal, IDEs
├── MODULE: web     show_widget HTML form — browser-embedded contexts (future)
└── SELF-TEST       Runs all available modules, reports pass/fail per module
```

### Calling Skill Contract

The calling skill adds one field to its Step 0:

```
interface: auto | native | prose | web
```

- `auto` — tries native first, drops to prose on failure (default)
- `native` — forces ask_user_input_v0, errors if unavailable
- `prose` — skips native attempt, uses text mode directly
- `web` — routes to show_widget HTML form (Phase 2 research item)

No other changes to the calling skill are required. The QUESTIONS array format is
unchanged from v4.0.0.

### Stable Payload Contract

Every module produces the same output regardless of which interface ran:

```
[Elicitation complete]
Key 1: Value 1 | Key 2: Value 2 | ... | Key N: Value N
```

Calling skills read the payload by key. Interface selection is invisible to them.

---

## Modules

### MODULE: native (carried from v4.0.0)

Activation: `interface: native` OR `interface: auto` and ask_user_input_v0 renders.

Behavior: Single batched ask_user_input_v0 call with the full QUESTIONS array.
All questions appear together in one native Claude UI interaction.

### MODULE: prose (new in v5.0.0)

Activation: `interface: prose` OR `interface: auto` and native module failed.

Behavior: Prints all questions as a numbered list with lettered options.
User types letter choices in one reply (e.g., `A B C A B`).
Engine maps letters to keys and assembles the standard payload.

Example output:

```
Intake (text mode — 5 questions):

1. What kind of project are you building?
   A) Mobile app  B) Web application  C) CLI tool  D) Game

2. Where does this project stand today?
   A) Brand new idea  B) Notes exist, no code  C) In active development  D) Near completion

...

Reply with your letter choices in order, e.g.: A B C A B
```

### MODULE: web (placeholder — research required)

Activation: `interface: web`.

Status: Deferred. Requires confirming show_widget availability and behavior in
browser-embedded Claude contexts. Architecture is defined; implementation is not.

---

## Open Questions

| Question | Status |
|----------|--------|
| Does ask_user_input_v0 render in Claude Code CLI? | Unconfirmed. First task on Friday. |
| Does ask_user_input_v0 render in VS Code / Cursor extensions? | Unconfirmed. |
| Is show_widget available in browser-embedded Claude contexts? | Unconfirmed. |
| What does `auto` failure detection look like in practice? | Design pending. |
| Is there a timing/flash issue when native fails before prose kicks in? | Unknown. |

---

## Phase 2 Work Plan

| Step | Task | Depends On |
|------|------|------------|
| 1 | Run `test widget` in Claude Code CLI — confirm native pass or fail | Nothing |
| 2 | If native fails: prototype prose module in isolation | Step 1 |
| 3 | Confirm payload parity between native and prose output | Step 2 |
| 4 | Design `auto` detection logic (attempt native, catch failure, route to prose) | Step 3 |
| 5 | Write v5.0.0 SKILL.md: engine + native module + prose module | Step 4 |
| 6 | Update self-test to run all available modules and report per module | Step 5 |
| 7 | Research show_widget availability for web module | Parallel |
| 8 | If show_widget confirmed: implement web module | Step 7 |
| 9 | Update wiki: new modules, interface field, updated compatibility table | Step 5 |
| 10 | Release v5.0.0 | Step 9 |

---

## Version Target

**v5.0.0** — Multi-environment release.

Breaking change: adds `interface:` field to Step 0 contract. Field is optional with
`auto` as the default, so existing v4.0.0 calling skills continue to work without
modification. Native module behavior is unchanged from v4.0.0.

---

## First Task on Friday

Run `test widget` in Claude Code CLI. The self-test will report PASSED or FAILED.
That single data point determines how much of the prose module needs to be built
and whether `auto` detection needs a fallback path.

---

Copyright 2025-2026 Kenneth Benavides. MIT License.

# CLAUDE.md -- Elicitation Widget

## What This Is

A single-file Claude skill that acts as a shared intake engine. Skills and plugins
delegate question rendering to this widget by declaring a QUESTIONS array and
invoking Step 0. The widget handles all `ask_user_input_v0` mechanics.

## Repository Structure

```
elicitation-widget/
├── SKILL.md        The widget engine (engine + self-test + integration guide)
├── README.md       Public-facing documentation
├── LICENSE         MIT License
└── CLAUDE.md       This file
```

## Phase Scope

Phase 1 targets Claude Desktop for Mac only. Installation is done through the
Claude Desktop Customize panel — no filesystem path required. Tested on
Claude Desktop 1.12603.1 (3df4fd), 2026-06-11.

CLI support (Claude Code, Cursor, VS Code) is out of scope for Phase 1. The
open question is whether ask_user_input_v0 renders in CLI contexts. If it does
not, a different intake mechanism will be needed. Phase 2 will investigate.

## Key Constraints

- `SKILL.md` is the only file that matters for installation. Never split it.
- The engine logic in SKILL.md must never be modified to add skill-specific questions.
- Questions always live in the calling skill, never in this file.
- `ask_user_input_v0` is the only permitted intake method. No prose fallbacks.
- Do not add /mnt/ paths to any documentation. Phase 1 users install via Claude Desktop UI.

## Branching

- `main` -- stable releases only
- `dev` -- integration branch, active work happens here
- `feat/*` -- feature branches off dev

## Commit Style

Conventional commits: `feat:`, `fix:`, `docs:`, `refactor:`, `chore:`

## Versioning

Semantic versioning. Update the version field in `SKILL.md` frontmatter and the
VERSION HISTORY section on every release. Update README version table to match.

## Owner

Kenneth Benavides (ken@xdacorp.com)
Copyright 2025-2026 Kenneth Benavides. MIT License.

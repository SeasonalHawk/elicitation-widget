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

## Key Constraints

- `SKILL.md` is the only file that matters for installation. Never split it.
- The engine logic in SKILL.md must never be modified to add skill-specific questions.
- Questions always live in the calling skill, never in this file.
- `ask_user_input_v0` is the only permitted intake method. No prose fallbacks.

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

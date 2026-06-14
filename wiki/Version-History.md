# Version History

## Compatibility

| Platform | Status | Notes |
|----------|--------|-------|
| Claude Desktop | Supported | Tested on 1.12603.1 (3df4fd), 2026-06-11 |
| Claude.ai | Supported | Native UI surface |
| Claude Code | Supported | Requires ask_user_input_v0 to be available |
| Claude (API) | Supported | Requires ask_user_input_v0 to be available |
| VS Code / Cursor / IDE extensions | Future release | Planned. Interface module approach under design. |

All supported platforms require `ask_user_input_v0` to be available in the chat context. If the tool is unavailable, the engine stops and displays a clear error message rather than falling back silently. Run `test widget` to verify availability in any environment.

---

## v4.0.0 (current)

**Breaking change:** Replaced the sequential one-question-per-call loop with a single batched `ask_user_input_v0` call.

Changes:
- All questions now appear together in one native Claude UI interaction
- Users submit all answers in one action instead of cycling through questions one at a time
- Added Part C integration guide for skill and plugin authors
- Self-test updated to use the batched call
- Sequential loop retired

**Migration from v3:** No skill-side changes required. The QUESTIONS array format is unchanged. The widget's execution model changed internally. If your skills used the old sequential flow, they now get the batched experience automatically on upgrade.

---

## v3.0.0

Merged the engine and self-test into a single `SKILL.md`. Eliminated the need for a separate self-test file.

Changes:
- Self-test QUESTIONS drawn from a randomized 20-question bank (retired in v4)
- Single file install confirmed as the canonical model

---

## v2.1.0

Removed the hardcoded 8-question cap. The engine now iterates dynamically over the full QUESTIONS array regardless of length.

---

## v2.0.0

**Breaking change:** Replaced the `show_widget` HTML rendering engine with native `ask_user_input_v0`.

Changes:
- No HTML required
- Questions rendered as native Claude choice cards
- One question per call (later retired in v4.0.0)
- Faster, more reliable, no external rendering dependency

---

## v1.1.0

Template embedded directly in `SKILL.md` to eliminate file-read failures at runtime. Mandatory no-fallback rule added: if the tool is unavailable, the engine stops and reports an error instead of falling back to prose.

---

## v1.0.0

Initial release. Custom HTML widget rendered via `show_widget`. Question template stored as a separate file.

---

Copyright 2025-2026 Kenneth Benavides. MIT License.

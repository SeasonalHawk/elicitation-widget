# Elicitation Widget — Wiki

**Version:** 4.0.0 | **License:** MIT | **Author:** Kenneth Benavides

> **[View the full documentation site](https://seasonalhawk.github.io/elicitation-widget/)** — architecture, schema reference, integration guide, and compatibility all in one polished overview.

A shared intake engine for Claude skills and plugins. Install once. Every skill you build gets a native choice-card form for free.

---

## Pages

| Page | What it covers |
|------|----------------|
| [Architecture](Architecture) | How the engine works, file structure, the skill-widget contract |
| [Sequence Diagram](Sequence-Diagram) | Step-by-step execution flow from skill invocation to payload return |
| [QUESTIONS Schema](QUESTIONS-Schema) | Field definitions, types, rules, and annotated examples |
| [Integration Walkthrough](Integration-Walkthrough) | Four steps to wire any skill or plugin to the widget |
| [Self-Test](Self-Test) | How to verify the engine is working in your Claude environment |
| [Common Mistakes](Common-Mistakes) | What to avoid and why |
| [Version History](Version-History) | Changelog across all major versions |

---

## Compatibility

| Platform | Phase | Status |
|----------|-------|--------|
| Claude Desktop for Mac | Phase 1 | Supported. Tested on `1.12603.1 (3df4fd)`, 2026-06-11. |
| Claude.ai | Phase 2 | Planned. Not yet tested. |
| Claude Code | Phase 2 | Planned. Pending `ask_user_input_v0` confirmation. |
| Claude (API) | Phase 2 | Planned. |
| VS Code / Cursor / IDE extensions | Phase 2 | Planned. Interface module under design. |

Phase 1 is Claude Desktop for Mac only. All other platforms are planned for Phase 2. If `ask_user_input_v0` is unavailable in your chat context, the engine stops and reports a clear error. Run `test widget` to verify your environment before use.

---

## Quick orientation

Elicitation Widget moves intake logic out of individual skills and into a single shared engine. Skills do not manage intake. They hand off to the widget entirely and only tell it what questions to ask.

The only shared surface between a skill and the widget is the QUESTIONS array. The skill writes it. The widget reads it. Neither side knows how the other works.

`SKILL.md` is both the configuration and the executable. Installing it installs the interface contract and the implementation in one file. There is nothing else to configure.

---

Copyright 2025-2026 Kenneth Benavides. MIT License.

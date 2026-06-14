# Elicitation Widget

**A shared intake engine for Claude skills and plugins. Install once. Every skill you build gets a native choice-card form for free.**

Without Elicitation Widget, every Claude skill reimplements the same intake logic: ask a question, wait for input, ask another, repeat. That approach is fragile, inconsistent, and forces every skill author to solve the same problem from scratch.

Elicitation Widget changes that by moving the intake problem out of individual skills and into a single shared engine. Your skill does not manage intake. It hands that job off entirely, and only tells the engine what questions to ask. The engine handles everything else.

---

## The Problem It Solves

Claude skills often need context before they can do useful work: what kind of project is this, who is the audience, how detailed should the output be. The naive solution is to ask questions in prose, which is slow, inconsistent, and bypasses Claude's native UI capabilities.

Elicitation Widget solves this by acting as a shared form renderer. Skills delegate intake to the widget. The widget presents all questions together in a single native Claude UI interaction using `ask_user_input_v0`. The user taps through choice cards and submits once. The widget returns a structured key-value payload. The skill proceeds.

The result: a consistent, tap-friendly intake experience across every skill you build, with zero duplication of intake logic.

---

## The Design

Most skill toolkits put intake logic inside each skill. When you build five skills, you write intake five times. When you want to change how intake works, you change it five times. When one skill gets it wrong, the others are unaffected but inconsistent.

Elicitation Widget flips that arrangement. The intake logic lives in one place. Skills do not manage it. They only describe what they need: a list of questions and the options for each one. The widget receives that list and handles everything else. The skill author never touches `ask_user_input_v0` directly. They never think about rendering, batching, or payload assembly. They write the questions. The engine runs.

This means the engine can evolve independently of every skill that uses it. A skill author can add or remove questions without touching the engine. The engine can be improved without touching any skill. Neither side knows or cares how the other is built. The only thing they share is the format of the QUESTIONS array, which is documented below.

`SKILL.md` serves a dual role in this arrangement: it is both the configuration that describes what the engine should do and the executable instruction set that tells Claude how to do it. When you install the file, you are installing both the interface contract and the implementation in a single document. There is no separate config file, no registry, no setup script. The file is the system.

---

## How It Works

```
Calling Skill            Elicitation Widget         User
     |                          |                     |
     |-- declares QUESTIONS --> |                     |
     |-- invokes Step 0 ------> |                     |
     |                          |-- choice cards ---> |
     |                          | <-- all answers --- |
     | <-- payload ------------ |                     |
     |-- proceeds to Step 1 --> |                     |
```

The contract is a single QUESTIONS array. The skill writes it. The widget reads it. Neither side needs to know how the other works.

---

## Installation

### Claude Desktop for Mac (Phase 1)

**Step 1:** Download `SKILL.md` from this repository.

**Step 2:** Open Claude Desktop. Click **Customize**, then add the file.

That is it. No path to configure, no CLI commands, no dependencies. Claude Desktop loads the file directly from wherever you saved it on your Mac.

**Step 3:** Say `"test widget"` to confirm the choice cards render. If five questions appear and you can submit all at once, the engine is working.

---

## Quick Start: Add Intake to Your Skill

> These steps are for Claude Desktop for Mac. CLI support is planned for a future phase.

**Step 1:** Declare a QUESTIONS array in your skill's `SKILL.md`:

```
QUESTIONS = [
  {
    key: "Goal",
    question: "What do you want to accomplish?",
    type: "single_select",
    options: ["Option A", "Option B", "Option C"]
  },
  {
    key: "Audience",
    question: "Who is this for?",
    type: "single_select",
    options: ["Internal team", "External clients", "Public"]
  }
]
```

**Step 2:** Add Step 0 to your skill's execution sequence:

```
## STEP 0: Intake Form

Load the Elicitation Widget. It is installed as a skill in Claude Desktop
under the name "elicitation-widget".

Pass this skill's QUESTIONS array to the widget engine. The widget
will present all questions in a single native Claude UI interaction
and return the completed payload. Proceed to Step 1 immediately after
the payload is assembled.
```

**Step 3:** Use the returned payload in your skill's core task:

```
answers = {
  "Goal": "Option B",
  "Audience": "External clients"
}
```

That is the complete integration.

---

## QUESTIONS Format

| Field | Type | Description |
|-------|------|-------------|
| `key` | string | Short label. Becomes the field name in the payload. |
| `question` | string | Full sentence the user reads. |
| `type` | string | Always `"single_select"` |
| `options` | array | 2 to 4 short choice labels. |

Rules:
- Any number of questions is supported. No minimum, no maximum.
- Keys must be unique within the array.
- Questions must be complete, self-contained sentences.
- Each question must have between 2 and 4 options.

---

## Self-Test

To verify the widget is working in your Claude environment, say:

> **"test widget"**

Claude will run a 5-question live intake using a built-in sample inventory and report whether `ask_user_input_v0` rendered correctly.

Other trigger phrases: `"run widget test"`, `"test elicitation"`, `"does the widget work"`.

---

## Architecture

```
elicitation-widget/
└── SKILL.md          Single file. Contains the engine (Part A), the built-in
                      self-test (Part B), and the integration guide for skill
                      authors (Part C). No other files required.
```

The engine logic lives in Part A of `SKILL.md`. The self-test lives in Part B. The integration guide for skill authors lives in Part C.

See the [Wiki](../../wiki) for full architecture documentation, sequence diagrams, and integration walkthroughs.

---

## Compatibility

### Phase 1 (current release)

| Platform | Status |
|----------|--------|
| Claude Desktop for Mac | Supported |
| Claude.ai (web) | Not tested |
| Claude Code / CLI | Not supported in Phase 1 |

Phase 1 is built for and tested on Claude Desktop for Mac. The native `ask_user_input_v0` tool that powers the choice-card UI is available in Claude Desktop. CLI environments may require a different rendering approach and are out of scope for this release.

Tested on: Claude Desktop `1.12603.1 (3df4fd)` — 2026-06-11.

### Phase 2 (planned)

CLI and IDE support (Claude Code, Cursor, VS Code) will be evaluated in a future phase. The open question is whether `ask_user_input_v0` is available in those environments or whether a different intake mechanism is needed. No release date is set.

---

## Version History

| Version | Summary |
|---------|---------|
| 4.0.0 | Single batched `ask_user_input_v0` call. All questions in one interaction. Part C integration guide added. |
| 3.0.0 | Merged engine and self-test into single SKILL.md. |
| 2.1.0 | Removed hardcoded question cap. |
| 2.0.0 | Replaced HTML widget with native `ask_user_input_v0`. |
| 1.1.0 | Template embedded in SKILL.md. |
| 1.0.0 | Initial release. HTML widget via `show_widget`. |

---

## License

MIT License. Copyright 2025-2026 Kenneth Benavides. See [LICENSE](LICENSE) for full text.

---

## Topics

`claude` `claude-ai` `claude-code` `claude-skill` `claude-plugin` `ask-user-input` `intake-form` `skill-engine` `anthropic` `llm-tooling`

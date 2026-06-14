# Elicitation Widget

**A shared intake engine for Claude skills and plugins. Install once. Every skill you build gets a native choice-card form for free.**

Without Elicitation Widget, every Claude skill reimplements the same intake logic: ask a question, wait for input, ask another, repeat. That approach is fragile, inconsistent, and forces every skill author to solve the same problem from scratch.

Elicitation Widget separates the intake mechanics from the skill logic. Your skill declares its questions. The widget renders them. One file, installed once, works for everything you build.

---

## The Business Problem

Claude skills often need context before they can do useful work: what kind of project is this, who is the audience, how detailed should the output be. The naive solution is to ask questions in prose, which is slow, inconsistent, and bypasses Claude's native UI capabilities.

Elicitation Widget solves this by acting as a shared form renderer. Skills delegate intake to the widget. The widget presents all questions together in a single native Claude UI interaction using `ask_user_input_v0`. The user taps through choice cards and submits once. The widget returns a structured key-value payload. The skill proceeds.

The result: a consistent, tap-friendly intake experience across every skill you build, with zero duplication of intake logic.

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

**Step 1:** Download `SKILL.md` from this repository.

**Step 2:** Place it in your Claude skills directory:

```
/mnt/skills/user/elicitation-widget/SKILL.md
```

That is the only file required. No dependencies. No configuration. One file.

---

## Quick Start: Add Intake to Your Skill

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

Load the Elicitation Widget at:
/mnt/skills/user/elicitation-widget/SKILL.md

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
└── SKILL.md          Single file. Contains the engine, self-test, and
                      integration guide. No other files required.
```

The engine logic lives in Part A of `SKILL.md`. The self-test lives in Part B. The integration guide for skill authors lives in Part C.

See the [Wiki](../../wiki) for full architecture documentation, sequence diagrams, and integration walkthroughs.

---

## Compatibility

| Platform | Status |
|----------|--------|
| Claude.ai | Supported |
| Claude Code | Supported |
| Claude (API) | Supported |

Requires `ask_user_input_v0` to be available in the chat context. If the tool is unavailable, the engine stops and displays a clear error message.

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

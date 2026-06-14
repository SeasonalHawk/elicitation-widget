# Architecture

## File structure

```
elicitation-widget/
└── SKILL.md
    ├── Part A  Engine
    │           Step A1  Opening line
    │           Step A2  Single batched ask_user_input_v0 call
    │           Step A3  Payload assembly and handoff
    ├── Part B  Built-in self-test
    │           SELF_TEST_QUESTIONS array (5 questions)
    │           Steps T1 – T4
    └── Part C  Integration guide for skill authors
                Steps 1 – 4, complete example, common mistakes
```

The engine, the self-test, and the integration guide all live in `SKILL.md`. No other files are required. The three parts are independent: Part A runs during normal intake, Part B runs during self-test, and Part C is reference documentation for skill authors.

---

## Design rationale

The conventional approach to skill intake puts the question-asking logic inside each skill. When you build five skills, you write intake five times. When you want to change how intake works, you change it five times. When one gets it wrong, the others are unaffected but inconsistent.

Elicitation Widget moves the intake logic out of individual skills entirely and into a shared engine that skills never touch directly. A skill only has two responsibilities: declare what questions it needs, and tell Claude to hand off to the widget at Step 0. The widget handles everything else: rendering, batching, payload assembly, and error reporting.

This means the engine can be improved independently of every skill that uses it. A skill author can change questions without touching the engine. The engine can be upgraded without touching any skill. The two sides communicate through one shared surface: the format of the QUESTIONS array.

`SKILL.md` serves a dual role in this arrangement. It is both the configuration that describes what the engine should do and the executable instruction set that tells Claude how to carry it out. Installing the file installs both the interface contract and the implementation in a single document. There is no registry, no config file, no setup script. The file is the system.

---

## The contract between skill and widget

```
-- Calling skill declares this --
QUESTIONS = [
  { key, question, type: "single_select", options[] }
]

-- Widget returns this --
[Elicitation complete]
Key 1: Value 1 | Key 2: Value 2 | ... | Key N: Value N
```

The skill defines the QUESTIONS array. The widget reads it. Neither side cares how the other is implemented. Adding or removing questions never requires touching the engine. Improving the engine never requires touching any skill.

---

## Parts of SKILL.md

### Part A: Engine

Contains the critical rules and three execution steps:

- **Step A1:** Claude says the opening line: "Let me gather a few details before we get started."
- **Step A2:** Claude calls `ask_user_input_v0` once, passing the full QUESTIONS array from the calling skill. All questions appear together in one native UI interaction.
- **Step A3:** After the user submits, Claude assembles the key-value payload and hands control back to the calling skill immediately.

### Part B: Self-test

A built-in verification mode triggered by specific phrases. Runs a 5-question live intake using a sample QUESTIONS array baked into the file. Confirms that `ask_user_input_v0` is available and rendering correctly. See [Self-Test](Self-Test) for details.

### Part C: Integration guide

Step-by-step instructions for skill and plugin authors. Includes a complete working example of a minimal skill that uses the widget correctly. See [Integration Walkthrough](Integration-Walkthrough).

---

Copyright 2025-2026 Kenneth Benavides. MIT License.

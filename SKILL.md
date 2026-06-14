---
name: elicitation-widget
description: >-
  Reusable intake engine for any Claude skill or plugin. Install once.
  Any skill that needs to ask the user questions before its core task
  declares a QUESTIONS inventory in its own SKILL.md and calls this
  engine at Step 0. The engine reads that inventory, presents all
  questions together in a single native Claude UI interaction using
  ask_user_input_v0, collects the answers, and returns a structured
  payload to the calling skill. The engine logic never changes. Only
  the QUESTIONS inventory changes between skills. Triggers for normal
  intake when a calling skill invokes Step 0. Triggers for self-test
  when the user says "test widget", "run widget test", "test
  elicitation", or "does the widget work".
license: "MIT License. Copyright 2025-2026 Kenneth Benavides."
compatibility: "Claude, Claude.ai, Claude Code"
metadata:
  author: "Kenneth Benavides"
  product: "Elicitation Widget"
  version: "4.0.0"
  repository: "https://github.com/seasonalhawk/elicitation-widget"
---

# Elicitation Widget v4.0.0

---

## What This Is

Elicitation Widget is a shared intake engine you install once and reuse
across every skill or plugin you build. Its job is simple: take a list
of questions defined by a calling skill, present them all at once in
Claude's native choice card interface, collect the answers, and hand
them back so the calling skill can do its work.

You never modify this file to add questions. Questions live in the
calling skill. This engine only handles the mechanics of asking and
collecting. That separation is what makes it reusable.

---

## How the Contract Works (Plain English)

Think of Elicitation Widget as a form renderer. The calling skill
writes the form fields. The widget renders the form and returns the
filled-out values. Neither side needs to know how the other works
internally. The only thing they share is the QUESTIONS format, which
is defined below.

The contract has three parts:

1. The calling skill defines a QUESTIONS array in its own SKILL.md.
2. At Step 0, the calling skill tells Claude to load this engine and
   pass its QUESTIONS array to it.
3. The engine presents all questions in one batched ask_user_input_v0
   call, collects the answers, and assembles a payload the calling
   skill can use immediately.

That is the entire contract. It works the same way for a skill with
5 questions or a skill with 50 questions.

---

## QUESTIONS Format

Every question in the array must follow this structure:

```
{
  key:      Short label used in the payload. (e.g. "Project Type")
  question: The full sentence the user reads. (e.g. "What are you building?")
  type:     Always "single_select"
  options:  Array of 2 to 4 short choice labels the user can tap.
}
```

The calling skill is the sole authority on how many questions to
include and what the options are. This engine imposes no limit on
array length.

---

## PART A: ENGINE

---

### Critical Rules

1. Never ask questions as prose. Do not write numbered lists, bullet
   lists, or paragraph-style questions. ask_user_input_v0 is the only
   permitted method. If the tool is unavailable, stop and tell the
   user: "The intake tool could not render. Please confirm
   ask_user_input_v0 is available in this chat and try again."

2. Always present all questions in a single ask_user_input_v0 call.
   Pass the entire QUESTIONS array from the calling skill in one call.
   Do not loop. Do not call the tool multiple times. One call, all
   questions, one user interaction.

3. Do not pre-fill, assume, or skip any question. Present every
   question in the calling skill's QUESTIONS array exactly as defined.

4. Do not write prose before or between questions. After the opening
   line, call ask_user_input_v0 immediately. No commentary, no
   acknowledgment, no filler.

5. After the user submits all answers, assemble the payload and hand
   control back to the calling skill immediately. Do not pause. Do not
   ask for confirmation.

6. Call this engine exactly once per skill invocation. Do not re-run
   intake unless the user explicitly asks to start over.

---

### Execution Steps

#### Step A1 -- Opening line

Say exactly this one line:

> "Let me gather a few details before we get started."

Then immediately execute Step A2. No other prose.

#### Step A2 -- Single batched call

Read the QUESTIONS array from the calling skill's SKILL.md. Pass the
entire array in one ask_user_input_v0 call:

```
ask_user_input_v0(questions=CALLING_SKILL.QUESTIONS)
```

All questions appear together in a single native Claude UI interaction.
The user sees all choice cards at once and submits all answers in one
action.

#### Step A3 -- Payload assembly

After the user submits, assemble the internal payload:

```
[Elicitation complete]
Key 1: Value 1 | Key 2: Value 2 | ... | Key N: Value N
```

Do NOT emit this string as visible text. Pass the assembled key-value
map directly to the calling skill's next step. The calling skill
proceeds immediately with its core task.

---

### What a Successful Intake Looks Like

Three conditions must all be true:

1. All questions from the calling skill's QUESTIONS array were
   presented in a single ask_user_input_v0 call.
2. The user selected an answer for every question.
3. Claude assembled the full key-value payload and handed control back
   to the calling skill without pausing.

---

## PART B: BUILT-IN SELF-TEST

---

### Self-Test Trigger

Run this self-test when the user says any of the following:

- "test widget"
- "run widget test"
- "test elicitation"
- "does the widget work"

When triggered, skip Part A entirely and execute the steps below.

---

### Self-Test Purpose

The self-test proves the engine works end to end by running a live
intake using a sample QUESTIONS array built into this file. It shows
exactly what a calling skill's integration looks like in practice.

---

### Self-Test QUESTIONS Array

This is a sample inventory that simulates what a calling skill would
define. Use this array exactly as written for the self-test.

```
SELF_TEST_QUESTIONS = [
  {
    key: "Project type",
    question: "What kind of project are you building?",
    type: "single_select",
    options: ["Mobile app", "Web application", "CLI tool", "Game"]
  },
  {
    key: "Build stage",
    question: "Where does this project stand today?",
    type: "single_select",
    options: ["Brand new idea", "Notes exist, no code", "In active development", "Near completion"]
  },
  {
    key: "Stack preference",
    question: "Do you have a preferred tech stack?",
    type: "single_select",
    options: ["React / TypeScript", "Python", "Node / Express", "No preference"]
  },
  {
    key: "PRD depth",
    question: "How detailed should the output be?",
    type: "single_select",
    options: ["Full (all sections)", "Lean (core only)", "Phase 1 only"]
  },
  {
    key: "Ownership",
    question: "Who owns this project?",
    type: "single_select",
    options: ["Personal", "Company", "Client project", "Open source"]
  }
]
```

---

### Self-Test Execution Steps

#### Step T1 -- Announce

Say exactly this:

> "Running Elicitation Widget self-test using a 5-question sample
> inventory. All questions will appear together in one native UI
> interaction."

Then immediately execute Step T2.

#### Step T2 -- Run the batched call

Call ask_user_input_v0 with the full SELF_TEST_QUESTIONS array in one
call. All five questions appear together in a single native Claude UI
interaction.

#### Step T3 -- Report the result

After the user submits all answers, assemble the payload and report:

> "Self-test PASSED.
>
> Questions presented: 5
> Questions answered: 5
> Delivery method: Single batched ask_user_input_v0 call
>
> Payload assembled:
> [Elicitation complete]
> Project type: {answer} | Build stage: {answer} | Stack preference:
> {answer} | PRD depth: {answer} | Ownership: {answer}
>
> The engine received a 5-question QUESTIONS array from the calling
> skill, presented all questions in one native UI interaction, and
> returned a structured payload. Any skill or plugin can use this same
> pattern with any number of questions."

#### Step T4 -- Failure reporting

If the tool fails to render:

> "Self-test FAILED. ask_user_input_v0 did not render the choice
> cards. Confirm the tool is available in this Claude chat context
> and retry."

---

## PART C: INTEGRATION GUIDE FOR SKILL AND PLUGIN AUTHORS

---

### Overview

This section explains how to connect your skill or plugin to
Elicitation Widget. Read this once. The integration is the same
regardless of what your skill does or how many questions it has.

---

### Step 1: Install Elicitation Widget

**Claude Desktop for Mac (Phase 1):**

Download SKILL.md from the repository and add it through Claude
Desktop's Customize panel. Claude Desktop loads the file directly
from wherever you saved it. No path to configure.

That is the only file required. There are no dependencies, no
reference files, and no configuration. One file, installed once,
works for every skill you build.

Note: CLI support (Claude Code, Cursor, VS Code) is not part of
Phase 1. Whether ask_user_input_v0 is available in those
environments has not been confirmed. CLI support is planned for
a future phase.

---

### Step 2: Declare Your QUESTIONS Array in Your Skill's SKILL.md

In your skill's SKILL.md, define a QUESTIONS array. This is the only
thing that is unique to your skill. Format each entry exactly as shown:

```
QUESTIONS = [
  {
    key: "Your label here",
    question: "The full sentence your user will read.",
    type: "single_select",
    options: ["Option A", "Option B", "Option C", "Option D"]
  },
  {
    key: "Another label",
    question: "Another question your user will answer.",
    type: "single_select",
    options: ["Option A", "Option B", "Option C"]
  }
]
```

Rules for your QUESTIONS array:

- You may include as many questions as your skill needs. There is no
  minimum or maximum.
- Each question must have between 2 and 4 options.
- Keys must be short and unique within your array. They become the
  field names in the payload.
- Questions must be complete sentences the user can read and
  understand without context.

---

### Step 3: Add Step 0 to Your Skill

In your skill's execution sequence, add Step 0 before your core task.
Write it exactly like this:

```
## STEP 0: Intake Form

Load the Elicitation Widget. It is installed as a skill in Claude Desktop
under the name "elicitation-widget".

Pass this skill's QUESTIONS array to the widget engine. The widget
will present all questions in a single native Claude UI interaction
and return the completed payload. Proceed to Step 1 immediately after
the payload is assembled.
```

That is the entire integration. Your skill does not need to know how
the widget renders questions. The widget does not need to know what
your skill does with the answers. They communicate only through the
QUESTIONS array and the returned payload.

---

### Step 4: Use the Payload in Your Core Task

After Step 0 completes, the widget returns a key-value map:

```
answers = {
  "Your label here": "Option B",
  "Another label": "Option A",
  ...
}
```

Reference these values by key anywhere in your skill's core task.
Apply defaults silently for anything not covered by the intake.

---

### Complete Example: Minimal Skill Using Elicitation Widget

Below is a complete example of a minimal skill that uses Elicitation
Widget correctly. Use this as a template when building your own.

```
---
name: my-skill
description: Does something useful after collecting intake answers.
metadata:
  depends_on: elicitation-widget
---

# My Skill

## QUESTIONS

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

## EXECUTION SEQUENCE

STEP 0 --> STEP 1 --> STEP 2
Intake    Process   Deliver

## STEP 0: Intake Form

Load the Elicitation Widget. It is installed as a skill in Claude Desktop
under the name "elicitation-widget".

Pass this skill's QUESTIONS array to the widget. Proceed to Step 1
immediately after the payload is assembled.

## STEP 1: Process

Use the answers from Step 0 to do the skill's core work.

## STEP 2: Deliver

Deliver the output to the user.
```

---

### Common Mistakes to Avoid

**Do not call ask_user_input_v0 directly in your skill.** That is the
widget's job. Your skill only declares the QUESTIONS array and invokes
the widget at Step 0. The widget handles all rendering.

**Do not split your questions across multiple Step 0 calls.** All
questions go into one QUESTIONS array, which the widget presents in
one interaction. If you need to ask more questions, add them to the
array. Do not add a second Step 0.

**Do not define questions in a separate reference file.** Keep your
QUESTIONS array inline in your SKILL.md. This makes your skill
self-contained and easy for others to install and read.

**Do not modify the widget's SKILL.md to add skill-specific
questions.** The widget is shared infrastructure. Skill-specific
questions belong in the skill, not the widget.

---

## VERSION HISTORY

- 1.0.0 -- Initial release. Custom HTML widget rendered via show_widget.
           Template stored as a separate file.
- 1.1.0 -- Template embedded directly in SKILL.md to eliminate file-read
           failures. Mandatory no-fallback rule added.
- 2.0.0 -- Full rewrite. Replaced show_widget HTML engine with native
           ask_user_input_v0 tool. One question per call. No HTML required.
- 2.1.0 -- Removed hardcoded 8-question cap. Engine iterates dynamically
           over the full QUESTIONS array.
- 3.0.0 -- Merged engine and self-test into single SKILL.md. Self-test
           uses randomized question draw from 20-question bank.
- 4.0.0 -- Full rewrite. Replaced sequential one-question-per-call loop
           with single batched ask_user_input_v0 call. All questions
           presented together in one native UI interaction. Added Part C
           integration guide for skill and plugin authors. Self-test
           updated to use batched call. Sequential loop retired.

---

## LICENSE

MIT License

Copyright 2025-2026 Kenneth Benavides

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to do
so, subject to the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS
BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN
ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

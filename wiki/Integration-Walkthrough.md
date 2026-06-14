# Integration Walkthrough

Four steps to wire any Claude skill or plugin to Elicitation Widget. Read this once. The integration is identical regardless of what your skill does or how many questions it has.

---

## Step 1: Install the widget

> Phase 1 targets Claude Desktop for Mac. CLI support is planned for a future phase.

Download `SKILL.md` from this repository. Open **Claude Desktop**, click **Customize**, and add the file. Claude Desktop loads it directly from wherever you saved it on your Mac. No path to configure, no CLI commands, no additional steps.

That is the only file required. No dependencies. No configuration.

---

## Step 2: Declare your QUESTIONS array

In your skill's `SKILL.md`, define a QUESTIONS array inline. Do not put skill-specific questions in the widget's file. Do not define them in a separate reference file. Inline in your skill is the only correct location.

```
QUESTIONS = [
  {
    key:      "Goal",
    question: "What do you want to accomplish?",
    type:     "single_select",
    options:  ["Option A", "Option B", "Option C"]
  },
  {
    key:      "Audience",
    question: "Who is this for?",
    type:     "single_select",
    options:  ["Internal team", "External clients", "Public"]
  }
]
```

See [QUESTIONS Schema](QUESTIONS-Schema) for field definitions and rules.

---

## Step 3: Add Step 0 to your execution sequence

In your skill's execution sequence, add Step 0 before your first real step. Write it exactly as shown:

```
## STEP 0: Intake Form

Load the Elicitation Widget. It is installed as a skill in Claude Desktop
under the name "elicitation-widget".

Pass this skill's QUESTIONS array to the widget engine. The widget
will present all questions in a single native Claude UI interaction
and return the completed payload. Proceed to Step 1 immediately after
the payload is assembled.
```

That is the complete integration instruction. Your skill does not need to know how the widget renders questions. The widget does not need to know what your skill does with the answers.

---

## Step 4: Use the payload in your core task

After Step 0 completes, the widget hands back a key-value map:

```
answers = {
  "Goal":     "Option B",
  "Audience": "External clients"
}
```

Reference values by key anywhere in your skill's subsequent steps. Apply silent defaults for anything not covered by the intake. Never ask the user again for information already in the payload.

---

## Complete minimal example

Below is a full working example of a skill that integrates correctly with Elicitation Widget.

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
    key:      "Goal",
    question: "What do you want to accomplish?",
    type:     "single_select",
    options:  ["Option A", "Option B", "Option C"]
  },
  {
    key:      "Audience",
    question: "Who is this for?",
    type:     "single_select",
    options:  ["Internal team", "External clients", "Public"]
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

Copyright 2025-2026 Kenneth Benavides. MIT License.

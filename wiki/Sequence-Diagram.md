# Sequence Diagram

## Normal intake flow

```
Calling Skill            Elicitation Widget         User
     |                          |                     |
     |-- declares QUESTIONS --> |                     |
     |                          |                     |
     |-- invokes Step 0 ------> |                     |
     |                          |                     |
     |                          |-- choice cards ---> |
     |                          |   (all at once)     |
     |                          |                     |
     |                          | <-- all answers --- |
     |                          |   (one submission)  |
     |                          |                     |
     |                          |-- assembles ----.   |
     |                          |    payload      |   |
     |                          '<----------------'   |
     |                          |                     |
     | <-- payload ------------ |                     |
     |                          |                     |
     |-- proceeds to Step 1 --> |                     |
```

---

## Step-by-step breakdown

### 1. Skill declares QUESTIONS

The calling skill defines a QUESTIONS array in its own `SKILL.md`. This happens at author time, not at runtime. The array is static configuration, not dynamic logic.

### 2. Skill invokes Step 0

At runtime, Claude reads the calling skill's Step 0 instruction and loads the Elicitation Widget. This is the handoff point. From here the widget is in control.

### 3. Widget presents all choice cards

The widget calls `ask_user_input_v0` exactly once, passing the entire QUESTIONS array. All questions appear together in a single native Claude UI interaction. The user sees every question at once and submits all answers in one action.

This is the critical design constraint: one call, all questions, one submission. The widget never loops. It never calls the tool more than once per intake session.

### 4. User submits

The user taps their answers across all choice cards and submits once. No back-and-forth. No sequential questioning.

### 5. Widget assembles payload

After the user submits, the widget collects all answers and assembles a key-value payload:

```
[Elicitation complete]
Key 1: Value 1 | Key 2: Value 2 | ... | Key N: Value N
```

This string is not shown to the user. It is passed internally to the calling skill.

### 6. Calling skill proceeds

The widget hands control back to the calling skill immediately. No pause. No confirmation. The calling skill proceeds to Step 1 with the full payload available as a structured map.

---

## Self-test flow

```
User                    Elicitation Widget
  |                             |
  |-- "test widget" ----------> |
  |                             |-- announces self-test
  |                             |
  |                             |-- presents 5-question
  |                             |   SELF_TEST_QUESTIONS
  | <-- choice cards ---------- |
  |                             |
  |-- submits all answers ----> |
  |                             |
  | <-- PASSED report --------- |
  |     with assembled payload  |
```

See [Self-Test](Self-Test) for full details.

---

Copyright 2025-2026 Kenneth Benavides. MIT License.

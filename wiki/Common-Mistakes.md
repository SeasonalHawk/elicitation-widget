# Common Mistakes

These are the most frequent errors made when integrating a skill with Elicitation Widget. Each one breaks the integration in a specific way.

---

## Calling ask_user_input_v0 directly in your skill

**What happens:** Your skill attempts to handle intake itself instead of delegating to the widget.

**Why it breaks:** The widget's job is to call `ask_user_input_v0`. If your skill also calls it, you have two intake systems running in parallel or sequence, producing inconsistent behavior and duplicate questions.

**Fix:** Remove all `ask_user_input_v0` calls from your skill. Your skill's only intake-related responsibility is declaring the QUESTIONS array and invoking the widget at Step 0. Nothing else.

---

## Splitting intake across multiple Step 0 calls

**What happens:** A skill defines two separate Step 0 blocks, or calls the widget twice with different sets of questions.

**Why it breaks:** The user ends up in two separate intake interactions instead of one. The batched design is deliberate: one call, all questions, one submission. Splitting it defeats that entirely and produces a fragmented experience.

**Fix:** Put all questions into a single QUESTIONS array. The widget has no limit on array length. If you have 10 questions, put all 10 in the array. Do not add a second Step 0.

---

## Defining questions in a separate reference file

**What happens:** A skill's `SKILL.md` references an external file like `questions.json` or `intake.md` for its QUESTIONS array.

**Why it breaks:** The skill is no longer self-contained. Anyone who installs the skill also needs to find and install the reference file. Claude may fail to read the file at runtime. The integration becomes brittle.

**Fix:** Keep your QUESTIONS array inline in your skill's `SKILL.md`. It is a short array. It belongs in the same file as the rest of your skill.

---

## Modifying the widget's SKILL.md to add skill-specific questions

**What happens:** A skill author edits `elicitation-widget/SKILL.md` directly to add their questions.

**Why it breaks:** The widget is shared infrastructure. When you modify it for one skill, every other skill that uses it inherits those questions. This defeats the entire purpose of a shared engine.

**Fix:** Leave the widget's `SKILL.md` untouched. Your skill's QUESTIONS array belongs in your skill's own `SKILL.md`. The widget reads it from there.

---

## Pausing or asking for confirmation after payload assembly

**What happens:** After the user submits answers, Claude pauses to summarize the answers, ask if they are correct, or wait for a signal before continuing.

**Why it breaks:** The widget's critical rules explicitly prohibit this. After Step A3, the widget hands control back to the calling skill immediately. Any pause breaks the flow contract.

**Fix:** Let the widget run its three steps without interruption. The calling skill proceeds to Step 1 the moment the payload is assembled. Trust the user's selections.

---

Copyright 2025-2026 Kenneth Benavides. MIT License.

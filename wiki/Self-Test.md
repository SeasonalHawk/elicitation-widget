# Self-Test

The widget includes a built-in self-test that proves `ask_user_input_v0` is available and rendering correctly in the current Claude context. Run it any time you are unsure whether the engine is working, after installing, or after moving to a new Claude environment.

---

## Trigger phrases

Say any of the following to the Claude instance where the widget is installed:

- `test widget`
- `run widget test`
- `test elicitation`
- `does the widget work`

The self-test skips Part A of the engine entirely. It runs its own dedicated flow using a 5-question sample inventory built into the widget.

---

## What the self-test does

**Step T1 — Announce**

Claude says:

> "Running Elicitation Widget self-test using a 5-question sample inventory. All questions will appear together in one native UI interaction."

**Step T2 — Run the batched call**

Claude calls `ask_user_input_v0` once with the built-in `SELF_TEST_QUESTIONS` array. All five questions appear together in a single interaction.

The sample inventory covers: project type, build stage, stack preference, PRD depth, and ownership. These questions are fixed inside the widget and are only used for testing.

**Step T3 — Report pass**

After you submit all answers, Claude reports:

```
Self-test PASSED.

Questions presented: 5
Questions answered: 5
Delivery method: Single batched ask_user_input_v0 call

Payload assembled:
[Elicitation complete]
Project type: {answer} | Build stage: {answer} | Stack preference: {answer} | PRD depth: {answer} | Ownership: {answer}
```

**Step T4 — Report failure**

If the tool does not render:

```
Self-test FAILED. ask_user_input_v0 did not render the choice cards.
Confirm the tool is available in this Claude chat context and retry.
```

---

## Pass conditions

All three must be true for the self-test to count as passed:

1. All five questions were presented in a single `ask_user_input_v0` call.
2. You selected an answer for every question.
3. Claude assembled and reported the full payload without pausing.

---

## Troubleshooting

| Symptom | Likely cause |
|---------|-------------|
| Self-test FAILED message | `ask_user_input_v0` not available in this Claude context |
| Questions shown as prose instead of choice cards | Wrong Claude context or outdated Claude version |
| Only some questions appeared | `ask_user_input_v0` option count limit; check your options arrays have 2 to 4 entries each |
| Widget ran but did not report results | Claude stopped before Step T3; retry |

---

Copyright 2025-2026 Kenneth Benavides. MIT License.

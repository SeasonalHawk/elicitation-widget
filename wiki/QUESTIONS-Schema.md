# QUESTIONS Schema

## Object structure

Every entry in the QUESTIONS array must follow this structure. The widget reads each field by name. Field names and types are not flexible.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `key` | string | Yes | Short label. Used as the field name in the returned payload. Must be unique within the array. |
| `question` | string | Yes | The full sentence the user reads. Must be self-contained with no assumed context. |
| `type` | string | Yes | Always `"single_select"`. No other values are supported in v4. |
| `options` | array of strings | Yes | Between 2 and 4 short labels the user can tap. Keep each label under 5 words. |

---

## Rules

- Any number of questions is supported. No minimum. No maximum.
- Keys must be unique within the array. Duplicate keys produce ambiguous payloads.
- Questions must be complete, self-contained sentences. Users see only the question text, not any surrounding context from the skill.
- Each question must have between 2 and 4 options. The `ask_user_input_v0` tool enforces this limit.
- The calling skill is the sole authority on what questions to include. The widget presents whatever it receives.

---

## Annotated example

```
QUESTIONS = [
  {
    key:      "Project type",       -- becomes the key in the payload
    question: "What kind of project are you building?",
    type:     "single_select",      -- always this value
    options:  ["Mobile app", "Web application", "CLI tool", "Game"]
                                    -- 2 to 4 options
  },
  {
    key:      "Build stage",
    question: "Where does this project stand today?",
    type:     "single_select",
    options:  ["Brand new idea", "Notes exist, no code", "In active development", "Near completion"]
  },
  {
    key:      "Ownership",
    question: "Who owns this project?",
    type:     "single_select",
    options:  ["Personal", "Company", "Client project", "Open source"]
  }
]
```

---

## Returned payload

After the user submits, the widget assembles a payload keyed by the `key` fields:

```
[Elicitation complete]
Project type: Web application | Build stage: In active development | Ownership: Personal
```

In your skill's core task, reference answers by key:

```
answers = {
  "Project type": "Web application",
  "Build stage":  "In active development",
  "Ownership":    "Personal"
}
```

---

## Key naming guidelines

Keys become field names in the payload. Short, noun-phrase keys work best:

| Good | Avoid |
|------|-------|
| `"Project type"` | `"What type of project is this?"` |
| `"Audience"` | `"Who is the intended audience for this project?"` |
| `"Output format"` | `"Format"` (too vague if you have multiple format questions) |

Keys do not need to be unique across different skills, only within the array of the same skill.

---

Copyright 2025-2026 Kenneth Benavides. MIT License.

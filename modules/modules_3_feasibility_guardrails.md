### **Module 3 — Feasibility & Guardrails**

<!-- Change Log:
- 2025-11-28: Added strict evidence mode and standard warnings for missing and very short sections.
-->

### Module 3 — Evidence & Guardrails

#### Strict Evidence Mode

- If `evidence_mode = "strict"`:
  - Only use information that is actually written in the section text.
  - Do not add outside facts, examples, or guesses.
- If a sentence or bullet does not clearly come from the section:
  - Remove it or rewrite it so it matches the text.
- If there is not enough information to summarize the section:
  - Replace the summary with:
    - `The source text does not provide enough detail to summarize this section in strict evidence mode.`

#### Missing or Empty Section

- If the section text is missing or basically empty:
  - Do not write a summary or key points.
  - Return:
    - `summary_text = ""`
    - `key_points = []`
  - Add this warning:
    - `Section skipped: no usable text was provided.`

#### Very Short Section (< 50 words)

- If the section has text but fewer than 50 words:
  - Allow a short summary, but keep it simple and careful.
  - Add this warning:
    - `Section very short: summary may be incomplete.`

#### General Hallucination Checks (All Modes)

- Do not:
  - Invent new results, experiments, or datasets.
  - Make up numbers, equations, author names, or years.
- If unsure whether something is in the text:
  - Leave it out.

#### What This Module Returns

For each section, pass back to Module 2:
- Whether the section is missing/empty.
- Whether the section is very short.
- Any warning messages (for example:
  - `Section skipped: no usable text was provided.`
  - `Section very short: summary may be incomplete.`
  - `The source text does not provide enough detail to summarize this section in strict evidence mode.`)

Change Log (2025-11-29): Added summary_level modes ("short" vs "detailed") and conditional behavior for how each section is summarized.

Module 2 — Section Loop (Paper → Summaries)

Inputs Required

Normalized section list from Module 1 (ordered)

Full paper text or pre-split section texts

Audience type (expert, mixed, lay)

summary_level setting:

"short" → 1–2 sentence summary per section

"detailed" → short paragraph plus 3–5 bullet points per section

Any section-specific instructions (e.g., skip, emphasize equations, focus on results)

Section Format 

Each section object includes:

Section name (e.g., “Abstract”, “Introduction”, “Method”, “Results”)

Section type tag (intro, method, result, discussion, conclusion, appendix)

Raw section text

Word count

Location (page range or index)

Links to key items (figures, tables, equations) if available

Flags from Module 1 (missing / empty / very short / candidate for chunking)

Section-Summarizing Loop

Use a simple loop to process sections:

for each section in normalized list:
    load section text and metadata
    apply guardrails and summary rules

For each section:

1. Pre-checks

Compute or confirm word count.

If section is missing or empty → mark for warning and pass to Module 3.

If section length < 50 words → mark as “very short” and pass to Module 3.

If section is very long → mark as “needs chunking” (Module 3 handles details).

2. Generate summary based on summary_level

If summary_level = "short":

Produce a compact 1–2 sentence summary.

Focus on the main purpose and most important contribution of the section.

Do not generate a bullet list.

If summary_level = "detailed":

Produce a short paragraph (3–5 sentences) that explains:

the role of the section in the paper,

key methods or arguments, and

the main findings or claims.

Then produce a bullet list of 3–5 key points, such as:

important definitions,

core equations or models (described in words),

critical experimental details,

main quantitative or qualitative results.

3. Evidence and safety hooks

Summaries must only use information found in the section text (or its approved chunks).

If the section does not provide enough information to satisfy the current summary_level, add a note for Module 3 (e.g., “insufficient detail for detailed summary”).

Hand off any missing/empty/short flags to Module 3 so it can attach standardized warnings.

Validation & Feasibility Checks

For each section summary:

Confirm that the summary length matches the selected summary_level:

"short" → 1–2 sentences, no bullets.

"detailed" → short paragraph plus 3–5 bullets.

Check that no claims, results, or equations appear that are not supported by the section text.

Ensure that any sections marked missing or very short have corresponding warning notes ready for Module 3.

Mark sections that required chunking so Module 3 can verify evidence and coverage.

Robustness Rules

Long sections:

Split into smaller text chunks if needed (based on token/word limits).

Summarize each chunk, then merge into a single section-level summary that still respects summary_level.

Ambiguous or combined sections (e.g., “Results and Discussion”):

Treat as a single section but make sure key results and interpretations are both represented in the summary.

Repeated content:

Avoid copying identical text across sections; reuse ideas but keep wording fresh.

Output Format

For each section, output a structured record that includes:

Section name and type

summary_level used ("short" or "detailed")

Section summary text (sentences or paragraph)

Bullet list of 3–5 points only if summary_level = "detailed"

Any flags from guardrails (e.g., missing, very short, chunked)

Notes for downstream modules (e.g., “warning: limited evidence”, “chunked summary combined”)

This structured per-section output is passed to Module 3 (Guardrails) and Module 4 (Rendering & Refinement) for final checks and formatting.

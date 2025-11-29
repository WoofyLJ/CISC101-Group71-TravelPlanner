<!-- Change Log:
- 2025-11-28: Adapted Travel Planner Plan Builder template to Section Loop for the Research Paper Summarizer. Added summary_level modes ("short" vs "detailed") and integrated guardrail calls.
-->

### **Module 2 — Section Loop (Sections → Summaries)**

Module 2 — Section Loop (Sections → Summaries)
---
#### Inputs Required
- **Normalized section list**
  List of target sections to summarize (e.g., Introduction, Methods, Results, Discussion, Conclusion), cleaned and ordered.

- **Section text map**
  Mapping from section name → raw text for that section. Some entries may be missing, empty, or very short.
  
- **Audience profile**
  Intended reader type (e.g., expert, undergraduate CS student, non-technical reader). Controls vocabulary and depth.

- **summary_level**
  Controls how detailed each section summary should be:

  - `summary_level = "short"`  
    - 1–2 sentence compact summary per section.
  
  - `summary_level = "detailed"`  
    - Short paragraph summary **plus** a bullet list of 3–5 key points for each section.

- **evidence_mode**
  Evidence behavior configuration forwarded to Module 3 (e.g., `"normal"` or `"strict"`).

- **Guardrail configuration**
  Thresholds and standard messages from Module 3, including:
  - Minimum word count for a “short” section (e.g., `< 50` words).
  - Standard warnings for missing / empty sections.
  - Standard warnings for very short sections.

---
#### Section Data Format (Internal)

Each section record includes:
- Section name
- Source text (raw)
- Word count estimate
- Flags:
  - `is_missing_or_empty`
  - `is_very_short`
- Guardrail warnings (list, initially empty)
- Draft summary fields:
  - `summary_text`
  - `key_points` (list of bullets; may be empty in `"short"` mode)

---

#### Section Loop
For each section in the **normalized section list**:
1. **Fetch section data**
   - Look up `source_text` from the section text map.
   - Compute approximate word count.
   - Initialize an internal record with:
     - `section_name`
     - `source_text`
     - `summary_text = ""`
     - `key_points = []`
     - `warnings = []`

2. **Call Guardrails (pre-check)**
   - Send `section_name`, `source_text`, `word_count`, and `evidence_mode` to **Module 3 — Guardrails**.
   - Module 3 returns:
     - `is_missing_or_empty`
     - `is_very_short`
     - Any initial warnings (e.g., missing / very short section).

3. **Handle missing or empty sections**

   - If `is_missing_or_empty = true`:
     - Do **not** attempt to generate a summary.
     - Keep:
       - `summary_text = ""`
       - `key_points = []`
     - Attach the standardized warning from Module 3, such as:
       - `"Section skipped: no usable text was provided."`
     - Add this record to the output list and move on to the next section.

4. **Handle very short sections**
   - If `is_very_short = true` (e.g., word count `< 50`):
     - Proceed with summarization, but:
       - Append the standardized warning from Module 3, such as:
         - `"Section very short: summary may be incomplete."`

5. **Generate summary based on `summary_level`**
   When there is usable text:
   - **If `summary_level = "short"`:**
     - Produce a **compact 1–2 sentence summary** that:
       - Captures the main purpose and key idea(s) of the section.
       - Uses vocabulary appropriate for `audience_profile`.
       - Is directly grounded in the section text (no external facts or invented details).
     - Set:
       - `summary_text` = this 1–2 sentence summary.
       - `key_points = []` (no bullet list in short mode).

   - **If `summary_level = "detailed"`:**
     - First, generate a **short paragraph** (about 3–5 sentences) that:
       - Explains what the section is doing (e.g., stating the problem, describing the model, presenting results).
       - Highlights important methods, assumptions, or findings.
       - Adapts explanation depth to `audience_profile`.
     - Then, generate a **bullet list of 3–5 key points**, where each bullet:
       - Describes a specific concept, step, equation, result, or contribution found in the section.
       - Is phrased as a clear, standalone idea.
       - Is directly supported by the source text (no speculation).
     - Set:
       - `summary_text` = the short paragraph.
       - `key_points` = the 3–5 bullets.

   - If `summary_level` is not recognized, default to `"short"` behavior for safety.

6. **Call Guardrails (post-check / evidence pass)**

   - Send `summary_text`, `key_points`, `source_text`, and `evidence_mode` to Module 3 for verification.
   - In **strict evidence mode**, Module 3 may:
     - Remove or adjust claims that are not clearly supported by the source text.
     - Replace the section summary with a standardized message such as:
       - `"The source text does not provide enough detail to summarize this section in strict evidence mode."`
   - Update the section record with:
     - `validated_summary_text`
     - `validated_key_points`
     - Updated `warnings` (including any strict-evidence or hallucination-related messages).

7. **Collect per-section outputs**
   - Save the final record for this section:
     - `section_name`
     - `summary_text` (validated)
     - `key_points` (validated)
     - `warnings`
   - Append it to the overall **section summaries list**.

---

#### Validation & Guardrail Checks

Across all sections, ensure:

- Every entry in the output corresponds to a section in the normalized list.
- Sections with no usable text always carry the standardized “skipped” warning.
- Sections under the word-count threshold carry the standardized “very short” warning.
- When `evidence_mode = "strict"`, summaries contain no claims that cannot be traced to the source text.

If any section fails validation (e.g., empty summary with no warning), rerun guardrails or add an explicit warning before handing off to Module 4.

---

#### Robustness Rules

- **Inconsistent or noisy section text**

  - If the section includes boilerplate or references with little explanatory text, focus on the most informative sentences and keep the summary conservative.

- **Overly technical sections for lay audiences**

  - When `audience_profile` is non-technical, preserve core ideas but avoid symbols and jargon unless necessary. Use the bullet list to unpack complex items.

- **Very long sections**

  - If a section is extremely long, respect any chunking strategy defined by Module 3 or Module 1 (e.g., summarize per chunk, then merge into a single section summary).

- **Unclear section boundaries**

  - If section text appears to contain multiple subtopics, still produce a single coherent summary and bullet set, but note any ambiguity in the wording if needed.

---

#### Output Format

The final output of Module 2 is a structured list of per-section summaries. For each section, include:

- **Section name**
- **Summary text**  
  - 1–2 sentences in `"short"` mode  
  - Short paragraph in `"detailed"` mode
- **Key points**  
  - Empty list in `"short"` mode  
  - 3–5 bullets in `"detailed"` mode
- **Warnings**  
  - Zero or more standardized warning messages (e.g., missing text, very short text, strict-evidence limitations)

This list is passed to **Module 4 — Rendering & Refinement** to be integrated into the paper-level summary, section-by-section table, expert / lay variants, and checks & warnings section.

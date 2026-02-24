# Scoring (V1)

This document defines the scoring behavior used by `scripts/score_run.py`.

---

## Scoring methods

Each prompt includes a `scoring` object with `method` set to one of:

- `exact`
- `contains`
- `regex`
- `json_schema`

---

## exact

There are two `exact` policies:

### 1) strict

Score = 1 if:

- `output_text.strip() == ground_truth.strip()`

Otherwise score = 0.

### 2) canonical numeric (optional)

This mode is intended for prompts where the ground truth is a number (optionally with a unit).

Behavior:

- Parse a numeric value from `ground_truth` and `output_text`
  - Commas are allowed in the numeric text (e.g., `1,234.5`)
- Optionally parse a lightweight “unit” token and normalize it (simple normalization only)
- Compare numeric values using **near-exact matching**:
  - relative tolerance: `0.0`
  - absolute tolerance: `1e-9`

Implications:

- This is effectively **exact numeric equality**, with only a tiny absolute tolerance to avoid floating parsing noise.
- If `ground_truth` includes a unit, the normalized unit must match the output’s normalized unit.
- If `ground_truth` has no unit, unit is ignored.

Special rule:

- If `ground_truth == "NOT_IN_CONTEXT"`, scoring is always `strict` (canonical numeric is not applied).

---

## contains

Score = 1 if `ground_truth` appears as a substring in `output_text`, case-insensitive.

Otherwise score = 0.

---

## regex

Score = 1 if `re.search(pattern, output_text)` matches.

Otherwise score = 0.

---

## json_schema

This method checks whether the model output is valid JSON and conforms to a named schema.

Parsing:

- Strict parse: attempt JSON parse on the raw output
- Lenient parse: if strict fails, strip Markdown code fences and re-parse

Validation:

- Validate the parsed JSON against the named schema

Score:

- Score = 1 if the JSON is schema-valid using the lenient parse result
- Otherwise score = 0

---

## NOT_IN_CONTEXT violation (NIC)

If `ground_truth == "NOT_IN_CONTEXT"`:

- `not_in_context_violation = 1` when output is not exactly `NOT_IN_CONTEXT` (after `.strip()`)
- Otherwise `0`

Aggregate rates are reported as:

- **NIC (global)**: violations / all prompts
- **NIC (cond)**: violations / prompts whose ground truth is `NOT_IN_CONTEXT`
- **NIC k/n**: explicit numerator/denominator for NIC (cond)

---

## Numeric invention proxy

For open-book extraction prompts:

- Extract numeric tokens from the model output
- Extract numeric tokens from the provided context
- If the output contains at least one numeric token not present in the context, set `numeric_invention = 1`
- Otherwise `0`

Notes:

- This is a heuristic proxy (can false-positive and false-negative).
- It is not intended to replace task-specific factuality checks.

---
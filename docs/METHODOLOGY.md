
---

## `docs/METHODOLOGY.md` (drop-in)

```md
# Methodology (V1 / v1_final)

This document explains what V1 measures, how the suite is constructed, and how metrics are computed.

---

## 1) Evaluation design

V1 is a local, deterministic offline evaluation harness designed to be:

- Reproducible (fixed suite + deterministic expansion rules)
- Comparable across models/settings
- Interpretable (scores tied to ground truth and provided context)

V1 primarily targets open-book tasks (use only the provided context).

---

## 2) Prompt suite

### Base suite

Stored as JSONL (one prompt per line) in:

- `data/prompts/v1_suite.jsonl`

### Expanded suite

Generated JSONL in:

- `data/prompts/v1_suite_exp.jsonl`

Expansion types:

1. **Paraphrase expansions**
   - Prepend safe instruction paraphrases while preserving the original prompt verbatim.

2. **Numeric perturbations**
   - For eligible prompts, deterministically modify a numeric fact and update:
     - context
     - ground_truth
     - metadata fact anchor

Guardrails:
- Never perturb prompts where `ground_truth == "NOT_IN_CONTEXT"`.
- For JSON-schema tasks, preserve `schema_name`.
- For exact numeric tasks with units, preserve the unit.

---

## 3) Scoring

Supported scoring methods:

- `exact`
- `contains`
- `regex`
- `json_schema`

For `exact`, V1 can optionally apply **canonical numeric scoring**:
- Parse numeric value + unit and compare canonically
- `NOT_IN_CONTEXT` is always strict

Details: `docs/SCORING.md`

---

## 4) Metrics

### Accuracy
Per run:
- Accuracy = mean(`score`) across prompts (binary 0/1 in V1)

### Uncertainty (95% CI)
- For binary accuracy, use Wilson confidence intervals.

### NIC (NOT_IN_CONTEXT compliance)
Reported two ways:

- **NIC (global)** = mean(`not_in_context_violation`) over all prompts
- **NIC (cond)** = mean(`not_in_context_violation`) over NIC prompts only

Also reported:
- **NIC k/n** to make the conditional denominator explicit.

### Numeric invention proxy
For open-book extraction prompts only:
- numeric invention flag = 1 if output introduces any numeric token not present in context.

---

## 5) Interpretation

- Treat `temperature=0.0` runs as the deterministic capability baseline.
- Use NIC (cond) to judge refusal/anti-hallucination robustness specifically.
- Compare confidence intervals (not just point estimates) when ranking models.

---

## 6) Known limitations

- Numeric invention is a proxy and can false positive.
- Canonical numeric scoring is intentionally lightweight and conservative.
- Multi-turn behavior is out of scope for V1 (planned for V2).

See `docs/ROADMAP.md`.
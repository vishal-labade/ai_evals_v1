# Run Report

- **Run ID:** `dev_rog_smoke_20260223_051100_llama_8b_t0_llama3-8b-instruct-q4_0_821e39941154`
- **Model:** `llama3:8b-instruct-q4_0`
- **N prompts:** 1070
- **Missing scores:** 0

## Overall
- **Accuracy (score mean):** 69.3%
- **Avg latency:** 279 ms
- **JSON valid (strict):** 30.1%
- **JSON valid (lenient):** 30.1%
- **NOT_IN_CONTEXT violation rate:** 2.9%
- **Numeric invention flag rate:** 0.4%

## By task
- date_specific: 98.6% (n=72)
- distractor_robustness: 98.9% (n=185)
- extract_value: 98.3% (n=298)
- json_multi_field: 13.9% (n=144)
- json_one_field: 12.3% (n=154)
- json_strict_schema: 0.0% (n=31)
- not_in_context: 83.3% (n=186)

## By domain
- finance: 70.8% (n=390)
- mixed: 42.3% (n=175)
- ops: 76.3% (n=257)
- product: 78.6% (n=248)

## By difficulty
- easy: 70.6% (n=309)
- hard: 72.3% (n=350)
- medium: 65.7% (n=411)

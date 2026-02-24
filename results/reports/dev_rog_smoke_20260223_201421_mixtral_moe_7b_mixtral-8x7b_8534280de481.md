# Run Report

- **Run ID:** `dev_rog_smoke_20260223_201421_mixtral_moe_7b_mixtral-8x7b_8534280de481`
- **Model:** `mixtral:8x7b`
- **N prompts:** 1070
- **Missing scores:** 0

## Overall
- **Accuracy (score mean):** 62.1%
- **Avg latency:** 2950 ms
- **JSON valid (strict):** 78.4%
- **JSON valid (lenient):** 79.0%
- **NOT_IN_CONTEXT violation rate:** 16.4%
- **Numeric invention flag rate:** 1.2%

## By task
- date_specific: 66.7% (n=72)
- distractor_robustness: 90.3% (n=185)
- extract_value: 87.6% (n=298)
- json_multi_field: 49.3% (n=144)
- json_one_field: 69.5% (n=154)
- json_strict_schema: 0.0% (n=31)
- not_in_context: 5.9% (n=186)

## By domain
- finance: 74.6% (n=390)
- mixed: 30.3% (n=175)
- ops: 73.2% (n=257)
- product: 53.6% (n=248)

## By difficulty
- easy: 63.4% (n=309)
- hard: 67.7% (n=350)
- medium: 56.4% (n=411)

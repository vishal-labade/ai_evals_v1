# Run Report

- **Run ID:** `dev_rog_smoke_20260223_190912_mixtral_moe_7b_t0_mixtral-8x7b_8534280de481`
- **Model:** `mixtral:8x7b`
- **N prompts:** 1070
- **Missing scores:** 0

## Overall
- **Accuracy (score mean):** 62.2%
- **Avg latency:** 2945 ms
- **JSON valid (strict):** 79.0%
- **JSON valid (lenient):** 79.3%
- **NOT_IN_CONTEXT violation rate:** 16.4%
- **Numeric invention flag rate:** 0.7%

## By task
- date_specific: 66.7% (n=72)
- distractor_robustness: 90.3% (n=185)
- extract_value: 87.9% (n=298)
- json_multi_field: 48.6% (n=144)
- json_one_field: 69.5% (n=154)
- json_strict_schema: 3.2% (n=31)
- not_in_context: 5.9% (n=186)

## By domain
- finance: 74.9% (n=390)
- mixed: 30.9% (n=175)
- ops: 73.5% (n=257)
- product: 52.8% (n=248)

## By difficulty
- easy: 63.4% (n=309)
- hard: 68.0% (n=350)
- medium: 56.4% (n=411)

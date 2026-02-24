# Run Report

- **Run ID:** `dev_rog_smoke_20260223_064325_mistral_12b_mistral-nemo-12b-instruct-2407-q4_K_M_821e39941154`
- **Model:** `mistral-nemo:12b-instruct-2407-q4_K_M`
- **N prompts:** 1070
- **Missing scores:** 0

## Overall
- **Accuracy (score mean):** 74.8%
- **Avg latency:** 410 ms
- **JSON valid (strict):** 100.0%
- **JSON valid (lenient):** 100.0%
- **NOT_IN_CONTEXT violation rate:** 0.4%
- **Numeric invention flag rate:** 0.2%

## By task
- date_specific: 93.1% (n=72)
- distractor_robustness: 98.9% (n=185)
- extract_value: 95.3% (n=298)
- json_multi_field: 35.4% (n=144)
- json_one_field: 21.4% (n=154)
- json_strict_schema: 0.0% (n=31)
- not_in_context: 97.8% (n=186)

## By domain
- finance: 82.6% (n=390)
- mixed: 56.0% (n=175)
- ops: 73.9% (n=257)
- product: 76.6% (n=248)

## By difficulty
- easy: 81.2% (n=309)
- hard: 73.4% (n=350)
- medium: 71.0% (n=411)

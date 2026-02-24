# AI Evals V1 Runbook

## What this runbook covers

This is the operational workflow to:

- Verify Ollama connectivity
- Run a batch of models against a prompt suite
- Score runs deterministically
- Produce reports and leaderboards
- Diagnose failures and compare runs

## Prerequisites

- Python 3
- Ollama running locally (default host http://localhost:11434)
- Prompt suite JSONL present at data/prompts/v1_suite_big.jsonl
- Config YAML present, commonly configs/dev_rog.yaml

## 1) Verify Ollama is reachable

Command:

python scripts/verify_ollama.py

Optional:

python scripts/verify_ollama.py --host http://localhost:11434

Expected behavior:

- Prints available models from /api/tags
- Exits non-zero if Ollama is unreachable

## 2) Run a single model (one run JSONL)

This uses configs/dev_rog.yaml and a model block selected by --modelid.

Command:

python scripts/run_model_ollama.py --config configs/dev_rog.yaml --modelid qwen_14b_t0

Output:

- results/runs/<run_id>.jsonl

Notes:

- The run_id is generated from run_name, timestamp, modelid, model string, and config fingerprint.
- If a request fails, the row includes an error field and output_text may be empty.

## 3) Run a batch of models (small, medium, large)

Command:

python scripts/run_models.py --model small
python scripts/run_models.py --model medium
python scripts/run_models.py --model large

Behavior:

- Iterates through predefined model ids and runs scripts/run_model_ollama.py per model id
- Sets OLLAMA_KEEP_ALIVE=0 to avoid models staying loaded between runs

If you add new model ids, update the models map in scripts/run_models.py.

## 4) Score a single run

Command:

python scripts/score_run.py --run results/runs/<run_id>.jsonl --prompts data/prompts/v1_suite_big.jsonl

Optional numeric policy default:

python scripts/score_run.py --run results/runs/<run_id>.jsonl --prompts data/prompts/v1_suite_big.jsonl --default_numeric_policy canonical

Output:

- results/metrics/<run_id>.csv

Behavior notes:

- If a prompt row is a variant and missing task/domain/difficulty/category, score_run.py inherits these fields from base_prompt_id.
- For json_schema prompts:
  - Strict parse validates raw JSON only
  - Lenient parse can strip code fences before parsing
  - Score is 1 only if schema_valid_lenient is 1

## 5) Score all runs in a directory

Command:

python scripts/score_all_runs.py --runs_dir results/runs --pattern "*.jsonl"

Dry run:

python scripts/score_all_runs.py --dry_run

## 6) Create a per-run report

Command:

python scripts/make_report.py --metrics results/metrics/<run_id>.csv

Output:

- results/reports/<run_id>.md

The report includes:

- Mean score (accuracy)
- Avg latency
- JSON validity rates for json_schema prompts
- NOT_IN_CONTEXT violation rate
- Numeric invention flag rate
- Slice breakdowns by task, domain, difficulty

## 7) Create reports for all runs

Command:

python scripts/make_report_all_runs.py --runs_dir results/metrics --pattern "*.csv"

Output:

- results/reports/<run_id>.md per metrics file

## 8) Build a leaderboard

Command:

python scripts/make_leaderboard.py --metrics-glob "results/metrics/*.csv" --out results/reports/leaderboard.md

Notes:

- This script writes a Markdown table.
- If you need a table-free artifact, use the CSV output from add_confidence_intervals.py.

## 9) Add confidence intervals and NIC diagnostics

Command:

python scripts/add_confidence_intervals.py --inputs "results/metrics/*.csv" --out_csv results/leaderboards/overall_ci.csv

Optional Markdown output:

python scripts/add_confidence_intervals.py --inputs "results/metrics/*.csv" --out_csv results/leaderboards/overall_ci.csv --out_md results/leaderboards/overall_ci.md

NIC modes:

- auto (default): uses ground_truth column if present, else task/category heuristics
- gt: uses ground_truth == NOT_IN_CONTEXT
- task_or_category: uses task == not_in_context OR category == anti_hallucination

## 10) Compare two runs

Command:

python scripts/compare_runs.py --a results/metrics/<run_a>.csv --b results/metrics/<run_b>.csv

Output:

- results/reports/compare_<run_a>_vs_<run_b>.md

Notes:

- This report includes per-slice comparisons and prompt-level improved/regressed counts.
- The output currently uses Markdown tables for slice sections.

## 11) Failure breakdown for debugging

Command example:

python scripts/failure_breakdown.py --inputs "results/metrics/*.csv" --out_dir results/reports/failures --top_k 8

Behavior:

- Produces one Markdown report per input CSV
- Extracts lowest-scoring prompts and example responses for NOT_IN_CONTEXT and numeric invention cases

Note:
This script outputs a Markdown table for the lowest-scoring prompts section.

## 12) Common failure modes

Ollama request failures:

- Symptom: error field present in run JSONL, empty output_text
- Action:
  - Verify Ollama host and model name in config
  - Check timeouts and GPU memory
  - Run scripts/verify_ollama.py

Prompt id not found:

- Symptom: metrics row with error prompt_id_not_found_in_suite
- Action:
  - Ensure the prompts file used for scoring matches the prompts used for running
  - Confirm configs/dev_rog.yaml prompts.path

JSON schema failures:

- Symptom: json_valid_lenient=1 but schema_valid_lenient=0
- Action:
  - Confirm the schema_name exists in data/prompts/schemas/index.json
  - Confirm model output matches required schema keys and types

NOT_IN_CONTEXT violations are high:

- Action:
  - Check prompt wording and ensure the model is instructed to output exactly NOT_IN_CONTEXT
  - Consider using temp=0 baseline for capability measurement

Numeric invention is high in open_book_extraction:

- Action:
  - Tighten prompt instruction to not guess
  - Add paraphrase or format variants that reinforce extraction-only behavior

## 13) Operational hygiene

Recommended per run:

- Store the config YAML used for the run alongside run_id in an experiment log
- Keep the run JSONL immutable
- Re-score only by producing a new metrics CSV (do not edit old files)

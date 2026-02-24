# AI Evals V1

Offline evaluation framework for local and open-weight LLMs.

This repo runs models via Ollama, scores outputs with deterministic functions, and produces run reports and leaderboards. It is designed to be reproducible and extensible for V2 behavioral eval.

## What you get

- A prompt suite with ground truth and scoring rules
- Batch model runs with logged generation settings and latency
- Deterministic scoring for:
  - exact match and numeric policies
  - JSON schema compliance
  - simple hallucination proxies
- Reports per run and aggregate leaderboards

## Core workflow

1. Build or select a prompt suite JSONL
2. Run models to produce results/runs/<run_id>.jsonl
3. Score runs to produce results/metrics/<run_id>.csv
4. Generate reports and leaderboards

## Repo layout

- scripts/
  - verify_ollama.py
  - run_model_ollama.py
  - run_models.py
  - score_run.py
  - score_all_runs.py
  - make_report.py
  - make_report_all_runs.py
  - make_leaderboard.py
  - add_confidence_intervals.py
  - compare_runs.py
  - failure_breakdown.py
  - expand_suite_v1.py

- configs/
  - dev_rog.yaml
  - expansions/

- data/prompts/
  - v1_suite_big.jsonl
  - schemas/
    - index.json

- results/
  - runs/
  - metrics/
  - reports/
  - leaderboards/

## Setup

Create a virtual environment:

python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

Make sure Ollama is running:

python scripts/verify_ollama.py

## Running models

Run one model id:

python scripts/run_model_ollama.py --config configs/dev_rog.yaml --modelid qwen_14b_t0

Run a batch group:

python scripts/run_models.py --model small

Outputs:
- results/runs/<run_id>.jsonl

## Scoring

Score one run:

python scripts/score_run.py --run results/runs/<run_id>.jsonl --prompts data/prompts/v1_suite_big.jsonl

Score all runs:

python scripts/score_all_runs.py --runs_dir results/runs --pattern "*.jsonl"

Outputs:
- results/metrics/<run_id>.csv

## Reporting

Per-run report:

python scripts/make_report.py --metrics results/metrics/<run_id>.csv

Reports for all runs:

python scripts/make_report_all_runs.py --runs_dir results/metrics --pattern "*.csv"

Outputs:
- results/reports/<run_id>.md

## Leaderboards and uncertainty

Basic leaderboard:

python scripts/make_leaderboard.py --metrics-glob "results/metrics/*.csv" --out results/reports/leaderboard.md

Confidence intervals and NIC diagnostics:

python scripts/add_confidence_intervals.py --inputs "results/metrics/*.csv" --out_csv results/leaderboards/overall_ci.csv

## Prompt suite expansion

You have two expansion scripts:

- scripts/expand_suite_v1.py
  - single-file version with paraphrase, format, and numeric expansions plus guardrails

- scripts/expand_suite_v1.bk.py
  - modular version that uses ai_eval.expand.* transforms and YAML registries

Key guardrails implemented:

- Unique prompt_id enforcement
- NOT_IN_CONTEXT prompts are never perturbed
- json_schema schema_name is never changed in numeric expansions
- Units are preserved in numeric exact expansions

## Metrics you are tracking in V1

Accuracy:

- Mean of score over prompts (binary 0/1 in current V1)

Hallucination proxies:

- NOT_IN_CONTEXT violation rate
- Numeric invention flag rate for open_book_extraction

Schema compliance:

- JSON strict and lenient validity rates
- JSON schema strict and lenient validity rates

## Notes on determinism

- Use temp=0 (or your *_t0 model variants) as the capability baseline.
- Keep run outputs immutable. If you change scoring, generate new metrics files.
- Prefer storing config fingerprints and git commit hash per run for auditability.

## License

MIT

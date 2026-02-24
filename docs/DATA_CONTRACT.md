# AI Evals V1 Data Contract

## Scope

This contract defines the on-disk interfaces for AI Evals V1:

- Prompt suite files in JSONL
- Model run outputs in JSONL
- Scored metrics in CSV
- Reports and leaderboards in Markdown/CSV

The goal is reproducible, deterministic evaluation runs with stable schemas across time.

## Versioning

Contract versioning is semantic:

- Major: breaking field rename/removal or meaning change
- Minor: backward-compatible field additions
- Patch: documentation or validation-only changes

Recommended field:
- contract_version: string (e.g. v1.0.0) stored in run config and optionally embedded in output rows

## A. Prompt Suite JSONL

File example:
- data/prompts/v1_suite_big.jsonl

Each line is a JSON object.

Required fields:

- prompt_id: string
- prompt: string
- context: string
- ground_truth: string or object
- scoring: object

Recommended fields (used by reports and slicing):

- task: string
- domain: string
- difficulty: string
- category: string
- suite: string
- suite_version: string
- metadata: object

Scoring object:

- scoring.method: string
  - exact
  - contains
  - regex
  - json_schema
- scoring.schema_name: string (required if method is json_schema)
- scoring.pattern: string (required if method is regex)

Special semantics:

- NOT_IN_CONTEXT prompts
  - If ground_truth is the literal string NOT_IN_CONTEXT, scoring.method must be exact.
  - The correct model output must be exactly NOT_IN_CONTEXT.
  - These prompts are used to compute NOT_IN_CONTEXT violation metrics.

Numeric policy semantics (optional):

- prompt.metadata.numeric_policy may be:
  - strict: string equality only
  - canonical: canonical numeric match with optional unit checking
- If numeric_policy is missing, the scorer default policy is used.
- NOT_IN_CONTEXT is always strict, regardless of policy.

Expansion semantics (prompt variants):

Expansion scripts may add:

- base_prompt_id: string
- expansion_type: string
  - original
  - paraphrase
  - format
  - numeric
- variant_index: integer

If a row has base_prompt_id, it may inherit missing slice fields from its base prompt during scoring.

## B. Run Output JSONL

Produced by:
- scripts/run_model_ollama.py
- scripts/run_model_ollama_bk.py

Directory:
- results/runs/

Each line is a JSON object representing one prompt execution.

Required fields:

- run_id: string
- model_name: string
- params: object
- prompt_id: string
- input_text: string
- output_text: string
- latency_ms: integer
- usage: object
- ts_utc: string (ISO timestamp)

Optional fields:

- error: string

params object:

- Must include the generation settings used for the run.
- Typical keys include:
  - temperature
  - num_predict
  - num_ctx
- params is logged verbatim from the config options passed to Ollama.

usage object:

- prompt_tokens: integer or null
- completion_tokens: integer or null
- total_tokens: integer or null

Constraints and invariants:

- One run file contains a single run_id.
- prompt_id must exist in the prompt suite used for that run.
- output_text must be preserved exactly as returned by the model.
- error, if present, means the call failed and output_text may be empty.

Run ID formation (current behavior):

- run_id is derived from run_name, UTC timestamp, modelid or model string, and a fingerprint of the config.
- This ensures that changes to config produce distinct run ids.

## C. Scored Metrics CSV

Produced by:
- scripts/score_run.py
- scripts/score_all_runs.py

Directory:
- results/metrics/

One row per prompt_id execution from the run JSONL.

Columns emitted by score_run.py:

- run_id
- model_name
- prompt_id
- task
- domain
- difficulty
- category
- scoring_method
- schema_name
- score
- json_valid_strict
- json_valid_lenient
- schema_valid_strict
- schema_valid_lenient
- latency_ms
- prompt_tokens
- completion_tokens
- total_tokens
- error
- not_in_context_violation
- numeric_invention_flag
- temperature
- num_predict
- num_ctx

Column semantics:

- score
  - integer 0 or 1 in V1 for current scoring methods
  - exact: exact match (with optional numeric canonical policy)
  - contains: substring match of ground truth inside output_text
  - regex: regex match against output_text
  - json_schema: schema_valid_lenient is used as the binary score

- json_valid_strict, json_valid_lenient
  - only meaningful when scoring_method is json_schema
  - strict: raw json.loads(output_text)
  - lenient: allows stripping code fences before parsing

- schema_valid_strict, schema_valid_lenient
  - only meaningful when scoring_method is json_schema
  - computed via jsonschema validation against scoring.schema_name

- not_in_context_violation
  - 1 if the prompt ground_truth is NOT_IN_CONTEXT and the model output_text is not exactly NOT_IN_CONTEXT
  - 0 otherwise

- numeric_invention_flag
  - proxy used for open_book_extraction prompts
  - 1 if model output contains at least one number not present in the provided context
  - 0 otherwise

Missing data policy:

- score is always emitted as an integer. If scoring errors occur, score is set to 0 and error is tagged.
- Token counts may be null if the backend does not return usage.

## D. Reports and Leaderboards

Per-run report:

- scripts/make_report.py writes:
  - results/reports/<run_id>.md

Leaderboards:

- scripts/make_leaderboard.py writes:
  - results/reports/leaderboard.md

- scripts/add_confidence_intervals.py writes:
  - results/leaderboards/*.csv and optionally *.md

Note:
Some leaderboard scripts output Markdown tables by design. This contract does not require tables, but it permits them as artifacts.

## E. Directory Contract

Required directories:

- configs/
- data/prompts/
- results/runs/
- results/metrics/
- results/reports/
- results/leaderboards/

All scripts must create output directories if missing.

# Reproducibility

V1 is designed to be reproducible across machines.

## Determinism controls
- Suite expansion uses a fixed seed (e.g., `--seed 1337`)
- Numeric perturbations are deterministic and guardrailed
- Scoring is deterministic and does not call external APIs

## Record environment
Recommended to record:
- OS / hardware
- Python version
- Ollama version
- Exact model tags used

## What can vary
- Latency (hardware/load)
- Some models emit Markdown fences for JSON (handled via lenient parsing)
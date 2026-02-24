# Leaderboard by model + temperature

| Model | Temp | Runs | Mean Acc | Std | Min | Max | Avg latency | NOT_IN_CONTEXT viol | Numeric invent | Missing scores |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| `qwen2.5:14b` | 0.0 | 1 | 89.5% | 0.0% | 89.5% | 89.5% | 432 ms | 0.8% | 0.0% | 0 |
| `qwen2.5:3b` | 0.0 | 1 | 89.3% | 0.0% | 89.3% | 89.3% | 183 ms | 0.0% | 0.0% | 0 |
| `qwen2.5:3b` | 0.2 | 1 | 89.3% | 0.0% | 89.3% | 89.3% | 185 ms | 0.0% | 0.0% | 0 |
| `qwen2.5:14b` | 0.2 | 1 | 89.0% | 0.0% | 89.0% | 89.0% | 428 ms | 0.8% | 0.0% | 0 |
| `gemma2:9b-instruct-q4_K_M` | 0.2 | 1 | 83.6% | 0.0% | 83.6% | 83.6% | 411 ms | 0.0% | 0.0% | 0 |
| `gemma2:9b-instruct-q4_K_M` | 0.0 | 1 | 83.5% | 0.0% | 83.5% | 83.5% | 413 ms | 0.0% | 0.0% | 0 |
| `phi3:mini` | 0.0 | 1 | 77.2% | 0.0% | 77.2% | 77.2% | 196 ms | 3.8% | 0.1% | 0 |
| `mistral-nemo:12b-instruct-2407-q4_K_M` | 0.0 | 1 | 75.2% | 0.0% | 75.2% | 75.2% | 406 ms | 0.3% | 0.1% | 0 |
| `phi3:mini` | 0.2 | 1 | 74.8% | 0.0% | 74.8% | 74.8% | 193 ms | 4.6% | 0.1% | 0 |
| `mistral-nemo:12b-instruct-2407-q4_K_M` | 0.2 | 1 | 74.8% | 0.0% | 74.8% | 74.8% | 409 ms | 0.4% | 0.2% | 0 |
| `gpt-oss-safeguard:20b` | 0.2 | 1 | 70.7% | 0.0% | 70.7% | 70.7% | 1384 ms | 2.7% | 0.0% | 0 |
| `gpt-oss-safeguard:20b` | 0.0 | 1 | 70.1% | 0.0% | 70.1% | 70.1% | 1370 ms | 2.5% | 0.0% | 0 |
| `llama3:8b-instruct-q4_0` | 0.2 | 1 | 69.3% | 0.0% | 69.3% | 69.3% | 279 ms | 2.8% | 0.6% | 0 |
| `llama3:8b-instruct-q4_0` | 0.0 | 1 | 69.3% | 0.0% | 69.3% | 69.3% | 279 ms | 2.9% | 0.4% | 0 |
| `mixtral:8x7b` | 0.0 | 1 | 62.2% | 0.0% | 62.2% | 62.2% | 2945 ms | 16.4% | 0.7% | 0 |
| `mixtral:8x7b` | 0.2 | 1 | 62.1% | 0.0% | 62.1% | 62.1% | 2949 ms | 16.4% | 1.2% | 0 |

## Interpretation
- Mean Acc is computed over **present (non-missing) prompt scores** within each run.
- If you see Missing scores > 0, fix upstream scoring to always emit score or log score_error.
- Treat **Temp=0** as the deterministic capability baseline.

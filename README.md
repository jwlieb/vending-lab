# Vending Lab

Research project by Jackson Lieb and Aiden Vierra exploring whether verified
context compression can reduce an agent's token use while preserving business
performance and reliability in Prosus Vending Bench.

The benchmark gives an agent €1,500 to operate six simulated vending machines
for 30 days. Completed runs score their nonnegative final bank balance;
unfinished or bankrupt runs score zero. API costs are measured separately.

Start with the [original benchmark README](https://github.com/ProsusAI/vending-bench/blob/f9a1d7efde193de2242ed5afb1f3a326e362c75d/README.md)
for the world, challenge, and baseline results, then the
[benchmark guide](docs/benchmark-guide.md) for detailed rules and tools.

Current status: project setup and scripted smoke test complete; context
compression and model evaluation are planned.

## Setup and smoke test

Requires [uv](https://docs.astral.sh/uv/getting-started/installation/).
The project pins Python 3.12 and dependencies in `uv.lock`.
`uv sync` installs the required Python if needed and creates a local `.venv`.

```bash
git clone https://github.com/jwlieb/vending-lab.git
cd vending-lab
uv sync --locked
uv run --locked python scripts/local_run.py --seed 101
```

The scripted reference needs no API key or Docker and has privileged catalogue
knowledge. It checks the simulator; it is not an LLM baseline.
Seed 101 completes 30 days with a final bank balance of €2,947.43.

## First model run

Copy `.env.example` to `.env` (`cp .env.example .env`) and set
`OPENROUTER_API_KEY` using your funded OpenRouter account. Keep `.env` local.
Replace `MODEL_ID` below with a tool-capable OpenRouter model ID after checking
availability and pricing.

```bash
uv run --locked python scripts/run_openrouter.py \
  --env-file .env --models MODEL_ID \
  --days 30 --seeds 101 --workers 1 \
  --total-budget 6 --timeout 3600 --max-turns 0 \
  --output results/pilot-101
```

This allows up to $6 and one hour, with no turn-count ceiling. Limits do not
guarantee completion. Use a new output directory for every attempt. Results
include scores, costs, usage, conversations, and simulator trajectories.

## Development

```bash
uv run --locked python -m pytest tests/ -q
uv run --locked python scripts/sync_config.py --check
```

See the [benchmark guide](docs/benchmark-guide.md) for tools, scoring, and
configuration, and [existing results](results/README.md) for upstream runs.
Historical v2 model results are separate from the current v3 economy.

## Attribution and license

Forked from [ProsusAI/vending-bench](https://github.com/ProsusAI/vending-bench)
at `f9a1d7efde193de2242ed5afb1f3a326e362c75d` (tag `baseline-20260922`).
This README was modified for Vending Lab by Jackson Lieb and Aiden Vierra in 2026.

[Apache 2.0](LICENSE) · Original benchmark copyright © 2026 MIH AI B.V.
The benchmark uses [Harbor](https://github.com/harbor-framework/harbor)'s MCP
sidecar pattern. Retained attribution is in [NOTICE](NOTICE); citation details
are in [CITATION.cff](CITATION.cff).

Please exclude task instructions, reference solutions, trajectories, and results
from model training corpora. This request does not change the Apache 2.0 license.

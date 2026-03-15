# polymarket-bot — pyenv Virtualenv Guide

All environments use **Python 3.12** managed via `pyenv` + `pyenv-virtualenv`.

## Prerequisites

```bash
# Install pyenv (if not already)
brew install pyenv pyenv-virtualenv

# Add to ~/.zshrc or ~/.zprofile
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"

# Install Python 3.12
pyenv install 3.12
```

## Environment Index

| Virtualenv Name              | Directory     | Purpose                              |
|-----------------------------|---------------|--------------------------------------|
| [polybot-master-3.12](master.md)    | root          | Unified env (attempt first)          |
| [polybot-adapters-3.12](adapters.md)   | adapters/     | Data bridge / v2→v3 conversion       |
| [polybot-backtest-3.12](backtest.md)   | backtest/     | Simulation & historical analysis     |
| [polybot-contracts-3.12](contracts.md)  | contracts/    | Model interface contracts            |
| [polybot-data-3.12](data.md)       | data/         | All live data feeds & websockets     |
| [polybot-diagnostics-3.12](diagnostics.md)| diagnostics/  | Profiling, KPI, circuit breaker logs |
| [polybot-execution-3.12](execution.md)  | execution/    | Order management & strategy routing  |
| [polybot-graph-3.12](graph.md)      | graph/        | Knowledge graph + Memgraph           |
| [polybot-infra-3.12](infra.md)      | infra/        | Config, secrets, FastAPI dashboard   |
| [polybot-risk-3.12](risk.md)       | risk/         | CVaR, circuit breaker, portfolio     |
| [polybot-signals-3.12](signals.md)    | signals/      | ML signal generation & calibration   |
| [polybot-tests-3.12](tests.md)      | tests/        | Full test suite                      |
| [polybot-training-3.12](training.md)   | training/     | Feature engineering & model training |

## Strategy

Start with the **master env**. If you hit dependency conflicts (most likely between `web3`/`eth-account` and numpy ABI pinning), fall back to per-module envs. The modules most likely to need isolation are:

- `data/` — `web3`, `eth-account`, `py-clob-client` vs ML numpy
- `graph/` — `neo4j` Bolt driver is an unusual dep
- `dashboard/` — Next.js (no Python env needed, uses `npm`)

## Setting a Local pyenv Version

Inside each module directory you can pin the env automatically:

```bash
cd polymarket-bot/signals
pyenv local polybot-signals-3.12
# This writes a .python-version file — pyenv shims pick it up automatically
```

## Shim Resolution Order

```
PYENV_VERSION env var
→ .python-version file (nearest parent)
→ ~/.pyenv/version (global)
```

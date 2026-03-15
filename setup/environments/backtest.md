# polybot-backtest-3.12

**Python:** 3.12
**Directory:** `backtest/`
**Files:** `__init__.py`, `analyzer.py`, `simulator.py`, `historical_data.py`, `data/`
**Purpose:** Historical simulation, P&L analysis, and strategy validation against recorded market data.

## Dependencies

```
numpy>=1.26.0
pandas>=2.1.0
scipy>=1.12.0
scikit-learn>=1.3.0
statsmodels>=0.14.0
pyarrow>=15.0.0
rich>=13.0.0
loguru>=0.7.0
python-dotenv>=1.0.0
```

## Create

```bash
pyenv virtualenv 3.12 polybot-backtest-3.12
pyenv activate polybot-backtest-3.12
pip install --upgrade pip
pip install \
  "numpy>=1.26.0" \
  "pandas>=2.1.0" \
  "scipy>=1.12.0" \
  "scikit-learn>=1.3.0" \
  "statsmodels>=0.14.0" \
  "pyarrow>=15.0.0" \
  "rich>=13.0.0" \
  "loguru>=0.7.0" \
  "python-dotenv>=1.0.0"
```

## Pin to Directory

```bash
cd polymarket-bot/backtest
pyenv local polybot-backtest-3.12
```

## Notes

- Shares the same scientific stack as `signals/` and `training/`. Could share an env with either.
- `data/backtest/` contains historical Polymarket price snapshots used by the simulator.
- Does **not** need `web3` or `py-clob-client` — runs entirely offline on stored data.
- The CPCV (Combinatorial Purged Cross-Validation) logic in `analyzer.py` needs `scikit-learn`.

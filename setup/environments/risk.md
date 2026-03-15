# polybot-risk-3.12

**Python:** 3.12
**Directory:** `risk/`
**Purpose:** Position sizing, CVaR allocation, portfolio-level risk tracking, circuit breaker logic, regime-based budget scaling, and anti-copy-trading detection.

## Key Files

| File | Purpose |
|---|---|
| `cvar_allocator.py` | CVaR-based position sizing |
| `circuit_breaker.py` | Hard stop-loss circuit breaker |
| `portfolio.py` | Portfolio state tracker |
| `metrics.py` | Risk metrics (VaR, CVaR, drawdown, Sharpe) |
| `regime_budget.py` | Regime-conditional risk budget scaling |
| `anti_copytrading.py` | Detects & avoids copy-trading cascades |

## Dependencies

```
numpy>=1.26.0
pandas>=2.1.0
scipy>=1.12.0
statsmodels>=0.14.0
rich>=13.0.0
loguru>=0.7.0
python-dotenv>=1.0.0
pyyaml>=6.0
```

## Create

```bash
pyenv virtualenv 3.12 polybot-risk-3.12
pyenv activate polybot-risk-3.12
pip install --upgrade pip
pip install \
  "numpy>=1.26.0" \
  "pandas>=2.1.0" \
  "scipy>=1.12.0" \
  "statsmodels>=0.14.0" \
  "rich>=13.0.0" \
  "loguru>=0.7.0" \
  "python-dotenv>=1.0.0" \
  "pyyaml>=6.0"
```

## Pin to Directory

```bash
cd polymarket-bot/risk
pyenv local polybot-risk-3.12
```

## Notes

- No web3, no ML inference needed at runtime — risk module receives numeric position data only.
- `statsmodels` adds significant install weight; if CVaR uses a custom implementation, it can be dropped.
- `circuit_breaker.py` writes to `diagnostics/circuit_breaker_log.csv` — path must be accessible from runtime working directory.
- `regime_budget.py` may import regime classifiers from `signals/` — ensure signal output is serialized (JSON/pickle) rather than direct import to maintain env isolation.

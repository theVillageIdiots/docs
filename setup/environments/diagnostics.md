# polybot-diagnostics-3.12

**Python:** 3.12
**Directory:** `diagnostics/`
**Purpose:** Runtime profiling, KPI report generation, circuit breaker log analysis, and model diagnostic evaluation.

## Key Files

| File | Purpose |
|---|---|
| `run_diagnostic.py` | Full system diagnostic runner |
| `generate_kpi_report.py` | KPI report generator (outputs markdown) |
| `profile_status_latency.py` | Latency profiler for status endpoints |

## Dependencies

```
numpy>=1.26.0
pandas>=2.1.0
scikit-learn>=1.3.0
rich>=13.0.0
loguru>=0.7.0
pyarrow>=15.0.0
python-dotenv>=1.0.0
pyyaml>=6.0
```

## Create

```bash
pyenv virtualenv 3.12 polybot-diagnostics-3.12
pyenv activate polybot-diagnostics-3.12
pip install --upgrade pip
pip install \
  "numpy>=1.26.0" \
  "pandas>=2.1.0" \
  "scikit-learn>=1.3.0" \
  "rich>=13.0.0" \
  "loguru>=0.7.0" \
  "pyarrow>=15.0.0" \
  "python-dotenv>=1.0.0" \
  "pyyaml>=6.0"
```

## Pin to Directory

```bash
cd polymarket-bot/diagnostics
pyenv local polybot-diagnostics-3.12
```

## Notes

- This env can share with `backtest/` — identical dependency footprint.
- Output files (`bsnipe_evaluation_log.csv`, `circuit_breaker_log.csv`, `cpcv_results.json`) are read/written by pandas — no special deps needed.
- `generate_kpi_report.py` writes to `docs/reports/` — run from the `polymarket-bot` root.

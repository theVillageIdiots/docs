# polybot-signals-3.12

**Python:** 3.12
**Directory:** `signals/`
**Purpose:** ML-based signal generation pipeline — XGBoost inference, conformal prediction gating, drift detection, fair value estimation, oracle timing, snipe timing, and graph-enriched signals.

## Key Files

| File | Purpose |
|---|---|
| `ml_model.py` | XGBoost model loader & inference |
| `fair_value.py` | Fair value estimation from features |
| `fair_value_edge.py` | Edge sizing from fair value delta |
| `fast_signals.py` | Low-latency synchronous signals |
| `conformal_gate.py` | Conformal prediction coverage gate |
| `drift_detector.py` | Feature distribution drift (PSI/KS) |
| `calibration_monitor.py` | Model calibration tracker |
| `oracle_timing.py` | Oracle update lag signal |
| `snipe_timing.py` | Resolution snipe timing signal |
| `graph_signals.py` | Knowledge graph-derived signals |

## Dependencies

```
numpy>=1.26.0
pandas>=2.1.0
scikit-learn>=1.3.0
xgboost>=2.0.0
scipy>=1.12.0
statsmodels>=0.14.0
ta>=0.11.0
pyarrow>=15.0.0
networkx>=3.2
rich>=13.0.0
loguru>=0.7.0
python-dotenv>=1.0.0
pyyaml>=6.0
```

## Create

```bash
pyenv virtualenv 3.12 polybot-signals-3.12
pyenv activate polybot-signals-3.12
pip install --upgrade pip wheel
pip install \
  "numpy>=1.26.0" \
  "pandas>=2.1.0" \
  "scikit-learn>=1.3.0" \
  "xgboost>=2.0.0" \
  "scipy>=1.12.0" \
  "statsmodels>=0.14.0" \
  "ta>=0.11.0" \
  "pyarrow>=15.0.0" \
  "networkx>=3.2" \
  "rich>=13.0.0" \
  "loguru>=0.7.0" \
  "python-dotenv>=1.0.0" \
  "pyyaml>=6.0"
```

## Pin to Directory

```bash
cd polymarket-bot/signals
pyenv local polybot-signals-3.12
```

## Model Files

Models are loaded from `../models/`. Default production model:
```
models/xgb_v2_production.pkl
```

Ensure the `models/` directory is on `PYTHONPATH` or pass the absolute path when loading.

## Notes

- `ta` (technical analysis) wraps `pandas` Series — pinned to the same numpy as pandas.
- `xgboost >= 2.0` changed its `Booster.load_model()` API; the `.pkl` files in `models/` use joblib serialization via scikit-learn's `Pipeline` wrapper — compatible.
- `conformal_gate.py` uses RAPS (Regularized Adaptive Prediction Sets) implemented via `scipy` — no external conformal library needed.
- `graph_signals.py` imports `networkx` — if fully decoupled from live Memgraph, this env needs no `neo4j` driver.

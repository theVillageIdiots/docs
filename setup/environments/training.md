# polybot-training-3.12

**Python:** 3.12
**Directory:** `training/`
**Purpose:** Feature engineering, data collection, model training, hyperparameter sweeping, symbolic distillation, and QA framework for all predictive models.

## Key Files

| File | Purpose |
|---|---|
| `train.py` | Main training entrypoint |
| `train_fair_value.py` | Fair value model training |
| `train_production.py` | Production model training pipeline |
| `feature_builder.py` | Feature matrix construction |
| `feature_engineering.py` | Raw → derived feature transforms |
| `data_collector.py` | Historical data ingestion |
| `rich_collector.py` | Rich-format data collector |
| `backfill_poly_prices.py` | Backfills Polymarket price history |
| `label_generator.py` | Training label generation |
| `hyperparam_sweep.py` | Optuna/random hyperparameter sweep |
| `sweep_v2_refined.py` | Refined sweep v2 |
| `monte_carlo.py` | Monte Carlo simulation for labels |
| `diagnostic_v2.py` | Training diagnostic v2 |
| `phase3_diagnostic.py` | Phase 3 model diagnostic |
| `qa_framework.py` | QA framework for model validation |
| `symbolic_analysis.py` | Symbolic regression analysis |
| `symbolic_distill.py` | Symbolic distillation (PySR) |
| `symtorch_distill.py` | SymTorch distillation |

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
aiohttp>=3.9.0
requests>=2.31.0
rich>=13.0.0
loguru>=0.7.0
python-dotenv>=1.0.0
pyyaml>=6.0
```

Optional (symbolic distillation):
```
pysr>=0.18.0       # symbolic_distill.py — requires Julia backend
torch>=2.1.0       # symtorch_distill.py
```

## Create

```bash
pyenv virtualenv 3.12 polybot-training-3.12
pyenv activate polybot-training-3.12
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
  "aiohttp>=3.9.0" \
  "requests>=2.31.0" \
  "rich>=13.0.0" \
  "loguru>=0.7.0" \
  "python-dotenv>=1.0.0" \
  "pyyaml>=6.0"

# Optional: symbolic distillation (heavy — adds Julia + PyTorch)
pip install "pysr>=0.18.0"
pip install "torch>=2.1.0"
```

## Pin to Directory

```bash
cd polymarket-bot/training
pyenv local polybot-training-3.12
```

## Notes

- `pysr` requires a Julia installation (`julia` on PATH). Install via `juliaup`: `curl -fsSL https://install.julialang.org | sh`. Then `julia -e 'using Pkg; Pkg.add("SymbolicRegression")'`.
- `torch` for `symtorch_distill.py` adds ~2GB to the env — install only if actively using symbolic distillation.
- Trained models are written to `../models/` — run training scripts from the `polymarket-bot` root or set `MODEL_DIR` env var.
- `SIGNAL_RESEARCH.md` in this directory documents the signal research backlog and feature hypotheses.

## Clash Warning: pysr + xgboost

`pysr` pins specific numpy versions depending on its own build. If `xgboost` and `pysr` conflict on numpy ABI:
1. Install without `pysr` first, verify xgboost works.
2. Install `pysr` last — let pip resolve numpy.
3. If still broken, isolate `pysr` into its own env (`polybot-pysr-3.12`) and run symbolic distillation separately.

# Roadmap

## Current Deployment State

| Component | Status |
|-----------|--------|
| Path A — Heuristic model | ✅ Production |
| Path B — Snipe mode | ✅ Active |
| Path C — Sum-arb | ✅ Active |
| ML model (XGBoost) | ❌ Failed QA gate |
| ML model (Stacking ensemble) | ❌ SIGSEGV/timeout |
| Knowledge graph (Memgraph) | ✅ Active |
| Dashboard (Next.js) | ✅ Active |

## Phase Overview

| Phase | Name | Entry Gate | Goal |
|-------|------|------------|------|
| 0 | Stabilization & Leakage Prevention | None | Clean data pipeline, sub-second Polymarket ingestion |
| 1 | Structural Alpha Activation | Phase 0 complete | Activate Path B divergence trigger, harden Path C |
| 2 | Baseline Path A Rebuild | ≥ 1,500 unique v2 windows | Regularized XGBoost baseline, calibrated, with abstain gate |
| 3 | Advanced Labeling & Validation Hardening | ≥ 2,500 unique v2 windows | CPCV, triple-barrier labeling, delayed-lag sentiment features |
| 4 | Staged 3-Graph Augmentation | Phase 3 exit criteria | Cross-asset GNN, participant temporal graph, event KG |
| 5 | Execution/Risk Framework & Deployment | Phase 4 exit criteria | Full Kelly sizing, live capital deployment |

## Locked Decisions (as of v1.1)

1. Deployment mode: `paper-first`
2. Primary Path A go/no-go metric: `ROI only` (net after fees/slippage)
3. Graph rollout mode: `staged 3-graph rollout`

## Sample Size Gates

| Phase | Minimum unique v2 windows | Approximate collection time |
|-------|--------------------------|----------------------------|
| Phase 2 entry | **1,500** | ~6 days after Phase 0 complete |
| Phase 3 entry | **2,500** | ~10 additional days |
| Phase 4 entry | No gate beyond Phase 3 exit criteria | — |

```python
# How to check current window count
df = pd.read_csv("data/training/features_*.csv")
df_v2 = df[df['data_version'] == 2].drop_duplicates(subset='market_round_id')
print(f"Unique v2 windows: {len(df_v2)}")
```

## Out of Scope (This Cycle)

- UI redesign
- Non-BTC markets until BTC pipeline is stable
- Full automation of live capital deployment

## Detailed Phase Documentation

See [upgrade-plan/](upgrade-plan/) for implementation-complete specs for each phase, including hyperparameters, validation protocols, acceptance criteria, and code templates.
# OpenClaw + Context-Graph Master Upgrade Plan

> **Version 1.1** — Decision-Complete

This plan merges the strongest parts of `openclaw_upgrade_plan.md` and `Polymarket Model Upgrade Implementation Spec.md` into one implementation path.

Optimized for:
- Paper-first deployment
- ROI-first gating for Path A
- Staged rollout of all 3 context graphs

## Files

| File | Phase | Description |
|------|-------|-------------|
| [phase-0-stabilization.md](phase-0-stabilization.md) | 0 | Data pipeline triage, leakage prevention, sub-second ingestion |
| [phase-1-structural-alpha.md](phase-1-structural-alpha.md) | 1 | Activate Path B divergence trigger, harden Path C |
| [phase-2-baseline-rebuild.md](phase-2-baseline-rebuild.md) | 2 | Regularized XGBoost baseline with calibration and abstain gate |
| [phase-3-validation-hardening.md](phase-3-validation-hardening.md) | 3 | CPCV, triple-barrier labeling, delayed-lag features |
| [phase-4-graph-augmentation.md](phase-4-graph-augmentation.md) | 4 | Cross-asset GNN, participant graph, event KG |
| [phase-5-deployment.md](phase-5-deployment.md) | 5 | Full Kelly sizing, live capital, deployment readiness |

## v1.1 Additions (over v1.0)

1. **Sample-size phase gates** — hard entry requirements for Phase 2 and 3
2. **Explicit feature retention list** — ~35 features named by rank and family
3. **Concrete XGBoost hyperparameters** — prevents reproducing the 50.7% overfit gap
4. **Stage 4B data pre-requisite check** — added before graph augmentation
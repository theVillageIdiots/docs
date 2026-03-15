# Feature Engineering

*572 samples × 165 features • SymTorch/PySR analysis run Feb 26, 2026*

**Signal key:** 🟢 Top 20 | 🟡 Borderline | 🟠 Weak | 🔴 Noise | ⭐ PySR selected

## Pipeline Overview

### Stage 1: Base Features (53 from GraphSnapshot)

```
graph/schema.py → GraphSnapshot dataclass → 53 base fields
```

### Stage 2: Feature Engineering (112 additional)

```
training/feature_engineering.py → engineer_features()
```

| Transform | Input | Output Count | Description |
|-----------|-------|-------------|-------------|
| **Lag features** | 7 key signals × 3 lags | 21 | t-1, t-2, t-3 of each key signal |
| **Rolling mean** | 7 signals × 2 windows | 14 | 3-period and 5-period rolling averages |
| **Rolling std** | 7 signals × 2 windows | 14 | 3-period and 5-period rolling volatility |
| **Acceleration** | 4 signals | 4 | First-difference (change in momentum) |
| **Interactions** | 4 cross-products | 4 | momentum×orderbook, sentiment×activity, CVD×momentum, whale×momentum |
| **Polymarket** | Book + heuristic data | ~15 | Spread, implied prob, cheapness, mispricing, depth imbalance, lag signals |
| **Ontology/Window** | Event ontology | 15 | n_events, coherence, confirms, contradicts, whale_preceded, etc. |
| **Calendar** | Timestamp | 8 | hour_sin/cos, dow_sin/cos, minute_of_day, session flags |

**Key signals used for lags/rolling:**
`btc_momentum_1m`, `btc_momentum_5m`, `orderbook_imbalance`, `cvd_1m`, `cvd_5m`, `funding_rate`, `depth_imbalance_5`

## Signal Summary

| Category | Count | % | Description |
|----------|-------|---|-------------|
| 🟢 Top 20 | 20 | 12% | \|corr\| > 0.079 — clear signal |
| 🟡 Borderline | 15 | 9% | \|corr\| 0.05–0.079 — possible signal |
| 🟠 Weak | 44 | 27% | \|corr\| 0.03–0.05 — marginal |
| 🔴 Noise | 86 | 52% | \|corr\| < 0.03 — no detectable signal |

## PySR-Selected Features (⭐)

These 6 features appear repeatedly in PySR's best symbolic equations:

1. **`spot_move_magnitude`** — BTC price move since window open
2. **`cvd_5m_rmean5`** — 5-min CVD rolling average
3. **`cvd_1m_lag1`** — 1-min CVD lagged one window
4. **`orderbook_inertia_ms`** — Chainlink→Polymarket order book lag
5. **`whale_x_momentum`** — Whale activity × BTC momentum
6. **`btc_momentum_5m_rmean3`** — 5-min momentum rolling average
7. **`liq_short_volume_5m`** — Short liquidation volume (used in NN distillation)

## Top 20 Features — Clear Signal (\|corr\| > 0.079)

| Rank | Feature | Correlation | Source | Description | PySR? |
|------|---------|-------------|--------|-------------|-------|
| 1 | `spot_move_magnitude` | -0.145 | Divergence trigger | Size of BTC move since window open | ⭐ |
| 2 | `cvd_5m_rmean5` | -0.121 | CVD tracker | 5-min CVD, 5-period rolling mean | ⭐ |
| 3 | `btc_momentum_5m` | -0.113 | Binance | 5-min price momentum | ⭐ |
| 4 | `btc_move_since_open_pct` | -0.113 | Engineered | BTC move since window open (%) | |
| 5 | `oracle_lag_signal` | -0.113 | Engineered | BTC move × Poly price lag | |
| 6 | `cvd_1m_rmean3` | -0.109 | CVD tracker | 1-min CVD, 3-period rolling mean | |
| 7 | `lag_signal_up` | -0.100 | Engineered | Upward move × UP cheapness | |
| 8 | `cvd_1m_lag1` | -0.099 | CVD tracker | 1-min CVD, lagged 1 window | ⭐ |
| 9 | `cvd_5m` | -0.098 | CVD tracker | Raw 5-min cumulative volume delta | ⭐ |
| 10 | `cvd_5m_lag1` | -0.097 | CVD tracker | 5-min CVD, lagged 1 window | |
| 11 | `btc_momentum_5m_rmean3` | -0.096 | Binance | 5-min momentum, 3-period mean | ⭐ |
| 12 | `btc_momentum_1m` | -0.094 | Binance | 1-min price momentum | |
| 13 | `orderbook_inertia_ms` | -0.085 | Divergence trigger | Chainlink→Poly order book lag (ms) | ⭐ |
| 14 | `whale_x_momentum` | -0.084 | Engineered | Whale intensity × BTC momentum | ⭐ |
| 15 | `total_alpha_window_ms` | -0.084 | Divergence trigger | Total Binance→Chainlink→Poly lag (ms) | |
| 16 | `cvd_5m_lag3` | -0.084 | CVD tracker | 5-min CVD, lagged 3 windows | |
| 17 | `cvd_1m_rmean5` | -0.084 | CVD tracker | 1-min CVD, 5-period rolling mean | |
| 18 | `btc_momentum_1m_rmean5` | -0.081 | Binance | 1-min momentum, 5-period mean | |
| 19 | `lag_signal_down` | +0.081 | Engineered | Downward move × DOWN cheapness | |
| 20 | `liq_short_volume_5m` | -0.079 | Binance futures | Short liquidation volume (5 min) | ⭐ |

**All top correlations are NEGATIVE** — mean-reversion/contrarian signal dominates.
**Dominant signal family:** CVD — 8 of top 20 features are CVD variants.

## Feature Families

| Family | Features | Top Ranked | Dominant Signal? |
|--------|----------|-----------|-----------------|
| **CVD** | 20 (raw + lags + rolling) | 8 in top 20 | ✅ Strongest |
| **BTC Momentum** | 16 | 4 in top 20 | ✅ Strong |
| **Polymarket/Lag** | 20 | 4 in top 20 | ✅ Strong |
| **Orderbook** | 14 | 1 in top 20 | 🟡 Moderate |
| **Whale/On-chain** | 10 | 1 in top 20 | 🟡 Moderate |
| **Derivatives** | 14 | 1 in top 20 | 🟠 Weak |
| **Social/Sentiment** | 10 | 0 in top 20 | 🔴 None |
| **Calendar/Time** | 8 | 0 in top 20 | 🔴 None |
| **Cross-Asset** | 4 | 0 in top 20 | 🔴 None |
| **Mempool/Lightning** | 6 | 0 in top 20 | 🔴 None |
| **Graph/Regime** | 10 | 0 in top 20 | 🔴 None |
| **Ontology/Window** | 15 | 0 in top 20 | 🔴 None |
| **Engineered (Poly)** | 15 | 4 in top 20 | ✅ Strong (lag signals) |

## SymTorch/PySR Analysis

*Run: Feb 26, 2026 9:35 PM ET*

### Neural Net Baseline
- Architecture: 3-layer MLP (165 → 64 → 32 → 1, ReLU, Sigmoid)
- Train accuracy: **98.9%** | Test accuracy: **48.2%** | Overfit gap: **50.7%** (catastrophic)

### PySR Pareto Front

| Complexity | Loss | Equation |
|-----------|------|----------|
| 1 | 0.2494 | `y = 0.525` (baseline: always predict majority class) |
| 5 | 0.2441 | `y = 0.525 - 0.072 × spot_move_magnitude` |
| 7 | 0.2414 | `y = 0.525 - (spot_move_magnitude + cvd_5m_rmean5) × 0.057` |
| 9 | 0.2402 | `y = 0.524 - (spot_move + cvd_5m_rmean5 + orderbook_inertia) × 0.052` |
| 13 | 0.2380 | `y = √(\|0.286 - (spot_move + cvd_5m + orderbook_inertia + oracle_lag) × 0.043\|)` |

### Key Findings

1. The simplest useful equation uses 1 feature and reduces loss by only 2.1% vs baseline
2. Adding more features barely helps — going from 1 to 6 features improves loss by only 4.5%
3. Maximum complexity (30 terms) achieves only 6.3% loss reduction vs "always predict 0.525"
4. Cross-validated accuracy: 50.2% — effectively coin flip
5. The signal is **mean-reversion** — all top features have negative correlation
6. CVD is the strongest signal family — 8 of top 20 features
7. PySR repeatedly selects the same 6 core features regardless of complexity

## Retained Feature Set for Phase 2 Compact Model (~35 features)

Based on SymTorch/PySR correlation analysis. ~130 features are dropped.

| Rank | Feature | Correlation | Family |
|------|---------|-------------|--------|
| 1 | `spot_move_magnitude` | -0.145 | Divergence trigger |
| 2 | `cvd_5m_rmean5` | -0.121 | CVD |
| 3 | `btc_momentum_5m` | -0.113 | Momentum |
| 4 | `btc_move_since_open_pct` | -0.113 | Poly engineered |
| 5 | `oracle_lag_signal` | -0.113 | Poly engineered |
| 6 | `cvd_1m_rmean3` | -0.109 | CVD |
| 7 | `lag_signal_up` | -0.100 | Poly engineered |
| 8 | `cvd_1m_lag1` | -0.099 | CVD |
| 9 | `cvd_5m` | -0.098 | CVD |
| 10 | `cvd_5m_lag1` | -0.097 | CVD |
| 11 | `btc_momentum_5m_rmean3` | -0.096 | Momentum |
| 12 | `btc_momentum_1m` | -0.094 | Momentum |
| 13 | `orderbook_inertia_ms` | -0.085 | Divergence trigger |
| 14 | `whale_x_momentum` | -0.084 | Whale/engineered |
| 15 | `total_alpha_window_ms` | -0.084 | Divergence trigger |

See [UPGRADE_PLAN](../development/upgrade-plan/phase-2-baseline-rebuild.md) for the complete retained feature list with all 35 features.
# Phase 2: Baseline Path A Rebuild (Tabular-First)

**Status:** Pending Phase 1 + data gate
**Entry gate:** ≥ 1,500 unique v2 windows

> Do not begin until verified: `df_v2 = df[df['data_version'] == 2].drop_duplicates(subset='market_round_id'); assert len(df_v2) >= 1500`

## Tasks

- Reduce to the ~35-feature retained set
- Train regularized XGBoost baseline with the hyperparameters below
- Add out-of-sample Platt scaling calibration
- Add abstain/no-trade gate from uncertainty + edge threshold
- Run PySR 3-feature equation as parallel paper trader (overfitting canary)

## XGBoost Hyperparameters

> These are not defaults. Defaults will overfit badly at this sample size.

```python
import xgboost as xgb
from sklearn.calibration import CalibratedClassifierCV

base_model = xgb.XGBClassifier(
    n_estimators=200,
    max_depth=3,              # Forces simple trees — critical at small N
    min_child_weight=15,      # Requires 15+ samples per leaf — prevents memorization
    subsample=0.7,
    colsample_bytree=0.6,
    learning_rate=0.05,
    reg_alpha=0.1,
    reg_lambda=1.0,
    eval_metric='logloss',
    use_label_encoder=False,
    random_state=42
)

calibrated_model = CalibratedClassifierCV(
    estimator=base_model,
    method='sigmoid',
    cv=5
)
calibrated_model.fit(X_train, y_train)
```

If train-test gap exceeds 10 percentage points after this, increase `min_child_weight` to 20 before any other adjustment.

## PySR Canary (run in parallel)

```python
def pysr_predict(spot_move_magnitude, cvd_5m_rmean5, orderbook_inertia_ms):
    """
    PySR complexity-9 equation. Leak-free. 3 parameters.
    Cross-validated accuracy: 50.2% on 572 windows.
    If XGBoost does not clearly outperform this, it is still overfitting.
    """
    return 0.524 - (spot_move_magnitude + cvd_5m_rmean5 + orderbook_inertia_ms) * 0.052
```

## Path A Trade Gate

```python
def should_trade_path_a(envelope: PredictionEnvelopeV1, u_max: float = 0.98) -> bool:
    has_edge = envelope['expected_edge'] > (
        envelope['expected_fee'] + envelope['expected_slippage'] + 0.015
    )
    low_uncertainty = envelope['uncertainty'] <= u_max
    confident = envelope['p_no_trade'] < 0.5
    return has_edge and low_uncertainty and confident
```

`u_max` default: `0.98`. Tighten to `0.95` once live calibration confirms model quality. Target 15–25% of windows triggering PASS.

## Validation Protocol

- Purged walk-forward with scaled embargo
- Embargo window: `max(1, int(0.01 * len(df)))` windows (at 1,500 = 15 windows ≈ 75 minutes)
- Rolling windows: train 70%, validate 15%, test 15% — no absolute calendar windows
- No k-fold, no random shuffle

## Retained Feature Set (~35)

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
| 14 | `whale_x_momentum` | -0.084 | Interaction |
| 15 | `total_alpha_window_ms` | -0.084 | Divergence trigger |
| 16 | `cvd_5m_lag3` | -0.084 | CVD |
| 17–35 | (see full list in feature-engineering.md) | | |

## Feature Families Dropped

| Family | Count dropped | Disposition |
|--------|--------------|-------------|
| Social / sentiment | 10 | Drop from model; keep raw capture via `SignalEventV1` for Phase 3 |
| Calendar / time | 8 | Drop from model entirely |
| Cross-asset (raw) | 4 | Drop from model; re-evaluate at 2,500+ windows |
| Mempool | 4 | Drop from model AND stop collection |
| Lightning Network | 2 | Drop from model AND stop collection |
| Graph / regime flags | 10 | Drop from model; re-evaluate in Phase 3 |
| Ontology / window | 15 | Drop from model; re-evaluate in Phase 3 |

## Deliverables

- `model_path_a_baseline_v1` (calibrated XGBoost, ~35 features)
- Calibration reliability diagram
- A/B report: XGBoost vs PySR canary

## Exit Criteria

- Positive net paper ROI for Path A over 14 rolling days
- Train-test performance gap < 10 percentage points
- Calibration drift below configured threshold
- XGBoost outperforms PySR canary in paper trading
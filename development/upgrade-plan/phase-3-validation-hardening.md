# Phase 3: Advanced Labeling & Validation Hardening

**Status:** Pending Phase 2
**Entry gate:** ≥ 2,500 unique v2 windows

> CPCV requires this sample count — running it on fewer windows produces unreliable PBO estimates.

## Tasks

- Introduce triple-barrier labeling with neutral exclusion
- Add CPCV for robustness analysis
- Add regime abstain gate when regime underperformance is confirmed
- Re-enable delayed-lag sentiment/whale/options features via `SignalEventV1` log with lagged joins

## Triple-Barrier Labeling

```python
def triple_barrier_label(df, atr_window=20, barrier_multiplier=0.05):
    """
    Labels: UP (1), DOWN (0), NEUTRAL (excluded from training).
    NEUTRAL = abs(price_move) < barrier_multiplier * ATR(atr_window).
    Target exclusion rate: 15–30% of windows.
    """
    df['atr'] = compute_atr(df, window=atr_window)
    df['barrier'] = df['atr'] * barrier_multiplier
    df['actual_move'] = (df['btc_close'] - df['btc_open']) / df['btc_open']

    df['label'] = None
    df.loc[df['actual_move'] > df['barrier'], 'label'] = 1
    df.loc[df['actual_move'] < -df['barrier'], 'label'] = 0

    df_labeled = df[df['label'].notna()].copy()
    print(f"Exclusion rate: {1 - len(df_labeled)/len(df):.1%}")
    return df_labeled
```

Initial multiplier: `0.05 * ATR(20)`. Tune based on observed exclusion rate.

## CPCV

```python
from mlfinlab.cross_validation import CombinatorialPurgedKFold, ml_cross_val_score

cpkf = CombinatorialPurgedKFold(
    n_splits=6,
    n_test_splits=2,
    pct_embargo=0.01    # ~25–30 minutes embargo at 2,500 windows
)

scores = ml_cross_val_score(
    classifier=calibrated_model,
    X=X, y=y,
    cv_gen=cpkf,
    scoring='accuracy'
)
# Target PBO < 0.5
```

## Delayed-Lag Feature Architecture

```python
def build_delayed_features(signal_log_df, prediction_windows_df, lag_minutes=30):
    """
    For each prediction window at T, look up slow signal values from T-30min.
    Requires SignalEventV1 log populated since Phase 0.
    Default lag offset: 30 minutes.
    """
    delayed = []
    for window_time in prediction_windows_df['window_open_time']:
        target_time = window_time - pd.Timedelta(minutes=lag_minutes)
        mask = signal_log_df['timestamp'] <= target_time
        if mask.any():
            closest = signal_log_df[mask].iloc[-1]
            delayed.append({
                'window_open_time': window_time,
                'sentiment_lag30': closest.get('tier1_sentiment_5m'),
                'whale_flow_lag30': closest.get('whale_net_exchange_flow'),
                'put_call_lag30': closest.get('put_call_ratio'),
                'iv_atm_lag30': closest.get('iv_atm'),
            })
    return prediction_windows_df.merge(pd.DataFrame(delayed), on='window_open_time', how='left')
```

Delayed-lag features are kept only if ablation confirms they improve net paper ROI.

## Regime Abstain Gate

Only activate if Phase 2 regime decomposition confirms accuracy gap > 8pp:

```python
def should_abstain_regime(df_recent) -> bool:
    recent_direction = df_recent['btc_went_up'].rolling(6).mean().iloc[-1]
    return (recent_direction > 0.70) or (recent_direction < 0.30)
```

## Defaults

- Triple-barrier initial multiplier: `0.05 * ATR(20)`
- Delayed-lag initial offset: `30 minutes`

## Deliverables

- Labeling module with neutral exclusion rate stats
- CPCV + PBO report
- Regime performance decomposition report
- Delayed-lag ablation report

## Exit Criteria

- Neutral exclusion rate in 15–30% band and reproducible
- CPCV distribution stable (std < 0.03 across folds)
- Delayed-lag features kept only if they improve net ROI in ablation
- Regime gate active only if gap > 8pp confirmed
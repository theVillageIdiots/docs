# Phase 1: Structural Alpha Activation

**Status:** Pending Phase 0
**Entry gate:** Phase 0 complete
**Goal:** Activate Path B divergence trigger in paper mode, harden Path C

## Tasks

- Activate Path B divergence trigger in paper mode (currently observe-only)
- Replace final-45s execution blend (70% heuristic / 30% momentum) with 100% oracle lag decision logic. The heuristic carries no information in the final 45 seconds — only the direction of spot vs. PTB matters at that point
- Keep Path C sum-arb active with strict threshold

## Path Rules

```python
# Path B trigger
if time_remaining <= 45 and abs(spot_price - chainlink_ptb) > threshold:
    direction = oracle_lag_signal_direction  # 100% oracle lag, no heuristic blend

# Path C trigger
if UP_ask + DOWN_ask <= 0.97:
    # Execute sum-arb
```

## Router Interface Update

```python
# execution/strategy_router.py
def select_path(envelope: PredictionEnvelopeV1, ctx: dict) -> Literal["A", "B", "C", "PASS"]
```

## Deliverables

- Paper-trade logs with path label, signal snapshot, and realized outcome for every round

## Exit Criteria

- Path B decisions are directionally aligned with oracle lag signal in 100% of logged trades
- Path B and Path C logs include full latency stamps and fee-adjusted EV
- Path B paper hit-rate measurable (target: confirm > 65% directional accuracy over 100+ trades before Phase 2 begins)
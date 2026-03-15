# Phase 5: Execution / Risk Framework & Deployment Readiness

**Status:** Pending Phase 4
**Entry gate:** Phase 4 exit criteria met

## Tasks

- Fractional Kelly sizing at 0.25× with hard per-round cap
- Enforce per-path minimum edge after fees/slippage
- Prefer maker orders when fill probability is acceptable
- Add hard circuit breakers and manual halt conditions
- Weekly retraining or on detected drift

## Kelly Sizing

```python
def kelly_fraction(p_up: float, odds: float = 1.0, fraction: float = 0.25) -> float:
    edge = p_up - (1 - p_up) / odds
    if edge <= 0:
        return 0.0
    return (edge / odds) * fraction

# Cap output at 0.05 (5% bankroll max per round)
bet_fraction = min(kelly_fraction(envelope['p_up']), 0.05)
```

## Maker-First Execution

```python
def place_order(direction, envelope, bankroll, time_remaining_s):
    bet_size = kelly_fraction(envelope['p_up']) * bankroll
    if time_remaining_s > 60:
        limit_price = current_market_price - 0.01  # Maker order
        return place_limit_order(direction, bet_size, limit_price)
    else:
        return place_market_order(direction, bet_size)  # Taker in final 60s
```

## Hard Limits

| Limit | Value |
|-------|-------|
| Max stake per round | 5% bankroll |
| Session hard stop drawdown | 15% |
| Path B degradation halt | 7-day hit-rate < 65% |

## Retraining Policy

- **Scheduled:** Weekly retrain
- **Drift trigger:** Rolling 7-day accuracy drops > 3pp below baseline, OR KS-test detects significant distribution shift (p < 0.05) on any top-10 feature
- **Window:** Always use rolling 90-day window — do not train on data older than 90 days

## Deliverables

- Unified risk policy config file
- Live-readiness dashboard with per-path metrics:
  - Path A/B/C win rate
  - Rolling ROI
  - Brier score
  - Calibration error
  - Kelly EV per round
  - Drift alerts

## Exit Criteria

- 6 consecutive weeks of positive combined paper ROI (A+B+C)
- No circuit-breaker breaches in final 14 days
- Stable latency and data freshness

## Acceptance Criteria (Final Gate to Live Capital)

1. 14-day rolling net ROI for Path A is positive after fees/slippage and remains positive through a 6-week paper window
2. Combined system (A+B+C) has positive net paper ROI for 6 consecutive weeks
3. Max drawdown stays below 15% in paper trading throughout
4. All promoted graph stages cleared their per-stage A/B gate. Failed stages rolled back and documented
5. No unresolved leakage or schema contract violations
6. Path B participant wallet data confirmed captured before Stage 4B began
# Monitoring

## Status Endpoint

`GET http://localhost:8080/status`

| Metric | Description | Alert Threshold |
|--------|-------------|-----------------|
| `calibration_brier_200` | Rolling Brier score (last 200 trades) | > 0.28 |
| `drift_psi_max` | Max feature drift (PSI) across all features | > 0.2 |
| `conformal_safe_mode` | Whether safe mode is active (50% bet reduction) | True for >30 min |
| `websocket_connected` | Polymarket WS status | False |
| `updates_per_second` | Data feed throughput | < 0.5 |
| `memgraph_connected` | Graph DB connectivity | False |

## Health Check

`GET http://localhost:8080/healthz`

Returns `200 OK` when the bot process is healthy. Circuit breaker state and WebSocket connectivity are checked.

## Dashboard

`http://localhost:3000` — Next.js dashboard with:
- Real-time P&L chart
- Trade log with path labels (A/B/C)
- Feature drift indicators
- Circuit breaker state
- Live feed status

## Circuit Breaker Behavior

| Condition | Action |
|-----------|--------|
| 8 consecutive losses | 2-min cooldown |
| Each additional breach | 1.5× escalation (max 10 min) |
| 15% daily drawdown | Hard stop for session |
| Path B 7-day hit-rate < 65% | Path B halt |

## Training Schedule

- **Cron:** Every 6 hours
- **Triggers if:** ≥ 500 v2 windows available
- **Cron ID:** `f1c485d7`

## Key Alerts to Set Up

1. `websocket_connected = false` for > 30 seconds
2. `drift_psi_max > 0.2` (distribution shift on key feature)
3. `calibration_brier_200 > 0.28` (model miscalibration)
4. Circuit breaker hard stop triggered
5. Process exit (LaunchAgent will restart, but alert on frequency)
# Release Process

## Blocking Gates (must stay green before any release)

- Unit/integration tests: `57/57` pass
- Gate tests A/B bundle: `test_assessment_fixes.py`, `test_path_a_gate.py`
- Gate tests Phase bundle: `test_phase0.py`, `test_phase1.py`, `test_graph.py`
- Audit scripts pass: `03`, `05`, `06`, `09`
- QA re-run confirms gate flips:
  - Leakage (H1/H2) PASS
  - Fill realism (H5) PASS
  - Breaker consistency (H3) PASS

## Standard Release Steps

1. **Freeze in git** — annotated tag on release commit
2. **24h forward paper validation** on fixed build
3. **Extend to 48h** if no regressions
4. **Record before/after KPIs:**
   - Win rate by path
   - Realized P&L by path
   - Drawdown
   - Circuit breaker halt behavior
   - Fill spread paid vs midpoint
5. **Signoff** — engineering, QA, risk, Go/No-Go decision

## Signoff Packet Templates

See [templates/](templates/) for:
- `engineering-signoff.md`
- `qa-signoff.md`
- `risk-signoff.md`
- `go-no-go.md`

Usage: copy each template for the active release (e.g., `engineering_signoff_2026-03-06.md`), fill all required evidence fields and attach artifact links.

## Monitoring Follow-ups (non-blocking, urgent)

- Expose `/status.websocket_connected`
- Expose `/status.updates_per_second`
- Expose `/status.memgraph_connected`
- Expose x-stream counters in `/status`
- Profile occasional `/status` latency spikes (>2s)

## Data/Model Follow-ups Post-Release

- Retrain model on post-fix clean pipeline data
- Recompute significance (frozen-window p-value, CI)
- Re-run calibration and Brier/log-loss comparison
- Re-check H4 graph growth/memory pressure behavior

## P0 Remediation Pack (2026-03-06)

Fixes merged in last release:
- **H1** collector overwrite fix
- **H2** trend leakage fix
- **H3** breaker double-count fix
- **H5** executable fill pricing fix
- StrategyRouter `execute()` compatibility fix
- P0 regression tests
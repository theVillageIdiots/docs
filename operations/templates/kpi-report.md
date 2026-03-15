# KPI Rollout Report Template

Use this template to evaluate rollout impact at **24h close** and **48h close**.

## Run commands

```bash
# 24h close
python diagnostics/generate_kpi_report.py \
  --change-ts <ROLLOUT_TS_UTC> \
  --close-hours 24

# 48h close
python diagnostics/generate_kpi_report.py \
  --change-ts <ROLLOUT_TS_UTC> \
  --close-hours 48
```

Optional flags:

```bash
--trades logs/trades.jsonl
--bot-logs-glob "logs/bot_*.log"
--out-dir docs/reports
--tag <custom_tag>
```

## KPIs required

1. **Win rate by path**
2. **Realized PnL by path**
3. **Drawdown**
4. **Breaker halt behavior**
5. **Fill spread paid vs midpoint**

## Interpretation checklist

- [ ] Did win rate improve for target paths?
- [ ] Did realized PnL improve without hidden tail risk?
- [ ] Did max drawdown shrink or remain acceptable?
- [ ] Did breaker halts drop (or become better concentrated by reason)?
- [ ] Did spread paid vs midpoint tighten (lower bps)?

## Sign-off section

- **Decision**: PASS / FAIL / EXTEND OBSERVATION
- **Notes**:
- **Follow-up actions**:

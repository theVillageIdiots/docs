# polybot-execution-3.12

**Python:** 3.12
**Directory:** `execution/`
**Purpose:** Live order management, market-making, paper trading, statistical arbitrage execution, and strategy routing.

## Key Files

| File | Purpose |
|---|---|
| `order_manager.py` | CLOB order lifecycle management |
| `market_maker.py` | Automated market-making strategy |
| `paper_trader.py` | Paper trading simulator (live feed, no real orders) |
| `sum_arb.py` | Sum-of-parts arbitrage executor |
| `divergence_trigger.py` | Cross-market divergence entry trigger |
| `strategy_router.py` | Routes signals to appropriate execution paths |

## Dependencies

```
py-clob-client>=0.1.0
web3>=6.0.0
eth-account>=0.9.0
websockets>=12.0
aiohttp>=3.9.0
requests>=2.31.0
numpy>=1.26.0
pandas>=2.1.0
rich>=13.0.0
loguru>=0.7.0
python-dotenv>=1.0.0
pyyaml>=6.0
```

## Create

```bash
pyenv virtualenv 3.12 polybot-execution-3.12
pyenv activate polybot-execution-3.12
pip install --upgrade pip wheel
pip install \
  "py-clob-client>=0.1.0" \
  "web3>=6.0.0" \
  "eth-account>=0.9.0" \
  "websockets>=12.0" \
  "aiohttp>=3.9.0" \
  "requests>=2.31.0" \
  "numpy>=1.26.0" \
  "pandas>=2.1.0" \
  "rich>=13.0.0" \
  "loguru>=0.7.0" \
  "python-dotenv>=1.0.0" \
  "pyyaml>=6.0"
```

## Pin to Directory

```bash
cd polymarket-bot/execution
pyenv local polybot-execution-3.12
```

## Notes

- Dependency overlap with `data/` is high — if splitting envs, these two could share one env (`polybot-feeds-3.12`).
- `paper_trader.py` logs to `data/paper_trades.json` — ensure both dirs share filesystem access.
- `sum_arb.py` reads arbitrage logs from `data/sum_arb_log.json`.
- Does **not** need ML deps (sklearn, xgboost) at runtime — signals are consumed as floats passed in from `signals/`.

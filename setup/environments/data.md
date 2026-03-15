# polybot-data-3.12

**Python:** 3.12
**Directory:** `data/`
**Purpose:** All live data feeds — Polymarket orderbook, Chainlink oracles, Deribit options, Binance spot WebSocket, CVD tracker, Fear & Greed index, on-chain feeds, social data.

## Key Files

| File | Feed |
|---|---|
| `polymarket_ws.py` | Polymarket WebSocket (real-time orderbook) |
| `polymarket_book.py` | Polymarket CLOB book snapshot |
| `polymarket_feed.py` | Polymarket REST polling |
| `polymarket_rtds.py` | Real-time data service |
| `polymarket_5min.py` | 5-minute bar aggregator |
| `chainlink_data_streams.py` | Chainlink Data Streams (REST + WS) |
| `chainlink_feed.py` | Chainlink public price feeds |
| `chainlink_public_feed.py` | Chainlink public on-chain reads |
| `binance_ws.py` | Binance spot WebSocket |
| `deribit_options.py` | Deribit options chain |
| `pyth_feed.py` | Pyth Network oracle |
| `cvd_tracker.py` | Cumulative Volume Delta |
| `fear_greed.py` | Fear & Greed index |
| `signal_event_log.py` | Event log writer |

Sub-dirs: `macro/`, `onchain/`, `social/`, `backtest/`, `training/`, `signal_events/`

## Dependencies

**This is the highest-risk env for the master** — `web3`/`eth-account` can have numpy ABI issues.

```
py-clob-client>=0.1.0
web3>=6.0.0
eth-account>=0.9.0
websockets>=12.0
aiohttp>=3.9.0
requests>=2.31.0
numpy>=1.26.0
pandas>=2.1.0
pyarrow>=15.0.0
rich>=13.0.0
loguru>=0.7.0
python-dotenv>=1.0.0
pyyaml>=6.0
tweepy>=4.14.0
```

## Create

```bash
pyenv virtualenv 3.12 polybot-data-3.12
pyenv activate polybot-data-3.12
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
  "pyarrow>=15.0.0" \
  "rich>=13.0.0" \
  "loguru>=0.7.0" \
  "python-dotenv>=1.0.0" \
  "pyyaml>=6.0" \
  "tweepy>=4.14.0"
```

## Pin to Directory

```bash
cd polymarket-bot/data
pyenv local polybot-data-3.12
```

## Clash Notes

- `web3 >= 6` requires `cytoolz`, `eth-hash`, `hexbytes` — all fine on 3.12.
- `websockets >= 12` changed its API significantly from v10; ensure `polymarket_ws.py` uses the v12+ async context manager pattern.
- `py-clob-client` may pin its own `requests` version — check with `pip show py-clob-client` after install and resolve with `pip install --upgrade requests` if needed.
- `tweepy` (social/) is isolated to `data/social/` — safe to add here.

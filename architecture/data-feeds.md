# Data Feeds

The bot ingests 24+ data feeds across five categories.

## Price & Market Data

| # | Feed | File | Protocol | Frequency | Data |
|---|------|------|----------|-----------|------|
| 1 | **Binance Spot** | `data/binance_ws.py` | WebSocket | Real-time | BTC/USDT price, volume, trades |
| 2 | **Binance Futures** | `data/binance_ws.py` | WebSocket | Real-time | Mark price, funding rate, OI |
| 3 | **CVD Tracker** | `data/cvd_tracker.py` | Computed | Per-tick | Cumulative Volume Delta (buy vs sell pressure) |
| 4 | **Pyth Network** | `data/pyth_feed.py` | WebSocket | ~1.35s avg | BTC/USD oracle price (FREE, sub-second) |
| 5 | **Chainlink Public** | `data/chainlink_public_feed.py` | HTTP polling | 10s | BTC/USD on-chain aggregator (27-min heartbeat) |

## Derivatives

| # | Feed | File | Protocol | Frequency | Data |
|---|------|------|----------|-----------|------|
| 6 | **Deribit Options** | `data/deribit_options.py` | REST | 30s | Put/call ratio, IV (ATM/skew), options volume |
| 7 | **Liquidations** | `data/binance_ws.py` | WebSocket | Real-time | Long/short liquidation volumes |

## Polymarket

| # | Feed | File | Protocol | Frequency | Data |
|---|------|------|----------|-----------|------|
| 8 | **Polymarket Book** | `data/polymarket_book.py` | REST | 5s | UP/DOWN best bid/ask, depth |
| 9 | **Polymarket 5-Min** | `data/polymarket_5min.py` | REST | Polling | Active 5-min BTC market discovery |
| 10 | **Polymarket Feed** | `data/polymarket_feed.py` | REST | Polling | General market data |
| 11 | **Polymarket WS** | `data/polymarket_ws.py` | REST polling | 5s | Order book snapshots (WS endpoints 404) |

## Social & Sentiment

| # | Feed | File | Protocol | Frequency | Data |
|---|------|------|----------|-----------|------|
| 12 | **X/Twitter Stream** | `data/social/x_stream.py` | Filtered Stream v2 | Real-time | 136 curated accounts (12 T1, 49 T2, 48 T3, 27 regulatory) |
| 13 | **News Feed** | `data/social/news_feed.py` | RSS/scraping | Polling | Crypto news headlines |
| 14 | **Sentiment Scorer** | `data/social/sentiment_scorer.py` | Computed | Per-post | LLM-scored sentiment (-1 to +1) |
| 15 | **Tweet Extractor** | `data/social/tweet_extractor.py` | GLiNER2 | Per-post | Zero-shot NER: entities, sentiment, causal claims |

## On-Chain & Macro

| # | Feed | File | Protocol | Frequency | Data |
|---|------|------|----------|-----------|------|
| 16 | **Whale Alerts** | (integrated in main.py) | API | Polling | Large BTC transactions, exchange flows |
| 17 | **Mempool** | (integrated in main.py) | API | Polling | Mempool size, fee rates, pending large txs |
| 18 | **Economic Calendar** | (integrated in main.py) | API | Daily | FOMC, CPI, employment — event proximity |
| 19 | **Fear & Greed Index** | `data/fear_greed.py` | REST | Hourly | Crypto Fear & Greed (0-100 → normalized -1 to +1) |

## Cross-Asset

| # | Feed | Source | Data |
|---|------|--------|------|
| 20 | **ETH/BTC ratio** | Binance | 5-min change in ETH/BTC |
| 21 | **SOL momentum** | Binance | SOL/USDT 5-min momentum |
| 22 | **DXY proxy** | Computed | Dollar strength proxy via EUR/USD |
| 23 | **Altcoin divergence** | Computed | BTC vs altcoin momentum divergence |

## Execution Feeds

| # | Feed | File | Data |
|---|------|------|------|
| 24 | **Divergence Trigger** | `execution/divergence_trigger.py` | Binance→Chainlink→Polymarket lead-lag (observe mode) |
| 25 | **Sum-Arb Detector** | `execution/sum_arb.py` | UP_ask + DOWN_ask < $0.97 detection |
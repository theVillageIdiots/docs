# Complete Feature List — Polymarket BTC 5-Min Prediction Bot

*572 samples × 165 features • SymTorch/PySR analysis run Feb 26, 2026*

**Signal key:** 🟢 Top 20 | 🟡 Borderline | 🟠 Weak | 🔴 Noise | ⭐ PySR selected

---

## Base Features (53) — from GraphSnapshot

These are collected directly from live feeds every snapshot cycle.

### Price & Momentum (from Binance)

| # | Feature | Type | Corr | Signal | Description |
|---|---------|------|------|--------|-------------|
| 1 | `btc_price` | float | — | (label input) | Current BTC/USDT spot price |
| 2 | `btc_momentum_1m` | float | -0.094 | 🟢 | 1-minute price change ratio |
| 3 | `btc_momentum_5m` | float | -0.113 | 🟢 | 5-minute price change ratio |
| 4 | `mark_price` | float | — | (excluded) | Futures mark price |

### Order Book (from Binance)

| # | Feature | Type | Corr | Signal | Description |
|---|---------|------|------|--------|-------------|
| 5 | `orderbook_imbalance` | float | +0.045 | 🟠 | (bid_depth - ask_depth) / total at top levels |
| 6 | `bid_ask_spread` | float | -0.044 | 🟠 | Best ask - best bid |
| 7 | `depth_imbalance_5` | float | +0.053 | 🟡 | Depth imbalance at 5 price levels |
| 8 | `depth_imbalance_20` | float | +0.048 | 🟠 | Depth imbalance at 20 price levels |

### Cumulative Volume Delta (from CVD Tracker)

| # | Feature | Type | Corr | Signal | Description |
|---|---------|------|------|--------|-------------|
| 9 | `cvd_1m` | float | -0.057 | 🟡 | 1-min cumulative volume delta (buy vol - sell vol) |
| 10 | `cvd_5m` | float | -0.098 | 🟢⭐ | 5-min cumulative volume delta |
| 11 | `cvd_price_divergence` | float | +0.023 | 🔴 | CVD vs price direction disagreement |

### Derivatives (from Binance Futures & Deribit)

| # | Feature | Type | Corr | Signal | Description |
|---|---------|------|------|--------|-------------|
| 12 | `funding_rate` | float | -0.030 | 🔴 | Perpetual futures funding rate |
| 13 | `open_interest` | float | +0.041 | 🟠 | Total open interest (contracts) |
| 14 | `oi_change_5m` | float | +0.033 | 🟠 | 5-min change in open interest |
| 15 | `liq_long_volume_5m` | float | +0.015 | 🔴 | Long liquidation volume (5 min) |
| 16 | `liq_short_volume_5m` | float | -0.079 | 🟢⭐ | Short liquidation volume (5 min) |
| 17 | `liq_ratio` | float | +0.044 | 🟠 | Long/short liquidation ratio |
| 18 | `put_call_ratio` | float | -0.043 | 🟠 | Deribit options put/call ratio |
| 19 | `iv_atm` | float | -0.013 | 🔴 | At-the-money implied volatility |
| 20 | `iv_skew` | float | +0.016 | 🔴 | Put-call IV skew |
| 21 | `deribit_funding_rate` | float | 0.000 | 🔴 | Deribit perpetual funding rate |
| 22 | `options_volume_ratio` | float | -0.011 | 🔴 | Options volume relative to spot |

### Polymarket Book (from Polymarket REST)

| # | Feature | Type | Corr | Signal | Description |
|---|---------|------|------|--------|-------------|
| 23 | `poly_up_bid_depth` | float | -0.016 | 🔴 | UP token bid-side depth (USD) |
| 24 | `poly_up_ask_depth` | float | +0.003 | 🔴 | UP token ask-side depth (USD) |
| 25 | `poly_down_bid_depth` | float | +0.006 | 🔴 | DOWN token bid-side depth (USD) |
| 26 | `poly_down_ask_depth` | float | -0.025 | 🔴 | DOWN token ask-side depth (USD) |
| 27 | `poly_smart_money_signal` | float | -0.027 | 🔴 | Smart money flow indicator |
| 28 | `polymarket_up_price` | float | 0.000 | 🔴 | UP token mid price |
| 29 | `polymarket_down_price` | float | 0.000 | 🔴 | DOWN token mid price |

### Social & Sentiment (from X/Twitter Stream)

| # | Feature | Type | Corr | Signal | Description |
|---|---------|------|------|--------|-------------|
| 30 | `tier1_sentiment_5m` | float | 0.000 | 🔴 | Tier-1 (market movers) avg sentiment |
| 31 | `tier1_post_count_5m` | int | +0.053 | 🟡 | Tier-1 post count in 5 min |
| 32 | `tier2_sentiment_5m` | float | -0.066 | 🟡 | Tier-2 (smart money) avg sentiment |
| 33 | `influencer_consensus` | float | +0.006 | 🔴 | Cross-influencer sentiment agreement |
| 34 | `sentiment_momentum` | float | +0.013 | 🔴 | Rate of sentiment change |
| 35 | `high_impact_poster_active` | bool | 0.000 | 🔴 | Whether a T1 account posted |
| 36 | `social_activity_level` | float | +0.053 | 🟡 | Overall social media activity |

### Whale & On-Chain (from Whale Alerts, Mempool)

| # | Feature | Type | Corr | Signal | Description |
|---|---------|------|------|--------|-------------|
| 37 | `whale_net_exchange_flow_10m` | float | -0.042 | 🟠 | Net BTC flow to/from exchanges (10 min) |
| 38 | `whale_tx_count_10m` | int | +0.003 | 🔴 | Number of whale transactions (10 min) |
| 39 | `known_fund_movement` | bool | 0.000 | 🔴 | Known fund wallet moved BTC |
| 40 | `exchange_deposit_spike` | bool | +0.030 | 🔴 | Unusual exchange deposit detected |
| 41 | `whale_intensity` | float | -0.046 | 🟠 | Composite whale activity score |
| 42 | `mempool_size_mb` | float | +0.001 | 🔴 | Bitcoin mempool size in MB |
| 43 | `mempool_tx_count` | int | +0.001 | 🔴 | Pending transaction count |
| 44 | `mempool_fee_rate_fast` | float | -0.016 | 🔴 | Fast-confirm fee rate (sat/vB) |
| 45 | `large_tx_pending` | int | +0.023 | 🔴 | Large (>1 BTC) pending transactions |
| 46 | `lightning_channel_count` | int | -0.022 | 🔴 | Lightning Network channel count |
| 47 | `lightning_capacity_btc` | float | -0.024 | 🔴 | Lightning Network total capacity |

### Cross-Asset (from Binance)

| # | Feature | Type | Corr | Signal | Description |
|---|---------|------|------|--------|-------------|
| 48 | `eth_btc_ratio_change_5m` | float | +0.000 | 🔴 | ETH/BTC ratio 5-min change |
| 49 | `sol_momentum_5m` | float | -0.058 | 🟡 | SOL/USDT 5-min momentum |
| 50 | `dxy_proxy_change_5m` | float | -0.031 | 🟠 | Dollar strength proxy change |
| 51 | `altcoin_btc_divergence` | float | -0.019 | 🔴 | BTC vs altcoin momentum gap |

### Lead-Lag / Divergence (from Divergence Trigger)

| # | Feature | Type | Corr | Signal | Description |
|---|---------|------|------|--------|-------------|
| 52 | `spot_poly_divergence` | float | +0.037 | 🟠 | Binance vs Polymarket price gap |
| 53 | `oracle_propagation_ms` | int | -0.004 | 🔴 | Binance → Chainlink delay (ms) |
| 54 | `orderbook_inertia_ms` | int | -0.085 | 🟢⭐ | Chainlink → Polymarket delay (ms) |
| 55 | `total_alpha_window_ms` | int | -0.084 | 🟢 | Total lead-lag window (ms) |
| 56 | `spot_poly_divergence_count_5m` | int | +0.006 | 🔴 | Divergence events in 5 min |
| 57 | `divergence_hit_rate` | float | -0.009 | 🔴 | Historical divergence accuracy |
| 58 | `spot_move_magnitude` | float | -0.145 | 🟢⭐ | BTC price move size since window open |
| 59 | `poly_adjustment_speed` | float | -0.029 | 🔴 | How fast Poly adjusts to spot |

### Graph & Regime (from Knowledge Graph)

| # | Feature | Type | Corr | Signal | Description |
|---|---------|------|------|--------|-------------|
| 60 | `event_proximity_hours` | float | +0.033 | 🟠 | Hours until next macro event |
| 61 | `graph_anomaly_score` | float | +0.021 | 🔴 | Graph-based anomaly detection |
| 62 | `causal_chain_strength` | float | -0.016 | 🔴 | Strength of causal event chains |
| 63 | `signal_count` | int | -0.064 | 🟡 | Number of active trading signals |
| 64 | `signal_agreement` | float | -0.016 | 🔴 | Agreement among active signals |
| 65 | `influencer_whale_alignment` | float | 0.000 | 🔴 | Influencer + whale signal alignment |
| 66 | `regime` | enum | — | (excluded) | Current market regime label |

### Regime Flags (one-hot from regime)

| # | Feature | Type | Corr | Signal | Description |
|---|---------|------|------|--------|-------------|
| 67 | `regime_high_conviction` | bool | 0.000 | 🔴 | High conviction trend regime |
| 68 | `regime_smart_divergence` | bool | 0.000 | 🔴 | Smart money divergence regime |
| 69 | `regime_pre_event` | bool | -0.019 | 🔴 | Pre-event positioning regime |
| 70 | `regime_low_signal` | bool | +0.015 | 🔴 | Low signal / choppy regime |
| 71 | `regime_info_cascade` | bool | +0.040 | 🟠 | Information cascade regime |

### Fear & Greed (from Alternative.me)

| # | Feature | Type | Corr | Signal | Description |
|---|---------|------|------|--------|-------------|
| 72 | `fear_greed_index` | float | +0.012 | 🔴 | Crypto Fear & Greed (-1 to +1) |

---

## Engineered Features (112) — from feature_engineering.py

### Lag Features (21)

Previous window values for 7 key signals × 3 lags (t-1, t-2, t-3).

| # | Feature | Corr | Signal | Base Signal |
|---|---------|------|--------|-------------|
| 73 | `btc_momentum_1m_lag1` | -0.051 | 🟠 | btc_momentum_1m |
| 74 | `btc_momentum_1m_lag2` | +0.017 | 🔴 | btc_momentum_1m |
| 75 | `btc_momentum_1m_lag3` | -0.019 | 🔴 | btc_momentum_1m |
| 76 | `btc_momentum_5m_lag1` | -0.017 | 🔴 | btc_momentum_5m |
| 77 | `btc_momentum_5m_lag2` | -0.051 | 🟠 | btc_momentum_5m |
| 78 | `btc_momentum_5m_lag3` | -0.045 | 🟠 | btc_momentum_5m |
| 79 | `orderbook_imbalance_lag1` | +0.046 | 🟠 | orderbook_imbalance |
| 80 | `orderbook_imbalance_lag2` | +0.031 | 🟠 | orderbook_imbalance |
| 81 | `orderbook_imbalance_lag3` | +0.047 | 🟠 | orderbook_imbalance |
| 82 | `cvd_1m_lag1` | -0.099 | 🟢⭐ | cvd_1m |
| 83 | `cvd_1m_lag2` | -0.047 | 🟠 | cvd_1m |
| 84 | `cvd_1m_lag3` | +0.000 | 🔴 | cvd_1m |
| 85 | `cvd_5m_lag1` | -0.097 | 🟢 | cvd_5m |
| 86 | `cvd_5m_lag2` | +0.046 | 🟠 | cvd_5m |
| 87 | `cvd_5m_lag3` | -0.084 | 🟢 | cvd_5m |
| 88 | `funding_rate_lag1` | -0.023 | 🔴 | funding_rate |
| 89 | `funding_rate_lag2` | -0.030 | 🔴 | funding_rate |
| 90 | `funding_rate_lag3` | -0.033 | 🟠 | funding_rate |
| 91 | `depth_imbalance_5_lag1` | +0.041 | 🟠 | depth_imbalance_5 |
| 92 | `depth_imbalance_5_lag2` | +0.024 | 🔴 | depth_imbalance_5 |
| 93 | `depth_imbalance_5_lag3` | +0.043 | 🟠 | depth_imbalance_5 |

### Rolling Mean (14)

3-period and 5-period rolling averages of 7 key signals.

| # | Feature | Corr | Signal | Base Signal |
|---|---------|------|--------|-------------|
| 94 | `btc_momentum_1m_rmean3` | -0.074 | 🟡 | btc_momentum_1m |
| 95 | `btc_momentum_1m_rmean5` | -0.081 | 🟢 | btc_momentum_1m |
| 96 | `btc_momentum_5m_rmean3` | -0.096 | 🟢⭐ | btc_momentum_5m |
| 97 | `btc_momentum_5m_rmean5` | -0.076 | 🟡 | btc_momentum_5m |
| 98 | `orderbook_imbalance_rmean3` | +0.064 | 🟡 | orderbook_imbalance |
| 99 | `orderbook_imbalance_rmean5` | +0.040 | 🟠 | orderbook_imbalance |
| 100 | `cvd_1m_rmean3` | -0.109 | 🟢 | cvd_1m |
| 101 | `cvd_1m_rmean5` | -0.084 | 🟢 | cvd_1m |
| 102 | `cvd_5m_rmean3` | -0.076 | 🟡 | cvd_5m |
| 103 | `cvd_5m_rmean5` | -0.121 | 🟢⭐ | cvd_5m |
| 104 | `funding_rate_rmean3` | -0.030 | 🔴 | funding_rate |
| 105 | `funding_rate_rmean5` | -0.033 | 🟠 | funding_rate |
| 106 | `depth_imbalance_5_rmean3` | +0.063 | 🟡 | depth_imbalance_5 |
| 107 | `depth_imbalance_5_rmean5` | +0.036 | 🟠 | depth_imbalance_5 |

### Rolling Std (14)

3-period and 5-period rolling standard deviations (volatility of signals).

| # | Feature | Corr | Signal | Base Signal |
|---|---------|------|--------|-------------|
| 108 | `btc_momentum_1m_rstd3` | +0.034 | 🟠 | btc_momentum_1m |
| 109 | `btc_momentum_1m_rstd5` | -0.002 | 🔴 | btc_momentum_1m |
| 110 | `btc_momentum_5m_rstd3` | -0.011 | 🔴 | btc_momentum_5m |
| 111 | `btc_momentum_5m_rstd5` | -0.065 | 🟡 | btc_momentum_5m |
| 112 | `orderbook_imbalance_rstd3` | -0.072 | 🟡 | orderbook_imbalance |
| 113 | `orderbook_imbalance_rstd5` | -0.067 | 🟡 | orderbook_imbalance |
| 114 | `cvd_1m_rstd3` | -0.008 | 🔴 | cvd_1m |
| 115 | `cvd_1m_rstd5` | +0.010 | 🔴 | cvd_1m |
| 116 | `cvd_5m_rstd3` | +0.039 | 🟠 | cvd_5m |
| 117 | `cvd_5m_rstd5` | +0.004 | 🔴 | cvd_5m |
| 118 | `funding_rate_rstd3` | +0.018 | 🔴 | funding_rate |
| 119 | `funding_rate_rstd5` | -0.047 | 🟠 | funding_rate |
| 120 | `depth_imbalance_5_rstd3` | -0.062 | 🟡 | depth_imbalance_5 |
| 121 | `depth_imbalance_5_rstd5` | -0.050 | 🟠 | depth_imbalance_5 |

### Acceleration (4)

First-difference of momentum/flow signals (rate of change).

| # | Feature | Corr | Signal | Description |
|---|---------|------|--------|-------------|
| 122 | `btc_momentum_1m_accel` | -0.030 | 🔴 | Change in 1-min momentum |
| 123 | `btc_momentum_5m_accel` | -0.073 | 🟡 | Change in 5-min momentum |
| 124 | `cvd_1m_accel` | +0.031 | 🟠 | Change in 1-min CVD |
| 125 | `orderbook_imbalance_accel` | -0.001 | 🔴 | Change in orderbook imbalance |

### Interaction Features (4)

Cross-products of key signals for non-linear relationships.

| # | Feature | Corr | Signal | Formula |
|---|---------|------|--------|---------|
| 126 | `momentum_x_orderbook` | +0.007 | 🔴 | btc_momentum_5m × orderbook_imbalance |
| 127 | `sentiment_x_activity` | 0.000 | 🔴 | tier1_sentiment_5m × social_activity_level |
| 128 | `cvd_x_momentum` | +0.007 | 🔴 | cvd_5m × btc_momentum_5m |
| 129 | `whale_x_momentum` | -0.084 | 🟢⭐ | whale_intensity × btc_momentum_5m |

### Polymarket-Specific (15)

Market microstructure features — the theoretical edge source.

| # | Feature | Corr | Signal | Description |
|---|---------|------|--------|-------------|
| 130 | `poly_spread` | float | 0.000 | 🔴 | Overround: |UP + DOWN - 1| |
| 131 | `poly_implied_up` | float | 0.000 | 🔴 | Implied UP probability from prices |
| 132 | `poly_up_cheapness` | float | 0.000 | 🔴 | 0.5 - UP price (positive = cheap) |
| 133 | `poly_down_cheapness` | float | 0.000 | 🔴 | 0.5 - DOWN price (positive = cheap) |
| 134 | `poly_price_deviation` | float | 0.000 | 🔴 | UP price - DOWN price |
| 135 | `poly_cheaper_side_is_up` | bool | 0.000 | 🔴 | 1.0 if UP is cheaper side |
| 136 | `poly_mispricing_magnitude` | float | -0.004 | 🔴 | |UP price - 0.50| |
| 137 | `poly_depth_imbalance` | float | -0.019 | 🔴 | (UP_bid - DOWN_bid) / total |
| 138 | `heuristic_prob_up` | float | +0.025 | 🔴 | Heuristic model P(up) output |
| 139 | `prob_distance_from_half` | float | -0.004 | 🔴 | |heuristic_prob - 0.50| |
| 140 | `edge_vs_poly` | float | +0.025 | 🔴 | heuristic_prob - poly UP price |
| 141 | `btc_move_since_open_pct` | float | -0.113 | 🟢 | BTC move since window open (%) |
| 142 | `lag_signal_up` | float | -0.100 | 🟢 | Upward move × UP cheapness |
| 143 | `lag_signal_down` | float | +0.081 | 🟢 | Downward move × DOWN cheapness |
| 144 | `oracle_lag_signal` | float | -0.113 | 🟢 | Combined lag signal (up - down) |

### Event Ontology / Window Features (15)

Aggregate statistics from the Temporal-Causal Event Ontology per 5-min window.

| # | Feature | Corr | Signal | Description |
|---|---------|------|--------|-------------|
| 145 | `window_n_events` | int | +0.039 | 🟠 | Total events in trading window |
| 146 | `window_n_bullish` | int | -0.020 | 🔴 | Bullish events in window |
| 147 | `window_n_bearish` | int | -0.006 | 🔴 | Bearish events in window |
| 148 | `window_signal_coherence` | float | -0.002 | 🔴 | Signal agreement (0–1) |
| 149 | `window_n_confirms` | int | -0.044 | 🟠 | Cross-signal confirmations |
| 150 | `window_n_contradicts` | int | -0.023 | 🔴 | Cross-signal contradictions |
| 151 | `window_confirms_ratio` | float | -0.047 | 🟠 | Confirms / (confirms + contradicts) |
| 152 | `window_whale_preceded_move` | bool | +0.038 | 🟠 | Whale activity preceded price move |
| 153 | `window_sentiment_preceded_move` | bool | +0.036 | 🟠 | Sentiment preceded price move |
| 154 | `window_max_magnitude` | float | +0.049 | 🟠 | Largest event magnitude in window |
| 155 | `window_weighted_direction` | float | +0.017 | 🔴 | Confidence-weighted direction score |
| 156 | `window_unique_sources` | int | +0.007 | 🔴 | Unique data sources in window |
| 157 | `window_causal_chain_length` | int | -0.009 | 🔴 | Longest causal chain in window |
| 158 | `momentum_sentiment_alignment` | float | 0.000 | 🔴 | Momentum + sentiment direction match |

### Calendar / Time Features (8)

Cyclical time encoding + trading session flags.

| # | Feature | Corr | Signal | Description |
|---|---------|------|--------|-------------|
| 159 | `hour_sin` | float | -0.050 | 🟠 | sin(2π × hour/24) — cyclical hour |
| 160 | `hour_cos` | float | +0.030 | 🔴 | cos(2π × hour/24) — cyclical hour |
| 161 | `dow_sin` | float | -0.001 | 🔴 | sin(2π × weekday/7) — cyclical day |
| 162 | `dow_cos` | float | +0.027 | 🔴 | cos(2π × weekday/7) — cyclical day |
| 163 | `minute_of_day` | int | -0.005 | 🔴 | Minute of day (0–1439) |
| 164 | `is_us_market_hours` | bool | +0.029 | 🔴 | 9:30 AM – 4:00 PM ET |
| 165 | `is_asia_session` | bool | +0.024 | 🔴 | 8:00 PM – 4:00 AM ET (Asia open) |
| 166 | `is_europe_session` | bool | -0.034 | 🟠 | 3:00 AM – 11:30 AM ET (Europe open) |

### Metadata (1)

| # | Feature | Corr | Signal | Description |
|---|---------|------|--------|-------------|
| 167 | `data_version` | int | -0.007 | 🔴 | Data pipeline version (1 or 2) |
| — | `event_urgency` | float | -0.033 | 🟠 | Urgency score for upcoming macro event |

---

## Signal Summary

| Category | Count | % | Description |
|----------|-------|---|-------------|
| 🟢 Top 20 | 20 | 12% | |corr| > 0.079 — clear signal |
| 🟡 Borderline | 15 | 9% | |corr| 0.05–0.079 — possible signal |
| 🟠 Weak | 44 | 27% | |corr| 0.03–0.05 — marginal |
| 🔴 Noise | 86 | 52% | |corr| < 0.03 — no detectable signal |

## PySR-Selected Features (⭐)

These 6 features appear repeatedly in PySR's best symbolic equations:

1. **`spot_move_magnitude`** — BTC price move since window open
2. **`cvd_5m_rmean5`** — 5-min CVD rolling average
3. **`cvd_1m_lag1`** — 1-min CVD lagged one window
4. **`orderbook_inertia_ms`** — Chainlink→Polymarket order book lag
5. **`whale_x_momentum`** — Whale activity × BTC momentum
6. **`btc_momentum_5m_rmean3`** — 5-min momentum rolling average
7. **`liq_short_volume_5m`** — Short liquidation volume (used in NN distillation)

## Feature Families

| Family | Features | Top Ranked | Dominant Signal? |
|--------|----------|-----------|-----------------|
| **CVD** | 20 (raw + lags + rolling) | 8 in top 20 | ✅ Strongest |
| **BTC Momentum** | 16 | 4 in top 20 | ✅ Strong |
| **Polymarket/Lag** | 20 | 4 in top 20 | ✅ Strong |
| **Orderbook** | 14 | 1 in top 20 | 🟡 Moderate |
| **Whale/On-chain** | 10 | 1 in top 20 | 🟡 Moderate |
| **Derivatives** | 14 | 1 in top 20 | 🟠 Weak |
| **Social/Sentiment** | 10 | 0 in top 20 | 🔴 None |
| **Calendar/Time** | 8 | 0 in top 20 | 🔴 None |
| **Cross-Asset** | 4 | 0 in top 20 | 🔴 None |
| **Mempool/Lightning** | 6 | 0 in top 20 | 🔴 None |
| **Graph/Regime** | 10 | 0 in top 20 | 🔴 None |
| **Ontology/Window** | 15 | 0 in top 20 | 🔴 None |
| **Engineered (Poly)** | 15 | 4 in top 20 | ✅ Strong (lag signals) |

---

*Generated from live training data (572 unique 5-min windows) and SymTorch/PySR symbolic regression analysis.*

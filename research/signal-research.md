# Signal Research — Polymarket BTC 5-Min Prediction Model

## Executive Summary

Analysis of 56 winning trades (77% win rate, $1,811 peak from $1,000) reveals that
**the edge is NOT BTC direction prediction — it's Polymarket price lag arbitrage.**

## Resolution Mechanism

Polymarket BTC 5-min markets:
- Binary: "Will BTC be higher at window_end than window_start?"
- Resolves via **UMA Optimistic Oracle** (Chainlink BTC/USD Data Stream)
- UP + DOWN tokens always sum to ~$1.00 (zero vig)
- Prices trade in a tight range: **0.485 to 0.515**
- New market every 5 minutes

## Empirical Findings (56 Trades)

### Market Efficiency
| Metric | Value |
|--------|-------|
| Mean UP price | 0.500 |
| Mean DOWN price | 0.500 |
| UP + DOWN sum | 1.000 (zero vig) |
| Price range | 0.485 - 0.515 |
| Mean deviation from 0.50 | 0.0043 |

### The Actual Edge
| Strategy | Win Rate |
|----------|----------|
| Random (coin flip) | 50% |
| Buy cheaper Poly side (contrarian) | **65%** |
| Our heuristic model | **77%** |
| When model neutral (P=0.50) | **86%** |
| When model bullish (P=0.62) | **82%** |
| When Poly deviation ≤ 0.005 | **84%** |
| When Poly deviation > 0.010 | **40%** |

### Key Insight
The highest win rate (86%) came when the heuristic had NO strong opinion (P=0.50) 
but the Polymarket price was slightly off. This means the model's "prediction"
wasn't driving wins — the market microstructure was.

## Signal Hierarchy

### TIER 1 — Primary (Market Microstructure)
These signals exploit the lag between Binance spot and Polymarket CLOB.

1. **`poly_price_deviation`**: UP - DOWN price. When UP > DOWN, DOWN is cheap (buy DOWN).
   - This alone: 65% win rate
   - WEIGHT: HIGH

2. **`btc_move_since_window_open`**: How much BTC moved since the 5-min window started.
   - If BTC up +0.3% but Poly UP still at 0.505 → mispricing
   - WEIGHT: HIGH
   - Proxy: `btc_momentum_5m` (approximates intra-window move)

3. **`oracle_lag_signal`**: Interaction of BTC direction × Poly cheapness.
   - BTC moving up AND UP side is cheap = strong buy signal
   - WEIGHT: VERY HIGH (combines the two strongest individual signals)

4. **`time_remaining`**: Seconds left in window.
   - With <60s left, BTC price is nearly locked — any Poly mispricing is pure alpha
   - With >240s left, much more uncertainty
   - WEIGHT: MODERATE (mainly as interaction term)

5. **`poly_mispricing_magnitude`**: |UP - 0.50| 
   - Bigger deviation = more confident signal
   - When |dev| ≤ 0.005: 84% win rate; when |dev| > 0.01: 40% win rate
   - ⚠️ INVERSE RELATIONSHIP: More mispricing = WORSE results (market knows something)
   - WEIGHT: HIGH (but as a FILTER, not a directional signal)

### TIER 2 — Supporting (Direction Confirmation)
These help confirm which way BTC is likely heading in the next few minutes.

6. **`orderbook_imbalance`**: Binance spot orderbook asymmetry
7. **`depth_imbalance_5/20`**: Binance depth at 5/20 levels  
8. **`btc_momentum_1m`**: Very short-term momentum
9. **`cvd_5m`**: Cumulative volume delta (buy vs sell pressure)

### TIER 3 — Conditional (Regime-Dependent)
Only useful in specific market conditions.

10. **`tier1_sentiment_5m`**: Social media sentiment
11. **`funding_rate`**: Derivatives positioning (contrarian)
12. **`liq_ratio`**: Liquidation flow
13. **`put_call_ratio`**: Options market sentiment
14. **`whale_intensity`**: Large transaction activity

## Model Architecture Recommendations

### Best Approach: Ensemble
1. **XGBoost on Tabular Features**: Best for the microstructure signals (price deviations, 
   orderbook features). These are tabular, not sequential.
2. **LSTM on Price Sequence**: Feed the last N 1-minute candles for temporal pattern detection.
3. **Ensemble**: Weighted combination where XGBoost dominates (70-80% weight) since 
   the primary signal is microstructure, not temporal.

### Critical Design Decision
The model should be framed as:
- **Input**: Current Polymarket prices + Binance spot state + all features
- **Output**: P(UP wins) = P(BTC at window_end > BTC at window_start)
- **Decision**: Buy whichever side has `our_P > poly_price + min_edge`

The model's P(UP) should be calibrated against the **Polymarket price**, not against 0.50.
A model that says P(UP)=0.55 when Poly UP = 0.50 has a 5% edge.
A model that says P(UP)=0.55 when Poly UP = 0.54 has only a 1% edge.

### QA Baselines
Any model must beat ALL of these:
1. **Random**: 50% accuracy
2. **Contrarian** (buy cheaper Poly side): ~65% accuracy
3. **Historical heuristic**: ~77% accuracy (though likely overstated due to small sample + luck)

Realistically, beating the 65% contrarian baseline would be a significant achievement.
Beating 60% with statistical significance (p < 0.05) would be deployable.

## Data Requirements
- Need 2,000+ unique 5-min windows (independent samples, one per window)
- Currently collecting ~12 samples/hour × 24h = ~288/day
- 7 days minimum for viable training data
- All 70 features now being saved correctly (fixed 2026-02-23)

## Anti-Patterns to Avoid
1. **Don't overfit on momentum**: btc_momentum_1m flips sign every 30s on $10 BTC moves
2. **Don't trust extreme P(up) values**: Heuristic produced 0.006 and 0.994 which were always wrong
3. **Don't size bets based on uncalibrated P(up)**: Kelly criterion amplified bad signals to $135 bets
4. **Don't change the model live**: Backtest first, deploy second (learned the hard way)

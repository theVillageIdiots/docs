# Execution Engine

## Three Trading Paths

| Path | File | Trigger | Timing | Description |
|------|------|---------|--------|-------------|
| **A: Heuristic** | `execution/paper_trader.py` | Every 5-min window | Normal | Standard prediction + bet |
| **B: Snipe Mode** | `execution/paper_trader.py` | Final 45 seconds | Late-round | Waits for BTC direction to establish, then bets with 70/30 momentum blend |
| **C: Sum-Arb** | `execution/sum_arb.py` | UP_ask + DOWN_ask < $0.97 | Opportunistic | Guaranteed profit when both sides sum below $1 |

## Key Execution Parameters

| Parameter | Value | Notes |
|-----------|-------|-------|
| Bet size | 2% of bankroll | Fixed (Kelly disabled) |
| Min edge | Clears taker fees + threshold | Fees peak 1.56% at 50% prob |
| Circuit breaker | 8 losses → 2-min cooldown | 1.5× escalation, 10-min max |
| Snipe window | Final 45 seconds | 70% heuristic / 30% momentum |
| Sum-arb threshold | < $0.97 | Conservative for Python latency |
| Daily drawdown halt | 15% | Hard stop |
| ARB cap | $500 | Max per-side arb bet |

## Other Execution Components

| Component | File | Description |
|-----------|------|-------------|
| `StrategyRouter` | `execution/strategy_router.py` | Routes between trading strategies |
| `OrderManager` | `execution/order_manager.py` | Order placement and tracking |
| `DivergenceTrigger` | `execution/divergence_trigger.py` | Lead-lag divergence detection (observe mode) |
| `MarketMaker` | `execution/market_maker.py` | Market making logic (not active) |

## Router Interface

```python
def select_path(envelope: PredictionEnvelopeV1, ctx: dict) -> Literal["A", "B", "C", "PASS"]
```

## Production Model Logic

The heuristic model (currently in production) uses simple threshold rules:

```python
if momentum > threshold AND orderbook confirms:
    P(up) = 0.62
elif momentum < -threshold AND orderbook confirms:
    P(up) = 0.38
else:
    P(up) = 0.50
```

- **Output:** 3 discrete values: 0.38, 0.50, 0.62
- **Performance:** 54.5–55.0% win rate, $1,610+ profit, 516+ trades
- **Why it works:** Captures mean-reversion + market microstructure lag

## ML Model Gate (QA Framework)

```python
# training/qa_framework.py
Model loads ONLY if:
  - is_useful = True   (accuracy > baseline + margin)
  - is_overfitted = False  (train-test gap < threshold)
  - p_value < 0.05     (statistically significant)
  - EV per trade > 0   (positive expected value after fees)
```

All ML models (XGBoost, stacking ensemble) have failed this gate to date. See [research/signal-research.md](../research/signal-research.md) for analysis.
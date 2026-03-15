# Research

Signal research, feature analysis, and empirical findings.

## Contents

| Document | Description |
|----------|-------------|
| [signal-research.md](signal-research.md) | Empirical analysis of 56 trades — the actual edge is market microstructure, not BTC prediction |
| [features.md](features.md) | Complete 165-feature list with correlations and signal ratings |

## Key Finding

The edge is **not** BTC direction prediction — it's **Polymarket price lag arbitrage**.

The highest win rate (86%) came when the heuristic had NO strong opinion (P=0.50) but the Polymarket price was slightly off. The model's "prediction" wasn't driving wins — the market microstructure was.

See [signal-research.md](signal-research.md) for the full analysis.
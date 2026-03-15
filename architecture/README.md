# Architecture

System design documentation for the Polymarket BTC 5-Min Prediction Bot.

## Contents

| Document | Description |
|----------|-------------|
| [system-overview.md](system-overview.md) | High-level system diagram, runtime environment, layer breakdown |
| [data-feeds.md](data-feeds.md) | All 24 data feeds — protocols, frequency, data types |
| [feature-engineering.md](feature-engineering.md) | 165-feature pipeline: base features, transforms, signal ratings |
| [execution-engine.md](execution-engine.md) | Three trading paths, parameters, strategy routing |
| [knowledge-graph.md](knowledge-graph.md) | Memgraph schema, node/edge types, temporal-causal event ontology |
| [threat-model.md](threat-model.md) | STRIDE threat analysis, mitigations, residual risks |
| [adr/](adr/) | Architecture Decision Records |

## System Layers

```
┌─────────────────────────────────────────────────────────────────┐
│                     DATA LAYER (24 feeds)                       │
│  Binance WS · CVD · Deribit · Pyth · Chainlink · Polymarket    │
│  X/Twitter · News · Whale Alerts · Mempool · Fear/Greed · Econ │
├─────────────────────────────────────────────────────────────────┤
│                   KNOWLEDGE GRAPH (Memgraph)                    │
│  In-process KnowledgeGraph + Memgraph Bolt write-through        │
│  Temporal-Causal Event Ontology · Entity Resolution · GLiNER2   │
├─────────────────────────────────────────────────────────────────┤
│                   FEATURE ENGINEERING                            │
│  Base features → Lags → Rolling stats → Interactions → Poly     │
│  53 base + 112 engineered = 165 total features                  │
├─────────────────────────────────────────────────────────────────┤
│                   MODEL LAYER                                   │
│  Heuristic (PRODUCTION) · XGBoost · Stacking Ensemble · LSTM   │
│  SymTorch/PySR symbolic distillation (analysis)                 │
├─────────────────────────────────────────────────────────────────┤
│                   EXECUTION ENGINE                              │
│  Path A: Heuristic · Path B: Snipe Mode · Path C: Sum-Arb      │
│  Paper Trader · Kelly Sizing (disabled) · Circuit Breaker       │
├─────────────────────────────────────────────────────────────────┤
│                   DASHBOARD & API                               │
│  FastAPI :8080 · Next.js :3000 · Cloudflare Tunnel              │
└─────────────────────────────────────────────────────────────────┘
```
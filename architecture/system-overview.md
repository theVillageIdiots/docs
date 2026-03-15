# System Overview

*Polymarket BTC 5-Min Prediction Bot — Updated 2026-02-26*

## Architecture Diagram

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

## Three-Path Execution Model

```
                    ┌──────────────────────┐
                    │   KNOWLEDGE GRAPH    │
                    │    (NetworkX)        │
                    └──────────┬───────────┘
                               │
              ┌────────────────┼────────────────┐
              │                │                │
     ┌────────▼──────┐ ┌──────▼───────┐ ┌──────▼───────┐
     │  FAST PATH    │ │ MEDIUM PATH  │ │  SLOW PATH   │
     │  <100ms       │ │  1-60s       │ │  minutes+    │
     │               │ │              │ │              │
     │ BTC momentum  │ │ Graph signals│ │ Regime       │
     │ OB imbalance  │ │ ML model     │ │ detection    │
     │ Volume spikes │ │ Mispricing   │ │ Strategy     │
     │               │ │ detection    │ │ rotation     │
     │ → Emergency   │ │              │ │              │
     │   trades only │ │ → Primary    │ │ → Config     │
     │   (veto/go)   │ │   trading    │ │   adjustment │
     └────────┬──────┘ └──────┬───────┘ └──────┬───────┘
              └───────────────┼────────────────┘
                              │
                    ┌─────────▼─────────┐
                    │  STRATEGY ROUTER  │
                    └─────────┬─────────┘
                              │
                    ┌─────────▼─────────┐
                    │  RISK MANAGEMENT  │
                    │  Circuit breaker  │
                    │  Kelly sizing     │
                    │  Anti-copy-trade  │
                    └─────────┬─────────┘
                              │
                    ┌─────────▼─────────┐
                    │  ORDER EXECUTION  │
                    │  Polymarket CLOB  │
                    └───────────────────┘
```

## Runtime Environment

| Component | Value |
|-----------|-------|
| Language | Python 3.12 |
| Package manager | `uv` venv |
| Logging | `loguru` |
| Platform | macOS ARM64 |
| Graph DB | Memgraph (Docker, Bolt protocol) — in-memory, sub-ms queries |
| Bot process | `main.py` on `:8080` (FastAPI) |
| Dashboard | Next.js on `:3000`, HTML dashboard at `:8080/dashboard` |

## Data Flow

```
X/Twitter ─────┐
Whale Alert ───┤
Binance WS ────┤     ┌──────────┐     ┌────────────┐     ┌───────────┐
Coinbase WS ───┼────▶│  GRAPH   │────▶│  SIGNALS   │────▶│ STRATEGY  │
Econ Calendar ─┤     │  STORE   │     │ (3 paths)  │     │  ROUTER   │
Polymarket ────┤     └──────────┘     └────────────┘     └─────┬─────┘
Chainlink ─────┘           │                                   │
                           │         ┌────────────┐            │
                           └────────▶│   RISK     │◀───────────┘
                                     │  MANAGER   │
                                     └──────┬─────┘
                                            │
                                     ┌──────▼──────┐
                                     │  FEEDBACK   │
                                     │ (resolved   │
                                     │  contracts  │
                                     │  → retrain) │
                                     └─────────────┘
```

## Regime-Strategy Mapping

| Regime | Trigger | Strategy | Position Sizing |
|--------|---------|----------|-----------------|
| HIGH_CONVICTION_TREND | Whale + sentiment + momentum aligned | Aggressive directional | 1.5× Kelly |
| INFORMATION_CASCADE | ≥3 tier-1 posts in 5 min | Momentum scalp, fast in/out | 1.2× Kelly |
| SMART_MONEY_DIVERGENCE | Whale flows opposite sentiment | Contrarian (follow whales) | 0.8× Kelly |
| PRE_EVENT_POSITIONING | Macro event <2 hours | Defensive MM, reduce exposure | 0.5× Kelly |
| LOW_SIGNAL | No strong graph signals | Passive market making only | 0.3× Kelly |
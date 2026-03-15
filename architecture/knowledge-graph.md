# Knowledge Graph & Ontology

## Graph Database: Memgraph

| Property | Value |
|----------|-------|
| Protocol | Bolt (neo4j Python driver) |
| Storage | In-memory (sub-ms queries) |
| Write path | Async via `asyncio.to_thread()` — sync writes caused all API hangs |
| Flush | Queue-based, batched every 500ms |

## Node Types

| Node | File | Fields | Description |
|------|------|--------|-------------|
| `Asset` | `graph/schema.py` | id, name, price, volume, market_cap, correlations | Tradeable asset (BTC, ETH) |
| `Person` | `graph/schema.py` | id, name, handles, role, tier, influence_score, accuracy, bias | Influencer/whale/analyst |
| `Organization` | `graph/schema.py` | id, name, org_type, wallets, people | Exchange, fund, company |
| `Wallet` | `graph/schema.py` | id, chain, type, owner, balance | On-chain address |
| `Post` | `graph/schema.py` | id, platform, author, content, sentiment, engagement | Tweet/post |
| `Transaction` | `graph/schema.py` | id, chain, from/to, amount, type | On-chain transfer |
| `MarketEvent` | `graph/schema.py` | id, name, type, timestamp, impact | FOMC, CPI, etc. |
| `SpotPrice` | `graph/schema.py` | timestamp_ms, price, volume_1s | Binance tick |
| `OracleUpdate` | `graph/schema.py` | timestamp_ms, price, propagation_delay_ms | Chainlink report |
| `MarketState` | `graph/schema.py` | timestamp_ms, yes/no price, imbalance, depth, spread | Polymarket CLOB state |
| `PriceDivergence` | `graph/schema.py` | spot/oracle/poly prices, divergence_pct, lag breakdown | Lead-lag divergence |
| `PolymarketContract` | `graph/schema.py` | id, question, token_ids, bid/ask, resolution | 5-min market contract |
| `Exchange` | Memgraph init | name, type | Binance, Polymarket, Chainlink |

## Relationship Types (Edges)

| Relationship | From | To | Properties | Description |
|-------------|------|-----|-----------|-------------|
| `LISTS` | Exchange | Asset | — | Exchange lists asset |
| `REPORTS` | Exchange(Chainlink) | Asset | — | Oracle reports on asset |
| `TICKED` | Exchange(Binance) | SpotPrice | — | Price tick event |
| `PUBLISHED` | Exchange(Chainlink) | OracleUpdate | — | Oracle update published |
| `OBSERVED` | Exchange(Polymarket) | MarketState | — | Market state observed |
| `PROPAGATED_TO` | SpotPrice | OracleUpdate | delay_ms | Lead-lag: spot → oracle |
| `REFLECTED_IN` | OracleUpdate | MarketState | delay_ms | Lead-lag: oracle → market |
| `POSTED` | Person | Post | — | Person authored post |
| `MENTIONS` | Post | Asset | — | Post mentions asset |
| `SENT` | Wallet | Transaction | — | Wallet sent tx |
| `RECEIVED_BY` | Transaction | Wallet | — | Wallet received tx |
| `{EventRelationType}` | Event | Event | lag_ms | Causal/temporal event edges |

## Temporal-Causal Event Ontology

Source: `graph/event_ontology.py`

### Three Components

1. **EventFactory** — Creates typed Event objects from raw feed data
2. **CausalEngine** — Infers relationships between events
3. **WindowManager** — Manages 5-min trading windows with aggregate features

### Event Supertype Hierarchy

```
Event (base)
├── PriceEvent        — Spot/futures price movement (Binance)
├── FlowEvent         — Whale/on-chain flow (whale alerts)
├── SentimentEvent    — Tweet, news, social post (X stream)
├── DerivativeEvent   — Options, funding, OI changes (Deribit, Binance)
├── BookEvent         — Order book state (Binance, Polymarket)
├── OracleEvent       — Chainlink oracle report
├── MarketEvent2      — Polymarket state
└── MempoolEvent      — Mempool/on-chain state
```

**Event Directions:** `BULLISH` | `BEARISH` | `NEUTRAL`

### Event Relationship Types

| Type | Meaning | Example |
|------|---------|---------|
| `PRECEDED_BY` | Temporal ordering | Price event after whale flow |
| `LIKELY_CAUSED` | Causal inference | Large exchange deposit → price drop |
| `CONFIRMS` | Directional agreement | Bearish tweet + orderbook selling |
| `CONTRADICTS` | Directional disagreement | Bullish tweet + bearish flow |
| `RESOLVES_TO` | Outcome linkage | Window prediction → actual result |
| `CO_OCCURRED` | Same timeframe | Multiple events in same 5-min window |

### TradingWindow Fields

| Field | Type | Description |
|-------|------|-------------|
| `n_events` | int | Total events in window |
| `n_bullish` / `n_bearish` | int | Directional event counts |
| `signal_coherence` | float | Agreement among signals (0–1) |
| `n_confirms` / `n_contradicts` | int | Cross-signal confirmation count |
| `whale_preceded_move` | bool | Whale activity before price move |
| `sentiment_preceded_move` | bool | Sentiment signal before price move |
| `max_event_magnitude` | float | Largest event magnitude |
| `weighted_direction_score` | float | Confidence-weighted direction |
| `earliest_signal_direction` | str | First signal direction in window |

## In-Process Knowledge Graph

Source: `graph/store.py — KnowledgeGraph class`

- Primary in-process store for hot path (trading loop)
- Maintains rolling buffers of recent data
- Produces `GraphSnapshot` for ML features
- Writes through to Memgraph for persistence

## Entity Resolution

```
graph/entity_resolver.py — resolves entities across data sources
graph/embeddings.py — vector embeddings for similarity
```
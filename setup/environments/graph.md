# polybot-graph-3.12

**Python:** 3.12
**Directory:** `graph/`
**Purpose:** Knowledge graph — event ontology, entity resolution, embedding generation, and Memgraph (in-memory graph DB) integration via Bolt protocol.

## Key Files

| File | Purpose |
|---|---|
| `store.py` | Primary in-process NetworkX knowledge graph |
| `memgraph_store.py` | Durable Memgraph backing store via Bolt |
| `embeddings.py` | Entity embedding generation |
| `entity_resolver.py` | Cross-feed entity deduplication |
| `event_ontology.py` | Ontology definitions (Event, Asset, Wallet, etc.) |
| `schema.py` | Graph schema / node type registry |

## Dependencies

```
networkx>=3.2
neo4j>=5.0.0
numpy>=1.26.0
pandas>=2.1.0
openai>=1.0.0
anthropic>=0.18.0
rich>=13.0.0
loguru>=0.7.0
python-dotenv>=1.0.0
pyyaml>=6.0
```

## Create

```bash
pyenv virtualenv 3.12 polybot-graph-3.12
pyenv activate polybot-graph-3.12
pip install --upgrade pip
pip install \
  "networkx>=3.2" \
  "neo4j>=5.0.0" \
  "numpy>=1.26.0" \
  "pandas>=2.1.0" \
  "openai>=1.0.0" \
  "anthropic>=0.18.0" \
  "rich>=13.0.0" \
  "loguru>=0.7.0" \
  "python-dotenv>=1.0.0" \
  "pyyaml>=6.0"
```

## Pin to Directory

```bash
cd polymarket-bot/graph
pyenv local polybot-graph-3.12
```

## External Service: Memgraph

Memgraph is a separate process — it does **not** install via pip. The `neo4j` Python driver connects to it over Bolt.

```bash
# Start Memgraph (Docker)
docker run -p 7687:7687 -p 7444:7444 memgraph/memgraph-platform

# Or via brew
brew install memgraph/memgraph/memgraph
```

Default connection: `bolt://localhost:7687` (no auth in dev mode).

## Clash Notes

- `neo4j` driver and `networkx` are entirely independent — no conflicts.
- `openai` uses `httpx` internally; `anthropic` also uses `httpx`. They share the same version requirement and coexist cleanly.
- This is the **only module** requiring `neo4j` — strongest argument for an isolated env vs. master.

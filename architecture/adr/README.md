# Architecture Decision Records (ADRs)

This directory contains Architecture Decision Records — lightweight documentation of significant technical decisions made during the project.

## Log

| ADR | Title | Status | Date |
|-----|-------|--------|------|
| [ADR-001](adr-001-networkx-over-neo4j.md) | NetworkX over Neo4j for initial graph | Accepted | 2026-02 |
| [ADR-002](adr-002-xgboost-before-deep-learning.md) | XGBoost before deep learning | Accepted | 2026-02 |
| [ADR-003](adr-003-env-vars-for-secrets.md) | Environment variables for secrets | Accepted | 2026-02 |
| [ADR-004](adr-004-three-path-architecture.md) | Three-path execution architecture | Accepted | 2026-02 |

## Format

Each ADR covers:
- **Decision** — what was decided
- **Context** — why this decision was needed
- **Tradeoff** — what was given up
- **Upgrade trigger** — when to revisit

---

### ADR-001: NetworkX over Neo4j for initial graph

**Decision:** Use in-memory NetworkX graph
**Context:** Need sub-second query latency, <100K nodes initially
**Tradeoff:** Limited to single-process, no persistence. But zero ops overhead.
**Upgrade trigger:** When graph exceeds 1M nodes or we need persistence

---

### ADR-002: XGBoost before deep learning

**Decision:** Start with XGBoost for the prediction model
**Context:** Small training dataset initially, need interpretability
**Tradeoff:** May miss non-linear graph structure that GNNs capture
**Upgrade trigger:** When we have >100K labeled samples and feature importance plateaus

---

### ADR-003: Environment variables for secrets

**Decision:** All secrets via env vars, never in config files
**Context:** Private keys control real money
**Tradeoff:** Slightly more complex local setup
**Alternative considered:** Vault, AWS Secrets Manager (overkill for v1)

---

### ADR-004: Three-path architecture

**Decision:** Separate fast/medium/slow execution paths
**Context:** Latency-sensitive fast path shouldn't wait for LLM sentiment scoring
**Tradeoff:** More complex orchestration, potential signal conflicts between paths
**Mitigation:** Fast path only executes on extreme signals; medium path is primary
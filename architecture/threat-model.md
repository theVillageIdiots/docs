# Threat Model

**Methodology:** STRIDE
**Date:** 2026-02-22
**Scope:** All bot components — data ingestion, knowledge graph, signal generation, order execution, key management, infrastructure.

## System Trust Boundaries

```
┌─────────────┐    ┌──────────────┐    ┌──────────────┐    ┌────────────────┐
│ Data Feeds   │───▶│ Knowledge    │───▶│ Signal /     │───▶│ Order Manager  │
│ (Twitter,    │    │ Graph Store  │    │ Regime       │    │ (CLOB API)     │
│  Whale Alert,│    │ (NetworkX)   │    │ Detection    │    │                │
│  Polymarket) │    └──────────────┘    └──────────────┘    └───────┬────────┘
└─────────────┘                                                     │
                                                                    ▼
                                                          ┌────────────────┐
                                                          │ Polymarket     │
                                                          │ On-Chain       │
                                                          └────────────────┘

Secrets: .env → infra/secrets.py → env vars only
Risk:    risk/circuit_breaker.py, risk/portfolio.py, risk/metrics.py
```

1. **Bot process ↔ External APIs** (Twitter, Polymarket CLOB, Whale Alert)
2. **Bot process ↔ Blockchain** (on-chain order settlement)
3. **Bot process ↔ Host OS** (env vars, filesystem)
4. **Operator ↔ Bot** (configuration, manual overrides)

## STRIDE Analysis

### S — Spoofing

| # | Threat | Target | Likelihood | Impact | Mitigation |
|---|--------|--------|------------|--------|------------|
| S1 | **Adversarial sentiment injection** — attacker creates fake influencer accounts to game the scorer | Data ingestion / Graph | **High** | **High** | `graph/store.py` weights posts by `historical_accuracy` and `influence_score`; new accounts get near-zero weight. Manual allow-list of tracked influencer IDs. |
| S2 | Spoofed whale alert data | Whale Alert feed | Medium | Medium | Validate tx hashes on-chain before acting. Cross-reference multiple data sources. |
| S3 | Impersonation of bot operator | Config / manual halt | Low | High | Secrets in env vars only; no remote admin interface exposed. |

### T — Tampering

| # | Threat | Target | Likelihood | Impact | Mitigation |
|---|--------|--------|------------|--------|------------|
| T1 | **Data feed manipulation** — MITM or compromised API returning altered prices | Polymarket feed | Medium | **Critical** | HTTPS with certificate pinning. Compare CLOB prices against on-chain settlement. Circuit breaker detects P&L anomalies. |
| T2 | Tampered .env file on disk | Secrets | Low | Critical | File permissions `600`; secrets loaded once at startup and held in memory only. |
| T3 | Modified graph state in memory | Knowledge Graph | Low | High | Graph is in-process. Input validation on all ingested entities. |

### R — Repudiation

| # | Threat | Target | Likelihood | Impact | Mitigation |
|---|--------|--------|------------|--------|------------|
| R1 | Inability to audit why a trade was placed | Order history | Medium | Medium | Every `TradeRecord` logs edge, regime, signal source, timestamp. All halts logged in `circuit_breaker.halt_history`. |
| R2 | Disputed trade execution | CLOB API | Low | Medium | Log full API request/response. Store order IDs and on-chain tx hashes. |

### I — Information Disclosure

| # | Threat | Target | Likelihood | Impact | Mitigation |
|---|--------|--------|------------|--------|------------|
| I1 | **Private key exposure** in logs, config files, or crash dumps | Wallet private key | Medium | **Critical** | `infra/secrets.py` loads from env only; `SecretMaskingFilter` redacts all secret values from log output. |
| I2 | **API key leakage** via version control | API credentials | Medium | High | `.env` in `.gitignore`; `REQUIRED_SECRETS` validated at startup (fail-fast if missing). |
| I3 | Trading strategy leak via copy-trading | Strategy alpha | **High** | High | `risk/anti_copytrading.py`: timing randomization, noise trade injection, wallet rotation, copy-trader detection. |

### D — Denial of Service

| # | Threat | Target | Likelihood | Impact | Mitigation |
|---|--------|--------|------------|--------|------------|
| D1 | API rate limiting / Polymarket CLOB downtime | Order execution | Medium | Medium | Retry with exponential backoff. Circuit breaker auto-halts if orders fail repeatedly. |
| D2 | Feed flooding — massive social post volume to overwhelm graph | Knowledge Graph | Low | Medium | `prune_old_data()` bounds memory. Rate-limit ingestion per source. Tier filtering ignores low-tier noise. |

### E — Elevation of Privilege

| # | Threat | Target | Likelihood | Impact | Mitigation |
|---|--------|--------|------------|--------|------------|
| E1 | **Front-running of on-chain orders** — MEV bots observe pending transactions | On-chain execution | **High** | **High** | Use Polymarket CLOB (off-chain matching). Timing randomization via `TimingRandomiser`. Wallet rotation. Consider Flashbots Protect for settlement txs. |
| E2 | Compromised dependency (supply chain attack) | Bot process | Low | Critical | Pin dependency versions; `pip audit`; minimal dependency surface; containerize runtime. |

## High-Priority Mitigations Summary

| Priority | Mitigation | Implemented In |
|----------|-----------|---------------|
| 🔴 P0 | Secrets from env vars only; log masking | `infra/secrets.py` |
| 🔴 P0 | Daily drawdown circuit breaker | `risk/circuit_breaker.py` |
| 🔴 P0 | Kelly fraction position sizing (quarter-Kelly) | `risk/metrics.py`, `execution/order_manager.py` |
| 🟠 P1 | Anti-copy-trading (timing, noise, rotation) | `risk/anti_copytrading.py` |
| 🟠 P1 | Graph uncertainty halt | `risk/circuit_breaker.py` |
| 🟠 P1 | Influencer accuracy weighting | `graph/store.py` |
| 🟡 P2 | Portfolio correlation limits | `risk/portfolio.py` |
| 🟡 P2 | P&L anomaly detection | `risk/circuit_breaker.py` |
| 🟡 P2 | On-chain tx hash verification | TODO |
| 🟢 P3 | Private mempool for settlement | TODO (Flashbots integration) |

## Residual Risks

1. **Sophisticated adversarial sentiment** — a well-resourced attacker with access to real high-tier accounts can still game signals.
2. **Regulatory risk** — Polymarket access or prediction market legality may change. Outside technical scope.
3. **Smart contract risk** — bugs in Polymarket's contracts could cause loss of funds. Limit total capital deployed.
4. **Correlation breakdown** — in extreme events, previously uncorrelated positions may move together. Portfolio VaR may underestimate tail risk.
# villageIdiots Documentation

Central documentation hub for the villageIdiots organization — covering system architecture, setup guides, development roadmaps, operations runbooks, research findings, and the OpenClaw skills catalog.

Docs Website: [Documentations](https://thevillageidiots.gitbook.io/thevillageidiots-docs)

## Navigation

| Section | Description |
|---------|-------------|
| [architecture/](architecture/) | System design, data feeds, feature engineering, execution engine, knowledge graph, threat model, ADRs |
| [setup/](setup/) | Quick start, configuration, Python virtual environment setup |
| [development/](development/) | Roadmap, upgrade plan (Phases 0–5), implementation specs |
| [operations/](operations/) | Release process, monitoring, signoff templates |
| [research/](research/) | Signal research, empirical findings, feature analysis |
| [skills/](skills/) | OpenClaw master skills catalog — 72 cross-domain competency guides |

## Projects

### Polymarket BTC 5-Min Prediction Bot

Automated trading bot for Polymarket BTC 5-minute Up/Down markets.

**Current state:**
- Production: Heuristic model — 54–55% win rate, 516+ trades, $1,610+ profit
- 24 live data feeds across price, derivatives, social, on-chain, and cross-asset
- 165-feature pipeline (53 base + 112 engineered)
- Three execution paths: Heuristic (A), Snipe (B), Sum-Arb (C)
- Knowledge graph: Memgraph + in-process NetworkX, temporal-causal event ontology

**Key finding:** The edge is Polymarket price lag arbitrage, not BTC direction prediction. The strongest signal is mean-reversion: CVD family dominates with 8 of the top 20 features. See [research/signal-research.md](research/signal-research.md).

**Next milestone:** Phase 0 stabilization → Phase 2 ML baseline (requires ≥1,500 unique v2 windows). See [development/roadmap.md](development/roadmap.md).

## Quick Links

- [Quick Start](setup/quick-start.md)
- [Architecture Overview](architecture/system-overview.md)
- [Feature Engineering](architecture/feature-engineering.md)
- [Threat Model](architecture/threat-model.md)
- [Development Roadmap](development/roadmap.md)
- [Upgrade Plan](development/upgrade-plan/README.md)
- [Release Process](operations/release-process.md)
- [Signal Research](research/signal-research.md)
- [Skills Catalog](skills/_master-skill-catalog.md)

## Repository Structure

```
docs/
├── README.md                    ← You are here
├── CONTRIBUTING.md
├── architecture/                ← System design
│   ├── system-overview.md
│   ├── data-feeds.md
│   ├── feature-engineering.md
│   ├── execution-engine.md
│   ├── knowledge-graph.md
│   ├── threat-model.md
│   └── adr/
├── setup/                       ← Getting started
│   ├── quick-start.md
│   ├── configuration.md
│   └── environments/            ← Per-module pyenv virtualenvs
├── development/                 ← Roadmap & implementation
│   ├── roadmap.md
│   └── upgrade-plan/            ← Phase 0–5 specs
├── operations/                  ← Release & monitoring
│   ├── release-process.md
│   ├── monitoring.md
│   └── templates/               ← Signoff packet templates
├── research/                    ← Empirical findings
│   ├── signal-research.md
│   └── features.md
└── skills/                      ← OpenClaw skills catalog
    └── _master-skill-catalog.md
```
# Phase 4: Staged 3-Graph Augmentation

**Status:** Pending Phase 3
**Entry gate:** Phase 3 exit criteria met

Add one graph at a time. Each stage must pass its A/B gate before the next stage begins. Failed modules are rolled back and documented.

## Fusion Strategy

Start with meta-model on `[PathA_tabular_score, z_asset, z_participant, z_event, latency_stats]`.
Promote to gated fusion head only if meta-model underperforms on the A/B gate metric.

## Per-Stage A/B Gate

All three stages must clear:
- Improves net paper ROI by >= 0.5% over 14-day rolling window vs. prior baseline
- Does not increase p95 inference latency by more than 40ms
- Does not degrade max drawdown

---

## Stage 4A: Cross-Asset Temporal Graph

**Model:** Directed dynamic spillover graph + GATv2Conv encoder + GRU temporal state

**Nodes (7):** BTC, ETH, SOL, XRP, SPX futures, Gold, DXY

**Edges:** Rolling 30-day Pearson correlation (threshold |r| > 0.3). Granger causality for directed edge orientation.

**Node features per snapshot:** `[5m_return, 15m_return, 1h_return, volatility_1h, momentum_score, volume_ratio]`

**Output:** `z_asset` — 32-dimensional BTC node embedding

```python
from torch_geometric.nn import GATv2Conv
import torch.nn as nn

class CrossAssetGAT(nn.Module):
    def __init__(self, in_channels=6, hidden=64, out_channels=32, heads=4):
        super().__init__()
        self.conv1 = GATv2Conv(in_channels, hidden, heads=heads, dropout=0.2)
        self.conv2 = GATv2Conv(hidden * heads, hidden, heads=heads, dropout=0.2)
        self.conv3 = GATv2Conv(hidden * heads, out_channels, heads=1, concat=False)

    def encode(self, x, edge_index, edge_attr):
        x = F.relu(self.conv1(x, edge_index, edge_attr))
        x = F.dropout(x, p=0.2, training=self.training)
        x = F.relu(self.conv2(x, edge_index, edge_attr))
        x = F.dropout(x, p=0.2, training=self.training)
        return self.conv3(x, edge_index, edge_attr)  # [n_nodes, 32]
```

Training: multi-task binary cross-entropy on all 7 nodes. AdamW lr=0.001, weight_decay=1e-4. Early stopping patience=15. Target inference latency < 50ms (export to ONNX if exceeded).

**Deliverable:** `graph_cross_asset_v1`, ablation report vs. tabular-only baseline

---

## Stage 4B: Participant Temporal Hetero Graph

> **Pre-requisite check (v1.1 addition):** Before building this graph, confirm wallet-level order data is being captured from Polymarket CLOB — individual wallet address, order side (UP/DOWN), order size, order timestamp, and market round ID. If not, add a CLOB order stream listener first.

**Model:** HGT (Heterogeneous Graph Transformer) + TGN memory module

**Node types:** Wallet, MarketRound, Side (UP/DOWN), Outcome

**Edge types:** `wallet_bet_side`, `side_in_round`, `round_resolved_to`, `wallet_won`, `wallet_lost`

**Output:** `z_participant` — embedding encoding current-round participant dynamics

**Deliverable:** `graph_participant_v1`, ablation report vs. Stage 4A baseline

---

## Stage 4C: Event Knowledge Graph

**Model:** Event-enhanced KG with Graph-RAG retrieval + RGAT/RGCN encoder

**Graph contents:** Macro events (FOMC, CPI, employment), on-chain events, sentiment events, price events — linked via `PRECEDED_BY`, `LIKELY_CAUSED`, `CONFIRMS`, `CONTRADICTS`

**Output:** `z_event` — embedding capturing causal event context for active trading window

**Deliverable:** `graph_event_v1`, ablation report vs. Stage 4B baseline
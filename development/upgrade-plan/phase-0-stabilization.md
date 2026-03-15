# Phase 0: Stabilization and Leakage Prevention

**Status:** In progress
**Entry gate:** None
**Goal:** Clean data pipeline, sub-second Polymarket ingestion, no data leakage

## Tasks

- Disable Lightning and mempool features from model inputs and stop collection
- Pause X/news feature use in model inputs, but **keep raw capture on** via `SignalEventV1` log
- Enforce `data_version == 2` filter before any feature engineering
- Replace any shuffled split with time-ordered split
- Fix Polymarket CLOB WebSocket and remove 5s REST fallback for live features. Reference `github.com/warproxxx/poly-maker` for working WebSocket connection code. Target mean update interval < 1.0s
- Add independent timestamped raw signal log (`SignalEventV1`) with fields: `ts_ms`, `signal_name`, `signal_value`, `source`, `market_round_id`. Log at exact capture time, independent of which 5-minute window the value is later assigned to
- Build `adapters/v2_to_v3.py` with the field mapping defined in the contracts section

## Data Contract

```python
class SignalEventV1(TypedDict):
    ts_ms: int
    signal_name: str
    signal_value: float
    source: Literal["cex_ws", "polymarket_ws", "chainlink_ws", "x", "news", "whale", "deribit"]
    market_round_id: str

class ModelInputV3(TypedDict):
    schema_version: Literal["v3"]   # Hard fail if missing
    ts_ms: int
    market_round_id: str
    tabular_features: dict[str, float]
    z_asset: list[float] | None
    z_participant: list[float] | None
    z_event: list[float] | None
    seconds_to_close: int
```

## Adapter

```python
# adapters/v2_to_v3.py
def v2_snapshot_to_model_input_v3(snapshot: dict) -> ModelInputV3:
    return ModelInputV3(
        schema_version="v3",
        ts_ms=int(snapshot.get("timestamp", 0) * 1000),
        market_round_id=snapshot.get("market_id", ""),
        tabular_features={k: v for k, v in snapshot.items()
                          if k in RETAINED_FEATURE_SET},
        z_asset=None,
        z_participant=None,
        z_event=None,
        seconds_to_close=snapshot.get("seconds_remaining", 0),
    )
```

## Deliverables

- Clean v2-only training dataset
- Sub-second Polymarket order-book ingestion
- Raw signal event log (`SignalEventV1`) populated continuously
- `adapters/v2_to_v3.py` with passing adapter tests

## Exit Criteria

- No disabled features appear in `X_train`
- Training/test timestamp ranges are non-overlapping and ordered
- Mean Polymarket update interval < 1.0s (verify from logs)
- `schema_version` present and validated on all new writes
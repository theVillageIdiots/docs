# Configuration

## Environment Variables

### Required

| Variable | Description |
|----------|-------------|
| `POLYMARKET_PRIVATE_KEY` | Wallet private key for live trading |
| `POLYMARKET_FUNDER_ADDRESS` | Wallet address for collateral |

### Optional

| Variable | Description | Feature Enabled |
|----------|-------------|-----------------|
| `TWITTER_BEARER_TOKEN` | X/Twitter API v2 bearer token | Social sentiment feeds (Tier 1/2/3) |
| `WHALE_ALERT_API_KEY` | Whale Alert API key | On-chain large transaction signals |
| `OPENAI_API_KEY` | OpenAI API key | LLM-based sentiment scoring |

## Key Bot Parameters

Edit values in `main.py` `PolymarketBot.__init__()`:

| Parameter | Default | Description |
|-----------|---------|-------------|
| `BET_FRACTION` | `0.02` | Base bet size as fraction of bankroll |
| `CIRCUIT_BREAKER_CONSECUTIVE` | `8` | Halt after N consecutive losses |
| `CIRCUIT_BREAKER_DRAWDOWN` | `0.15` | Halt at 15% daily drawdown |
| `ARB_CAP` | `500` | Max per-side arb bet ($) |

## Monitoring Metrics

The bot exposes metrics via `GET /status`:

| Metric | Description |
|--------|-------------|
| `calibration_brier_200` | Rolling Brier score (model calibration) |
| `drift_psi_max` | Max feature drift (PSI) |
| `conformal_safe_mode` | Whether safe mode is active (reduces sizing by 50%) |
| `websocket_connected` | Polymarket WS status |
| `updates_per_second` | Data feed throughput |

## Secrets Management

All secrets must be loaded via environment variables. The `infra/secrets.py` module:
- Loads from env at startup
- Validates `REQUIRED_SECRETS` — fails fast if any are missing
- Applies `SecretMaskingFilter` to all log output — secrets are never written to disk after load

See [threat-model.md](../architecture/threat-model.md) for full security context.
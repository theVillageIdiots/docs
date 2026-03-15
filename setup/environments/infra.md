# polybot-infra-3.12

**Python:** 3.12
**Directory:** `infra/`
**Purpose:** Configuration management, secrets loading, FastAPI backend dashboard server, and deployment utilities.

## Key Files

| File | Purpose |
|---|---|
| `config.example.yaml` | Template config (copy to `config.yaml`) |
| `secrets.py` | Secrets loader (env vars + YAML) |
| `dashboard.py` | FastAPI backend for the dashboard |
| `deploy/` | Deployment scripts |

## Dependencies

```
fastapi>=0.109.0
uvicorn[standard]>=0.27.0
pyyaml>=6.0
python-dotenv>=1.0.0
requests>=2.31.0
rich>=13.0.0
loguru>=0.7.0
```

## Create

```bash
pyenv virtualenv 3.12 polybot-infra-3.12
pyenv activate polybot-infra-3.12
pip install --upgrade pip
pip install \
  "fastapi>=0.109.0" \
  "uvicorn[standard]>=0.27.0" \
  "pyyaml>=6.0" \
  "python-dotenv>=1.0.0" \
  "requests>=2.31.0" \
  "rich>=13.0.0" \
  "loguru>=0.7.0"
```

## Pin to Directory

```bash
cd polymarket-bot/infra
pyenv local polybot-infra-3.12
```

## Notes

- `uvicorn[standard]` includes `websockets` and `httptools` for production performance.
- The Next.js frontend in `dashboard/` connects to this FastAPI backend — they run independently.
- `secrets.py` reads from environment variables first, falling back to `config.yaml`. Never commit `config.yaml` with real keys.
- Lightest production-runtime env; no ML, no web3.

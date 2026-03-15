# Quick Start

Get the Polymarket BTC bot running in paper-trading mode in under 10 minutes.

## 1. Clone & Install

```bash
cd ~/workspace
git clone <repo-url> polymarket-bot
cd polymarket-bot

# Python environment
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

# Dashboard
cd dashboard
npm install
cd ..
```

## 2. Configure Environment

```bash
cp .env.example .env
# Edit .env with your keys:
#   POLYMARKET_PRIVATE_KEY  (required for live trading)
#   POLYMARKET_FUNDER_ADDRESS
#   TWITTER_BEARER_TOKEN    (optional, for X/Twitter sentiment)
#   WHALE_ALERT_API_KEY     (optional, on-chain signals)
#   OPENAI_API_KEY          (optional, LLM features)
```

## 3. Run (Paper Trading)

```bash
# Terminal 1: Bot
source .venv/bin/activate
python main.py

# Terminal 2: Dashboard
cd dashboard
npx next dev -p 3000

# Terminal 3: Cloudflare tunnel (optional, for remote access)
cloudflared tunnel --url http://localhost:8080
```

The bot starts in **paper trading mode** by default. No real money is at risk.

## 4. Verify

- Dashboard: http://localhost:3000
- API: http://localhost:8080/status
- Health: http://localhost:8080/healthz

## Project Structure

```
polymarket-bot/
├── main.py                 # Entry point, wires everything together
├── data/                   # Data feeds (Binance, Polymarket, Chainlink, etc.)
├── signals/                # ML model, graph signals, calibration monitors
├── execution/              # Paper trader, order manager, strategy router
├── risk/                   # Circuit breaker, CVaR allocator, regime budgets
├── graph/                  # NetworkX knowledge graph + schema
├── training/               # Feature engineering, model training pipeline
├── dashboard/              # Next.js dashboard UI
├── infra/                  # FastAPI dashboard API, secrets, health checks
├── tests/                  # pytest suite
├── models/                 # Trained XGBoost models (included)
├── logs/                   # Runtime logs (included for reference)
└── docs/                   # Reports, audits, architecture docs
```

## Trading Paths

| Path | Strategy | Description |
|------|----------|-------------|
| **A (Heuristic)** | Production model | Heuristic directional bets, 54–55% win rate |
| **B_snipe** | Snipe mode | At 45s mark, bet with established BTC direction |
| **C_arb** | Sum-arb | Guaranteed profit when UP+DOWN < $0.97 |

## LaunchAgent (Auto-Start on macOS)

```bash
cat > ~/Library/LaunchAgents/com.polybot.bot.plist << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.polybot.bot</string>
    <key>ProgramArguments</key>
    <array>
        <string>/path/to/polymarket-bot/.venv/bin/python</string>
        <string>main.py</string>
    </array>
    <key>WorkingDirectory</key>
    <string>/path/to/polymarket-bot</string>
    <key>KeepAlive</key>
    <true/>
    <key>StandardOutPath</key>
    <string>/path/to/polymarket-bot/logs/bot-stdout.log</string>
    <key>StandardErrorPath</key>
    <string>/path/to/polymarket-bot/logs/bot-stderr.log</string>
</dict>
</plist>
EOF

launchctl load ~/Library/LaunchAgents/com.polybot.bot.plist
```

## Tests

```bash
source .venv/bin/activate
python -m pytest tests/ -v
```

## Retraining the Model

```bash
source .venv/bin/activate
python training/train_production.py
# Output: models/latest.pkl + models/latest_metadata.json
```
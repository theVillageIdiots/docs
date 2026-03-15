# polybot-adapters-3.12

**Python:** 3.12
**Directory:** `adapters/`
**Files:** `__init__.py`, `v2_to_v3.py`
**Purpose:** Bridge / conversion layer between Polymarket CLOB API v2 and v3 data formats.

## Dependencies

Minimal — only needs the CLOB client and basic typing/data utilities.

```
py-clob-client>=0.1.0
requests>=2.31.0
python-dotenv>=1.0.0
```

## Create

```bash
pyenv virtualenv 3.12 polybot-adapters-3.12
pyenv activate polybot-adapters-3.12
pip install --upgrade pip
pip install "py-clob-client>=0.1.0" "requests>=2.31.0" "python-dotenv>=1.0.0"
```

## Pin to Directory

```bash
cd polymarket-bot/adapters
pyenv local polybot-adapters-3.12
```

## Notes

- Lightest env in the project. If using the master env this module adds nothing conflicting.
- `v2_to_v3.py` translates legacy order/fill schemas; no ML deps required.

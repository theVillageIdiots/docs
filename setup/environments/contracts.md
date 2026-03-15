# polybot-contracts-3.12

**Python:** 3.12
**Directory:** `contracts/`
**Files:** `__init__.py`, `model_contracts.py`
**Purpose:** Typed interface contracts (dataclasses / protocols) that define the I/O surface between modules — signals → execution, models → risk, etc.

## Dependencies

Pure stdlib + optional typing extensions.

```
python-dotenv>=1.0.0
pyyaml>=6.0
```

Optional if contracts include serialization validation:
```
pydantic>=2.0.0
```

## Create

```bash
pyenv virtualenv 3.12 polybot-contracts-3.12
pyenv activate polybot-contracts-3.12
pip install --upgrade pip
pip install "pyyaml>=6.0" "python-dotenv>=1.0.0"
# Optional:
pip install "pydantic>=2.0.0"
```

## Pin to Directory

```bash
cd polymarket-bot/contracts
pyenv local polybot-contracts-3.12
```

## Notes

- Intentionally minimal. This module should import cleanly into any other env.
- If `model_contracts.py` grows to include numpy `ndarray` type annotations, add `numpy` here.
- No ML, no web3, no async networking required.

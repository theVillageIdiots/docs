# polybot-master-3.12

**Python:** 3.12
**Scope:** Entire polymarket-bot codebase
**Status:** Attempt first — covers all modules from a single env

## Create

```bash
pyenv install 3.12
pyenv virtualenv 3.12 polybot-master-3.12
pyenv activate polybot-master-3.12
pip install --upgrade pip wheel
pip install -r polymarket-bot/requirements.txt
# Graph extra (not in requirements.txt)
pip install neo4j
```

## Verify

```bash
python -c "import xgboost, web3, anthropic, networkx, fastapi; print('master env OK')"
```

## Known Clash Risks

| Conflict Area | Detail |
|---|---|
| `web3 >= 6.0` + `numpy` | web3 pulls `cytoolz` which may compile against a specific numpy ABI. Pin numpy if needed: `numpy==1.26.4` |
| `xgboost >= 2.0` + `scikit-learn` | Generally fine on 3.12 but watch for `sklearn` API changes |
| `openai` + `httpx` | openai uses httpx under the hood; `aiohttp` and `httpx` coexist without issue |
| `statsmodels` + `scipy` | Both require numpy; all compatible at the `>=` pins in requirements.txt |

## If the Master Env Breaks

Fall back to per-module envs. The most common split point is isolating the **data** env (web3/eth-account) from the **signals/training** envs (heavy ML). See [data.md](data.md) and [signals.md](signals.md).

## Set as Root Default

```bash
cd polymarket-bot
pyenv local polybot-master-3.12
```

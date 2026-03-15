# polybot-tests-3.12

**Python:** 3.12
**Directory:** `tests/`
**Purpose:** Full test suite — unit, integration, and QA tests across all modules.

## Test Files

| File | Covers |
|---|---|
| `test_phase0.py` | Phase 0 baseline checks |
| `test_phase1.py` | Phase 1 signal pipeline |
| `test_fair_value.py` | Fair value model accuracy |
| `test_cvar_allocator.py` | CVaR position sizing |
| `test_oracle_timing.py` | Oracle lag signal |
| `test_graph.py` | Knowledge graph ops |
| `test_graph_prune.py` | Graph memory pruning |
| `test_monitors.py` | Signal monitor checks |
| `test_chainlink_streams.py` | Chainlink feed integration |
| `test_p0_fixes.py` | P0 regression fixes |
| `test_path_a_gate.py` | Conformal gate path A |
| `test_stability_fix.py` | Stability regression |
| `test_assessment_fixes.py` | Assessment harness |
| `test_status_surface_start_now.py` | Status surface latency |
| `qa_dashboard.py` | QA dashboard runner |

## Dependencies

All modules under test + test tooling:

```
pytest>=7.4.0
pytest-asyncio>=0.23.0
numpy>=1.26.0
pandas>=2.1.0
scikit-learn>=1.3.0
xgboost>=2.0.0
scipy>=1.12.0
statsmodels>=0.14.0
networkx>=3.2
ta>=0.11.0
pyarrow>=15.0.0
websockets>=12.0
aiohttp>=3.9.0
requests>=2.31.0
rich>=13.0.0
loguru>=0.7.0
python-dotenv>=1.0.0
pyyaml>=6.0
```

## Create

```bash
pyenv virtualenv 3.12 polybot-tests-3.12
pyenv activate polybot-tests-3.12
pip install --upgrade pip wheel
# Install full requirements (tests need all modules importable)
pip install -r ../requirements.txt
pip install "neo4j>=5.0.0"  # for graph tests
```

## Pin to Directory

```bash
cd polymarket-bot/tests
pyenv local polybot-tests-3.12
```

## Run Tests

```bash
# All tests
pytest tests/

# Async tests (requires pytest-asyncio)
pytest tests/ --asyncio-mode=auto

# Specific module
pytest tests/test_fair_value.py -v

# QA dashboard
python tests/qa_dashboard.py
```

## Notes

- The test env is effectively the **same as the master env** since tests exercise all modules.
- Use `pytest-asyncio` with `asyncio_mode = "auto"` (set in `pyproject.toml` or `pytest.ini`) to avoid `@pytest.mark.asyncio` on every async test.
- Tests that hit `chainlink_streams` need `CHAINLINK_STREAMS_API_KEY` in environment or will skip.
- `web3`/`eth-account` are not in requirements.txt but may be pulled in by `py-clob-client` — add them explicitly if tests fail on import.

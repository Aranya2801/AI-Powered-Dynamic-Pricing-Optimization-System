# Contributing to AI Dynamic Pricing System

Thank you for considering a contribution!

## Getting Started

```bash
git clone https://github.com/your-username/ai-dynamic-pricing.git
cd ai-dynamic-pricing
pip install -e ".[dev]"
pre-commit install
python scripts/generate_datasets.py  # creates local test data
pytest tests/ -v
```

## Pull Request Process

1. Fork & branch: `git checkout -b feature/your-feature`
2. Write tests for new functionality (target 80%+ coverage)
3. Run `pre-commit run --all-files` and `pytest tests/ -v`
4. Update `CHANGELOG.md`
5. Open a PR against `develop`

### Commit/PR Title Format
```
feat: add contextual bandit pricing agent
fix: correct competitor price cap clipping bug
docs: expand RL agent documentation
test: add coverage for autoencoder anomaly detector
```

## Adding a New Demand Forecasting Model

Edit `models/demand_forecasting/forecasters.py`:
1. Implement a new class following the `GradientBoostingForecaster` / `DeepSequenceForecaster` pattern
2. Return a `ForecastResult` dataclass from `.predict()`
3. Register it in `DemandForecastingEngine.run_all()`
4. Add a test in `tests/test_suite.py::TestDemandForecasting`

## Adding a New RL Algorithm

Edit `models/rl_agent/agents.py`:
1. Implement an agent class with `select_action()` and `train(env, num_episodes)` methods
2. Ensure it's compatible with `PricingEnvironment`'s `(state, action) → (next_state, reward, done, info)` interface
3. Add to `PolicyEvaluator.evaluate()`'s `agent_type` branches
4. Add a test in `tests/test_suite.py::TestRLAgents`

## Code Standards
- **Black** (line-length 100), **Ruff**, **MyPy**
- Google-style docstrings
- All new modules require corresponding tests

## Reporting Bugs
Use the [bug report template](.github/ISSUE_TEMPLATE/bug_report.md). Include Python version, full traceback, and a minimal reproducible example.

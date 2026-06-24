# Changelog

All notable changes to this project are documented in this file.
Format based on [Keep a Changelog](https://keepachangelog.com/), versioning per [SemVer](https://semver.org/).

## [1.0.0] - 2025-06-01

### Added
- Demand forecasting engine: XGBoost, LightGBM, CatBoost, Prophet, LSTM, GRU, Transformer
- Dynamic pricing engine with log-log elasticity estimation and revenue/profit optimization
- Reinforcement learning pricing agents: DQN, PPO, Multi-Armed Bandit (Thompson Sampling/UCB1/ε-greedy)
- Simulated pricing environment with inventory dynamics and customer retention modeling
- Customer segmentation: K-Means, DBSCAN, Hierarchical clustering with business-label mapping
- Anomaly detection: Isolation Forest, Autoencoder, One-Class SVM with ensemble voting
- Explainable AI layer: SHAP and LIME explainers for pricing/demand models
- Competitor intelligence engine with mock, web-scraper, and API-feed data sources
- Generative AI pricing advisor with Anthropic/OpenAI provider abstraction
- FastAPI backend with JWT authentication and RBAC
- Streamlit dashboard: Executive, Forecast, Pricing, Competitor, AI Insights, Explainability, RL pages
- PostgreSQL schema with 8 tables, indexes, and convenience views
- Synthetic dataset generator producing 100K+ pricing rows, 50K+ customer rows, 50K+ competitor rows
- Docker Compose stack: API, Frontend, PostgreSQL, Redis, MLflow, Nginx
- GitHub Actions CI/CD: lint, test, security scan, Docker build, release automation
- 41 passing unit/integration tests across all modules

## [Unreleased]
- See [ROADMAP.md](ROADMAP.md) for planned features

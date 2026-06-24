# Roadmap

## ✅ v1.0.0 (Current)
- Core forecasting, pricing, RL, segmentation, anomaly detection, XAI, GenAI advisor
- FastAPI + Streamlit + PostgreSQL + Docker stack

## 🚧 v1.1.0 (Planned)
- [ ] Real-time WebSocket price feed for live dashboards
- [ ] A/B testing framework for pricing strategy comparison
- [ ] Multi-currency and multi-region pricing support
- [ ] Automated competitor scraper scheduling (Celery + Redis queue)
- [ ] SHAP/LIME explanations wired directly into the dashboard (live, not demo data)

## 🔮 v1.2.0 (Future)
- [ ] Contextual bandits with customer-level features
- [ ] Hierarchical RL for category-level + SKU-level pricing
- [ ] Causal inference module (uplift modeling) for promotion impact
- [ ] GraphQL API alongside REST
- [ ] Mobile-responsive React dashboard alternative to Streamlit
- [ ] Federated pricing across multi-tenant deployments
- [ ] Integration with major e-commerce platforms (Shopify, WooCommerce APIs)

## 💡 Ideas Under Consideration
- LLM-based synthetic customer simulation for RL training
- Price-fairness auditing (avoid discriminatory pricing across protected segments)
- On-device/edge inference for retail POS pricing

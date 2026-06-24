# Datasets

## Synthetic Datasets (auto-generated)

Generate all three via:
```bash
python scripts/generate_datasets.py --pricing-rows 100000 --customer-rows 50000 --competitor-rows 50000
```

Output: `data/synthetic/`

### 1. Dynamic Pricing Dataset (100,000+ rows)
`dynamic_pricing_dataset.csv` / `.parquet`

| Column | Type | Description |
|--------|------|-------------|
| product_id | int | Product identifier (500 unique products) |
| date | date | Observation date |
| category | str | One of 10 product categories |
| demand | float | Units demanded (elasticity-driven, realistic signal) |
| inventory | int | Units in stock |
| competitor_price | float | Nearest competitor's price |
| customer_segment | str | premium / budget / high_frequency / seasonal / price_sensitive |
| weather | str | sunny / rainy / snowy / cloudy / stormy |
| holiday | int (0/1) | Holiday period flag |
| season | str | winter / spring / summer / fall |
| price | float | Our listed price |
| cost | float | Unit cost (COGS) |
| revenue | float | price × demand |
| profit | float | (price − cost) × demand |

**Generation methodology:** demand follows a log-log elasticity model (`demand = base_demand × (price/base_price)^elasticity`) with category-specific elasticities (−0.6 to −1.8), seasonal/holiday/weather multipliers, and Gaussian noise — so downstream ML models have genuine, learnable signal rather than pure randomness.

### 2. Customer Dataset (50,000+ rows)
`customer_dataset.csv`

| Column | Description |
|--------|-------------|
| customer_id | Unique ID |
| true_segment | Ground-truth segment (for clustering validation) |
| total_spend | Lifetime spend |
| purchase_frequency | Orders per period |
| avg_order_value | Average basket size |
| discount_sensitivity | 0–1 scale |
| recency_days | Days since last purchase |
| seasonal_purchase_ratio | Fraction of purchases in peak season |
| tenure_days | Customer age (days since signup) |
| email_open_rate | Marketing engagement |
| churn_risk_score | 0–1 scale |
| age, region | Demographics |

Generated from 5 latent segment profiles with realistic overlapping distributions (Gaussian per-feature, segment-specific means/stds) — designed so K-Means/DBSCAN/Hierarchical clustering can recover meaningful structure.

### 3. Competitor Dataset (50,000+ rows)
`competitor_dataset.csv`

| Column | Description |
|--------|-------------|
| product_id | Linked to pricing dataset |
| competitor_name | One of 5 simulated competitors |
| date | Observation date |
| competitor_price | Observed price |
| competitor_strategy | aggressive / premium / matching / dynamic |
| in_stock | Availability flag |
| discount_active, discount_pct | Promotion info |
| market_position_rank | Relative rank (1=cheapest) |

---

## Real-World Datasets (external references)

For benchmarking against real-world pricing/demand data, consider:

| Dataset | Source | Use Case |
|---------|--------|----------|
| [Online Retail II](https://archive.ics.uci.edu/dataset/502/online+retail+ii) | UCI | E-commerce transaction-level demand |
| [Instacart Market Basket](https://www.kaggle.com/c/instacart-market-basket-analysis) | Kaggle | Grocery demand patterns |
| [Airbnb Open Data](https://www.kaggle.com/datasets/dgomonov/new-york-city-airbnb-open-data) | Kaggle | Dynamic pricing for short-term rentals |
| [Flight Price Prediction](https://www.kaggle.com/datasets/shubhambathwal/flight-price-prediction) | Kaggle | Airline dynamic pricing |
| [Hotel Booking Demand](https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand) | Kaggle | Hotel pricing & cancellation patterns |
| [Walmart Sales Forecasting](https://www.kaggle.com/c/m5-forecasting-accuracy) | Kaggle (M5 Competition) | Retail demand forecasting benchmark |
| [US Bureau of Labor Statistics CPI](https://www.bls.gov/cpi/data.htm) | Government | Inflation-adjusted pricing baselines |
| [data.gov Retail Trade](https://www.census.gov/retail/index.html) | US Census Bureau | Macro retail trend validation |

## Loading Real Data

The `app/main.py` `/api/v1/datasets/upload` endpoint accepts any CSV with `product_id`, `price`, `demand` (or similarly named) columns — rename headers to match the schema above before uploading, or pass `text_column`/`label_column`-style overrides as needed in your own ETL script under `data/raw/`.

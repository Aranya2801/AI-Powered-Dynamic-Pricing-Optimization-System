# System Architecture

<p align="center"><img src="assets/diagrams/architecture.svg" width="100%"/></p>

## Table of Contents
- [High-Level Architecture](#high-level-architecture)
- [Low-Level Component Architecture](#low-level-component-architecture)
- [Data Flow Diagram](#data-flow-diagram)
- [ML Pipeline Diagram](#ml-pipeline-diagram)
- [Deployment Architecture](#deployment-architecture)
- [Database ER Diagram](#database-er-diagram)

---

## High-Level Architecture

```mermaid
graph TB
    subgraph Sources["📡 Data Sources"]
        A1[Sales History]
        A2[Inventory Systems]
        A3[Competitor Scraper/API]
        A4[Customer Behavior]
        A5[Weather/Calendar APIs]
    end

    subgraph Engineering["🔧 Data Engineering"]
        B1[ETL Pipeline<br/>Polars/DuckDB]
        B2[Data Validation]
        B3[Feature Store]
    end

    subgraph ML["🧠 AI/ML Core"]
        C1[Demand Forecasting<br/>XGBoost·LightGBM·LSTM·Transformer]
        C2[Pricing Engine<br/>Elasticity + Revenue Optimization]
        C3[RL Pricing Agent<br/>DQN·PPO·Bandit]
        C4[Segmentation<br/>KMeans·DBSCAN·Hierarchical]
        C5[Anomaly Detection<br/>IsoForest·Autoencoder·OCSVM]
        C6[Explainability<br/>SHAP·LIME]
        C7[GenAI Advisor<br/>Claude/OpenAI]
    end

    subgraph Serving["🌐 Serving Layer"]
        D1[FastAPI Backend<br/>JWT + RBAC]
        D2[Streamlit Dashboard]
    end

    subgraph Storage["💾 Storage"]
        E1[(PostgreSQL)]
        E2[(Redis Cache)]
    end

    subgraph MLOps["⚙️ MLOps"]
        F1[MLflow Tracking]
        F2[DVC Data Versioning]
        F3[Model Registry]
    end

    A1 & A2 & A3 & A4 & A5 --> B1 --> B2 --> B3
    B3 --> C1 & C2 & C3 & C4 & C5
    C1 --> C2
    C2 & C3 & C4 & C5 --> C6
    C2 & C5 --> C7
    C1 & C2 & C3 & C4 & C5 & C6 & C7 --> D1
    D1 --> D2
    D1 <--> E1
    D1 <--> E2
    C1 & C2 & C3 -.tracked by.-> F1
    B3 -.versioned by.-> F2
    F1 --> F3
```

---

## Low-Level Component Architecture

```mermaid
graph TB
    subgraph API["FastAPI Application (app/main.py)"]
        Auth[auth router<br/>JWT login/refresh]
        Forecast[forecast router]
        Pricing[pricing router]
        Segment[segmentation router]
        RL[rl router]
        Anomaly[anomaly router]
        Advisor[advisor router]
    end

    subgraph Core["app/core"]
        Security[security.py<br/>JWT + RBAC + bcrypt]
        Config[config.py]
    end

    subgraph Services["app/services"]
        CompIntel[competitor_intelligence.py]
        PricingAdvisor[pricing_advisor.py<br/>LLMProvider ABC]
    end

    subgraph Models["models/"]
        DF[demand_forecasting/<br/>forecasters.py]
        PE[pricing_engine/<br/>engine.py]
        RLAgent[rl_agent/<br/>agents.py]
        Seg[segmentation/<br/>segmentation.py]
        Anom[anomaly/<br/>detectors.py]
        XAI[xai/<br/>explainers.py]
    end

    Auth --> Security
    Forecast --> DF
    Pricing --> PE
    Segment --> Seg
    RL --> RLAgent
    Anomaly --> Anom
    Advisor --> PricingAdvisor
    PE --> XAI
    DF --> XAI
    Pricing --> CompIntel
```

---

## Data Flow Diagram

```mermaid
sequenceDiagram
    participant Client as Dashboard/API Client
    participant API as FastAPI
    participant Auth as Auth/RBAC
    participant Engine as Pricing Engine
    participant Forecast as Demand Forecaster
    participant DB as PostgreSQL
    participant LLM as GenAI Advisor

    Client->>API: POST /pricing/recommend
    API->>Auth: validate JWT + check permission
    Auth-->>API: authorized (role=analyst)
    API->>Engine: recommend_price(product_id, cost, inventory)
    Engine->>Forecast: get elasticity-adjusted demand
    Forecast-->>Engine: demand estimate + CI
    Engine->>Engine: optimize revenue/profit s.t. constraints
    Engine-->>API: PriceRecommendation(price, reasoning)
    API->>DB: INSERT INTO price_recommendations
    API->>LLM: (optional) explain_anomaly / generate_strategy
    LLM-->>API: natural language rationale
    API-->>Client: JSON response with price + reasoning
```

---

## ML Pipeline Diagram

```mermaid
graph LR
    Raw[("Raw Data<br/>(data/raw)")] --> Clean[Data Cleaning<br/>& Validation]
    Clean --> Feat[Feature Engineering<br/>(data/features)]
    Feat --> Split[Train/Val/Test Split]

    Split --> GBM[Gradient Boosting<br/>XGB/LGBM/CatBoost]
    Split --> Seq[Sequence Models<br/>LSTM/GRU/Transformer]
    Split --> Prophet[Prophet<br/>Seasonal Decomposition]

    GBM & Seq & Prophet --> Ensemble[Model Comparison<br/>& Selection]
    Ensemble --> Elasticity[Elasticity Estimation]
    Elasticity --> Optimize[Revenue/Profit<br/>Optimization]
    Optimize --> RLEnv[RL Environment<br/>Simulation]
    RLEnv --> RLTrain[DQN/PPO/Bandit<br/>Training]
    RLTrain --> Eval[Policy Evaluation<br/>vs Baseline]
    Eval --> Registry[(MLflow Model<br/>Registry)]
    Registry --> Deploy[Production API]
```

---

## Deployment Architecture

```mermaid
graph TB
    subgraph Internet
        User[Users / Clients]
    end

    subgraph LB["Load Balancer / Nginx"]
        NGINX[Reverse Proxy<br/>Rate Limiting · TLS]
    end

    subgraph Compute["Compute Layer (Docker/K8s)"]
        API1[FastAPI Pod 1]
        API2[FastAPI Pod 2]
        Dash[Streamlit Pod]
        MLflowSrv[MLflow Server]
    end

    subgraph Data["Data Layer"]
        PG[(PostgreSQL<br/>Primary)]
        PGReplica[(PostgreSQL<br/>Read Replica)]
        Redis[(Redis Cache)]
        S3[(Object Storage<br/>S3/Blob/GCS)]
    end

    subgraph Cloud["Cloud Provider"]
        AWS[AWS: ECS/EKS + RDS + ElastiCache]
        Azure[Azure: AKS + PostgreSQL + Cache]
        GCP[GCP: GKE + Cloud SQL + Memorystore]
    end

    User --> NGINX
    NGINX --> API1 & API2
    NGINX --> Dash
    API1 & API2 --> PG
    API1 & API2 --> Redis
    PG --> PGReplica
    API1 & API2 -.artifacts.-> S3
    MLflowSrv --> S3

    Compute -.deployed on.-> Cloud
```

---

## Database ER Diagram

```mermaid
erDiagram
    PRODUCTS ||--o{ PRICING_HISTORY : has
    PRODUCTS ||--o{ PRICE_RECOMMENDATIONS : generates
    PRODUCTS ||--o{ COMPETITOR_PRICES : tracked_by
    PRODUCTS ||--o{ ANOMALY_EVENTS : flagged_in
    USERS ||--o{ AUDIT_LOG : performs
    USERS ||--o{ PRICE_RECOMMENDATIONS : approves

    PRODUCTS {
        int product_id PK
        string sku
        string name
        string category
        string industry
        numeric base_price
        numeric cost
    }

    PRICING_HISTORY {
        bigint id PK
        int product_id FK
        date observed_date
        numeric price
        numeric demand
        int inventory
        numeric competitor_price
        string customer_segment
        boolean is_holiday
        numeric revenue
        numeric profit
    }

    PRICE_RECOMMENDATIONS {
        bigint id PK
        int product_id FK
        numeric recommended_price
        numeric current_price
        numeric confidence_score
        jsonb reasoning
        boolean accepted
    }

    COMPETITOR_PRICES {
        bigint id PK
        int product_id FK
        string competitor_name
        numeric price
        boolean in_stock
        string source
    }

    ANOMALY_EVENTS {
        bigint id PK
        int product_id FK
        string category
        numeric anomaly_score
        jsonb deviations
        boolean resolved
    }

    CUSTOMERS {
        int customer_id PK
        string predicted_segment
        numeric total_spend
        numeric churn_risk_score
    }

    USERS {
        uuid id PK
        string username
        string role
    }

    AUDIT_LOG {
        bigint id PK
        uuid user_id FK
        string action
        jsonb details
    }
```

---

## Technology Stack Summary

| Layer | Technologies |
|-------|--------------|
| **Forecasting** | XGBoost, LightGBM, CatBoost, Prophet, PyTorch (LSTM/GRU/Transformer) |
| **Pricing** | scipy.optimize, scikit-learn (elasticity regression) |
| **RL** | Custom DQN/PPO (PyTorch), Multi-Armed Bandit (Thompson Sampling/UCB1) |
| **Segmentation** | scikit-learn (KMeans, DBSCAN, Agglomerative) |
| **Anomaly Detection** | scikit-learn (IsolationForest, OneClassSVM), PyTorch Autoencoder |
| **Explainability** | SHAP, LIME |
| **GenAI** | Anthropic Claude / OpenAI GPT (provider abstraction) |
| **API** | FastAPI, JWT (python-jose), bcrypt (passlib), slowapi (rate limiting) |
| **Dashboard** | Streamlit, Plotly |
| **Database** | PostgreSQL, Redis |
| **MLOps** | MLflow, DVC, Optuna |
| **Data Engineering** | Pandas, Polars, DuckDB |
| **Deployment** | Docker, Docker Compose, Kubernetes, AWS/Azure/GCP |
| **CI/CD** | GitHub Actions |

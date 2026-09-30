# System Architecture

## High-Level Flow

```
GitHub/GitLab API ──▶ Ingestion Layer ──▶ PostgreSQL (raw events) ──▶ Metrics Engine ──▶ Aggregated tables ──▶ Dashboard (Streamlit/Grafana)
                          (webhooks or                                     │
                           scheduled poll)                                 ▼
                                                                   Benchmarking/Correlation
                                                                     (pandas/scipy jobs)
```

## Components

### 1. Ingestion Layer
- **Responsibility:** pull raw events (commits, PRs, reviews, deploys, CI runs, issues) from GitHub/GitLab APIs
- **Trigger model:** [webhooks / scheduled polling / both] — decide and document why in ADR
- **Idempotency:** every ingest job must be safely re-runnable (upsert on external event ID)

### 2. Storage Layer (PostgreSQL)
- Stores raw events, not pre-aggregated metrics — see `02-data-schema.md`
- Rationale: lets you redefine metric windows/logic later without re-ingesting from source APIs

### 3. Metrics Engine
- Scheduled or on-demand Python jobs that compute the 4 DORA metrics per repo/team/window
- Pure functions over the event tables — inputs and outputs should be reproducible and testable

### 4. Benchmarking & Correlation Layer
- Cross-team percentile ranking
- Practice correlation analysis (PR size vs lead time, review latency vs change-failure rate, etc.)

### 5. Dashboard Layer
- [Streamlit for fast iteration / Grafana for production feel] — decide and document why in ADR
- Reads from aggregated tables only, never computes metrics itself

## Component Boundaries (why this matters)
Each layer should be replaceable independently:
- Swapping GitHub → GitLab should only touch the Ingestion Layer
- Swapping Streamlit → Grafana should only touch the Dashboard Layer
- Redefining a metric should only touch the Metrics Engine, not ingestion or storage

## Deployment/Infra Notes
- [Local dev setup, hosting plan, scheduled job runner — cron / Airflow / GitHub Actions, etc.]

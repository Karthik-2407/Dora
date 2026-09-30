# Dev Workflow

## Local Setup
```bash
# 1. Clone and enter repo
git clone [repo-url] && cd [repo-name]

# 2. Python environment
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt

# 3. Database
# Fill in: docker-compose for Postgres, or local install instructions
docker compose up -d postgres

# 4. Run migrations
alembic upgrade head

# 5. Run ingestion (manual, for local testing)
python -m ingestion.run --repo <repo-id>

# 6. Run dashboard
streamlit run dashboard/app.py
```

## Branching
- `main` — always deployable/demo-ready
- `feature/<short-name>` — one feature per branch
- Commit messages: `[component] short description` e.g. `[ingestion] add PR review event parsing`

## Adding a New Data Source (e.g., GitLab)
1. Write an ADR if the ingestion model differs meaningfully from GitHub
2. Add ingestion module under `ingestion/<provider>/`
3. Map provider's event shape → the existing `commits`/`pull_requests`/`deployments` schema
   (don't create provider-specific tables — normalize at ingestion time)
4. Update `02-data-schema.md` if new columns are needed
5. Add tests with recorded fixture API responses (don't hit live API in tests)

## Adding a New Metric
1. Define it first in `03-metrics-definitions.md`
2. Implement as a pure function in the Metrics Engine: `(events_df) -> metric_value`
3. Add unit tests with synthetic event data covering edge cases (no deploys yet, single event, etc.)
4. Wire into `metrics_daily` aggregation job
5. Add to dashboard + API reference

## Testing Philosophy
- Metrics Engine functions must be pure and unit-testable without a live DB or API
- Use fixture data (small JSON/CSV samples) for ingestion parsing tests
- Integration test: one full pipeline run against a recorded/synthetic repo event set

## Code Review Checklist (self-review, since solo-first)
- [ ] Does this change require a doc update? (schema, API, metric definition)
- [ ] Does this change require an ADR?
- [ ] Are new functions tested?

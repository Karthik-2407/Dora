# Roadmap

Status legend: 🔲 Not started · 🟡 In progress · ✅ Done

## Phase 1 — Foundations
🔲 Lock metric definitions (`03-metrics-definitions.md`)
🔲 Finalize DB schema + first migration
🔲 GitHub API ingestion: commits, PRs, reviews
🔲 CI/CD workflow-run ingestion
🔲 Deployment-event ingestion and confidence classification
🔲 Basic ADRs written (deployment definition, incident definition, tenancy model)
🔲 Synthetic ingestion fixtures and idempotency tests

**Phase 1 risk:** CI/CD ingestion is the highest-risk item and the most likely feature to slip
if schedule becomes constrained. If it slips, use the documented tag/merge deployment degrade path
from ADR-001.

## Phase 2 — Metrics Engine
🔲 Deployment frequency calculation
🔲 Lead time for changes calculation
🔲 MTTR calculation
🔲 Change failure rate calculation
🔲 Deployment/incident linking and deduplication
🔲 Unit tests against known synthetic event sets

## Phase 3 — Dashboard v1
🔲 Streamlit app reading from `metrics_daily`
🔲 Single-repo view: 4 DORA metrics + trend over time
🔲 Data confidence and coverage indicators
🔲 Manual ingestion trigger from UI

## Phase 4 — Benchmarking & Correlation
🔲 Multi-team/multi-repo percentile ranking
🔲 Minimum sample/data-coverage rules
🔲 PR size vs lead time correlation
🔲 Review latency vs change failure rate correlation
🔲 Report correlation coefficient, sample size, and p-value
🔲 Correlation visualizations

**Phase 4 scope rule:** only the two correlations above are required. Additional practice metrics
remain stretch scope.

## Phase 5 — Polish & Presentation
🔲 GitLab support
🔲 Webhook-based real-time ingestion
🔲 Final documentation pass
🔲 Demo dataset + walkthrough for presentation
🔲 Dockerized deployment/demo

## Backlog / Stretch
- [ ] Multi-tenant OAuth for other teams to self-connect
- [ ] Grafana migration from Streamlit
- [ ] Additional engineering-practice metrics
- [ ] More CI/CD providers

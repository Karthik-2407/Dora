# API Reference

> Update this file in the same PR as any route change. Keep examples runnable (copy-paste curl).

## Conventions
- Base URL: `http://localhost:8000` (dev) / `[prod URL]`
- Auth: [token type / header name]
- All timestamps: ISO 8601, UTC
- Errors: `{ "error": string, "detail": string }` with standard HTTP status codes

## Endpoints

### `GET /repos`
List all tracked repos.
```
curl http://localhost:8000/repos
```
Response:
```json
[{ "id": "uuid", "name": "string", "provider": "github", "team_id": "uuid" }]
```

### `POST /repos`
Register a new repo for tracking.
```json
{ "provider": "github", "external_id": "string", "team_id": "uuid" }
```

### `GET /repos/{repo_id}/metrics`
Fetch computed DORA metrics for a repo over a date range.
Query params: `start_date`, `end_date`
```json
{
  "deploy_frequency": 0.0,
  "lead_time_hours": 0.0,
  "mttr_hours": 0.0,
  "change_failure_rate": 0.0
}
```

### `GET /repos/{repo_id}/benchmark`
Fetch percentile rank vs other teams for each metric.

### `GET /repos/{repo_id}/correlations`
Fetch practice-correlation results (PR size vs lead time, review latency vs change-failure rate).

### `POST /ingest/{repo_id}`
Manually trigger an ingestion run (in addition to webhook/scheduled ingestion).

---
_Add new routes above this line as they're implemented. Remove placeholder examples once real
schemas are locked in._

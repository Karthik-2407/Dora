# Data Schema Reference

> Update this doc in the same PR as any migration. Include the migration file name/version next
> to each table once migrations exist.

## Design Principle
Store raw events, not pre-computed metrics. Metrics are derived from raw events so definitions can
change without re-ingesting historical data.

## Core Tables

### `repos`
| column | type | notes |
|---|---|---|
| id | uuid pk | |
| provider | text | `github` \| `gitlab` |
| external_id | text | provider's repo ID |
| name | text | |
| team_id | uuid fk | for benchmarking across teams |

### `commits`
| column | type | notes |
|---|---|---|
| id | uuid pk | |
| repo_id | uuid fk | |
| sha | text | unique per repo |
| author | text | |
| committed_at | timestamptz | |

### `pull_requests`
| column | type | notes |
|---|---|---|
| id | uuid pk | |
| repo_id | uuid fk | |
| external_id | text | provider PR ID |
| opened_at | timestamptz | |
| merged_at | timestamptz | nullable |
| first_review_at | timestamptz | nullable |
| additions | int | primary PR-size input |
| deletions | int | primary PR-size input |
| files_changed | int | secondary descriptive field |

### `workflow_runs`
| column | type | notes |
|---|---|---|
| id | uuid pk | |
| repo_id | uuid fk | |
| external_id | text | provider workflow/run ID |
| workflow_name | text | |
| commit_sha | text | associated commit |
| started_at | timestamptz | |
| completed_at | timestamptz | nullable |
| status | text | queued/running/completed |
| conclusion | text | success/failure/cancelled/etc. |
| environment | text | nullable; production when known |

### `deployments`
| column | type | notes |
|---|---|---|
| id | uuid pk | |
| repo_id | uuid fk | |
| deployed_at | timestamptz | |
| source_type | text | `github_deployment_api` \| `ci_cd` \| `release_tag` \| `merge_to_main` |
| confidence | text | `high` \| `medium` \| `low` |
| environment | text | `production` for DORA deployments |
| commit_sha | text | links back to `commits` |
| pull_request_id | uuid fk | nullable; change delivered by deployment |

### `incidents`
| column | type | notes |
|---|---|---|
| id | uuid pk | |
| repo_id | uuid fk | |
| detected_at | timestamptz | |
| resolved_at | timestamptz | nullable |
| source_type | text | `revert_commit` \| `hotfix_pr` \| `labeled_issue` |
| confidence | text | `high` \| `medium` \| `low` |
| deployment_id | uuid fk | nullable; linked failed deployment |

### `metrics_daily`
| column | type | notes |
|---|---|---|
| repo_id | uuid fk | |
| date | date | |
| deploy_frequency | float | |
| lead_time_hours | float | primary DORA Lead Time |
| mttr_hours | float | nullable; N/A when no incidents |
| change_failure_rate | float | percentage |
| metric_definition_version | text | formula version |
| data_coverage | float | fraction of expected/available source data represented |
| confidence | text | aggregate confidence indicator |

## Versioning Notes
- Track schema changes via Alembic migrations.
- Never silently redefine a column's meaning — add a new column + ADR instead.
- Ingestion must be idempotent using provider/external identifiers.

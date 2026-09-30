# Metrics Definitions

This is the single source of truth for how every metric is computed. If a definition changes,
update it here first, then update the Metrics Engine and record a meaningful change in an ADR.

## The Four DORA Metrics

### 1. Deployment Frequency
**Definition:** count of production deployments per unit time.

**Primary source events:** `deployments.deployed_at`

**Deployment hierarchy:**
1. GitHub Deployment API / explicit production deployment signal
2. Successful CI/CD workflow associated with a production deployment
3. Version/release tag
4. Merge to `main`/`master` as the lowest-confidence fallback

Only production deployments are included in the DORA metric.

### 2. Lead Time for Changes
**Primary DORA definition:** time from a change being merged to the production deployment
containing that change.

**Formula:**
`deployment.deployed_at - pull_request.merged_at`

**Source events:** `pull_requests`, `deployments`

**Secondary diagnostic metric:** first commit associated with the change → production deployment.
This is retained for analysis but is not the primary DORA Lead Time value.

### 3. Mean Time to Restore (MTTR)
**Definition:** average time from an inferred production incident being detected to its resolution.

**Formula:**
`AVG(incidents.resolved_at - incidents.detected_at)`

If no incidents are detected, report `N/A`, not zero.

**Unresolved incidents:** incidents with `resolved_at = NULL` are excluded from MTTR until they are resolved; they remain visible as open incidents.

**Source events:** `incidents`, `deployments`, commits, pull requests, issues.

### 4. Change Failure Rate
**Definition:** percentage of production deployments associated with a detected production
incident requiring remediation.

**Formula:**
`COUNT(failed deployments) / COUNT(all production deployments) * 100`

A deployment is considered failed when it can be linked to an inferred incident using the
incident-linking rules in ADR-002.

## Data Confidence

Every deployment-derived metric must retain enough metadata to explain the quality of its inputs.

Deployment records include:
- `source_type`
- `confidence`
- `environment`

Dashboard views should surface confidence when deployment or incident signals are inferred.

## Custom / Correlation Metrics

### PR Size
**Definition:** `additions + deletions` per merged PR.

**Used for:** PR size vs. Lead Time correlation.

`files_changed` is retained as a secondary descriptive field, not the primary PR-size measure.

### Review Latency
**Definition:** `first_review_at - opened_at`.

**Used for:** review latency vs. Change Failure Rate correlation.

## Phase 4 Research Scope

Only these two practice correlations are required:
1. PR size vs. Lead Time
2. Review latency vs. Change Failure Rate

Each result must report:
- correlation coefficient
- sample size (`n`)
- p-value

The analysis describes association, not causation.

Additional practice metrics are stretch scope.

## Benchmarking
**Definition:** percentile rank of a team's metric value against the configured comparison
population in the same time window.

Benchmarking must not rank a repository/team until the configured minimum sample size and
data-coverage requirements are satisfied.

## Versioning
If any formula above changes, bump `metric_definition_version` and record it in
`metrics_daily` so historical dashboards remain interpretable.

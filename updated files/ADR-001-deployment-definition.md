# ADR-001: How we define a "deployment" event

**Status:** Accepted  
**Date:** 2026-08-21

## Context
Deployment Frequency and Lead Time both depend on knowing when a production deployment occurred.
Student/small-team repositories frequently lack a formal release process, so a single signal
cannot cover the target population.

CI/CD ingestion is part of the Phase 1 MVP because successful production workflow information
provides a stronger deployment signal than repository activity alone. It is also the highest-risk
Phase 1 item because it adds another external event source and schema surface.

## Decision
Use the following deployment-signal hierarchy:

1. Explicit GitHub Deployment API / production deployment event — **high confidence**
2. Successful CI/CD workflow associated with a production deployment — **high confidence**
3. Version/release tag matching the configured release convention — **medium confidence**
4. Merge to `main`/`master` — **low confidence fallback**

Only signals identified as production deployments are included in DORA metrics.

Every deployment stores `source_type`, `confidence`, `environment`, and `commit_sha`.

## Degrade Path
If CI/CD ingestion cannot be completed within the Phase 1 schedule:
- retain GitHub Deployment API support;
- use release tags as the next signal;
- use merge-to-main only as the lowest-confidence fallback.

The system must remain functional under this degraded mode, and the dashboard must disclose
the deployment-source confidence.

## Alternatives Considered
- **Require GitHub Deployment API only:** rejected because many student repos do not use it.
- **Merge to main only:** rejected because merges do not necessarily represent production deployments.
- **Make CI/CD ingestion a later phase:** rejected because deployment confidence is materially weaker
  without it.

## Consequences
- Deployment quality varies by repository.
- The dashboard must expose deployment-source confidence.
- Historical data can contain multiple source types if a repository changes its delivery practice.
- CI/CD ingestion is explicitly identified as the most likely Phase 1 item to slip.

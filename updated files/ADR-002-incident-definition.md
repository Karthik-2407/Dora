# ADR-002: How we define an "incident" (for MTTR and Change Failure Rate)

**Status:** Accepted  
**Date:** 2026-08-21

## Context
MTTR and Change Failure Rate require production-incident information, but target teams may not
use PagerDuty, Opsgenie, or another incident-management system. Incidents therefore have to be
inferred from repository activity.

## Decision
Treat an incident as an inferred production incident when one of these signals is detected:

1. A revert commit matching `^Revert "`; detection time is the revert commit time and resolution is
   the following successful production deployment.
2. A PR labeled `hotfix` or `bugfix` that targets `main` directly; resolution is the associated
   successful production deployment.
3. An issue labeled both `bug` and `production`; detection is issue creation and resolution is
   issue closure when no stronger deployment-linked resolution is available.

Link incidents to deployments using this priority:
1. explicit commit/deployment SHA relationship;
2. revert references to the deployed commit;
3. hotfix PR associated with the deployment;
4. configured time-window matching for production issues.

Deduplicate signals that represent the same incident using deployment/commit identity and time
proximity.

## Alternatives Considered
- **Issues only:** rejected because teams may not consistently label issues.
- **Revert commits only:** rejected because some incidents are fixed forward.
- **External incident-management tooling:** rejected for v1 because it is unavailable for many target teams.

## Consequences
- MTTR and Change Failure Rate are based on inferred incidents, not complete observability.
- No detected incidents must produce `N/A` for MTTR rather than zero.
- The dashboard must visibly state that incident detection is inference-based.
- Multiple source signals require deduplication.

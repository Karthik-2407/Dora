# Project Charter

## Problem Statement
Student and small-team projects have no visibility into their software delivery performance.
Unlike enterprise teams, they lack access to tools like LinearB, Sleuth, or Google's DORA
tooling — so they can't measure or improve deployment frequency, lead time for changes,
mean time to restore (MTTR), or change failure rate.

## Target Users
- [ ] Academic teams working on semester-long or capstone software projects
- [ ] Small (2–8 person) startup/side-project teams using GitHub or GitLab
- [ ] Instructors/TAs who want objective delivery-performance signals across student teams

## Non-Goals (explicitly out of scope)
- Not a CI/CD orchestration tool — it observes existing pipelines, doesn't run them
- Not a project management tool (no ticket assignment, sprint planning, etc.)
- Not aiming for enterprise multi-org scale in v1 — single org / small set of repos first
- Not attempting to infer every real-world production incident; incident detection is repository-activity based

## Success Criteria
- Computes all 4 DORA metrics correctly against synthetic event fixtures covering at least 50 commits and 10 deployments.
- GitHub ingestion captures commits, pull requests, reviews, CI/CD workflow runs, and deployment signals idempotently.
- CI/CD ingestion is implemented in Phase 1; if it slips, the system degrades to tag-based deployment detection, with merge-to-main as the lowest-confidence fallback.
- Dashboard refreshes after a manual ingestion run without requiring a full application restart.
- Correlation analysis for PR size vs. lead time and review latency vs. change-failure rate is validated on at least 2 repositories, with correlation coefficient, sample size, and p-value reported.
- Benchmarking is only shown when the configured minimum sample/data-coverage threshold is satisfied.
- All metric outputs expose metric-definition version and relevant data-confidence/coverage information.

## Stakeholders
- Owner: Karthik
- Reviewers / users for feedback: [names]

## Key Risks / Open Questions
- CI/CD ingestion is the highest-risk Phase 1 item because it adds another external event source and schema surface.
- How do we define "deployment" for repos without formal releases? → see ADR-001
- How do we define "incident" without an incident management tool? → see ADR-002
- Single-tenant vs multi-tenant auth model? → see ADR-003

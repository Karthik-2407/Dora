# ADR-003: Single-tenant vs multi-tenant auth model

**Status:** Accepted  
**Date:** 2026-08-21

## Context
The platform could be built as a personal tool with configured repositories, or as a
multi-tenant service where teams connect their own GitHub organizations through OAuth.
Multi-tenant authentication materially increases security, authorization, and implementation
scope without contributing directly to the core DORA analytics deliverable.

## Decision
Use a **single-tenant model for v1**. Repositories are explicitly configured and authenticated
with a personal access token or equivalent configured credential.

Multi-tenant OAuth is deferred to stretch scope.

## Alternatives Considered
- **Multi-tenant OAuth from day one:** rejected because it adds significant authentication,
  authorization, token-management, and security complexity before the core metrics are proven.
- **No authentication / public repositories only:** rejected because the platform should support
  private academic and small-team repositories.

## Consequences
- Faster path to a working and demoable MVP.
- Repository credentials remain deployment/configuration concerns rather than user-managed OAuth flows.
- Benchmarking across multiple repositories is still supported within the configured tenant.
- OAuth and tenant isolation must be revisited if the project becomes a shared hosted service.

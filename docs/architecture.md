# Atlas architecture baseline

**Status:** Initial proposal; validate against the Garmin proof of concept before treating integration details as settled.

## System boundaries

| Component | Responsibility | Initial technology |
|---|---|---|
| Web app | Dashboard, filters, visualizations, user interactions | Next.js, TypeScript |
| API | Application-facing access, authorization, domain operations | NestJS |
| Ingestion | Source authentication, incremental sync, payload validation and normalization | Python |
| Database | Canonical records, provenance, sync state, relational queries | Supabase PostgreSQL |
| Object storage | Immutable original JSON/FIT payloads where available | Supabase Storage |

## Data flow

1. An adapter reads a bounded time window from a source and records a sync run.
2. The ingestion service validates each response and persists an original copy before transformation where practical.
3. Source-specific identifiers and provenance are retained alongside canonical records.
4. Canonical records are upserted using stable source identity and uniqueness constraints so retries do not create duplicates.
5. The API serves authorized, processed records to the dashboard. The browser never receives privileged database credentials.

## Security and operational principles

- Keep secrets out of Git, logs, browser bundles, and committed fixtures. Use local ignored env files for development and a secret manager in deployed environments.
- Enable Supabase Row Level Security for user data. Server-side service-role access must be narrowly scoped and must never be exposed to the client.
- Preserve provenance: source, external ID, sync time, and raw-object reference should be traceable from a canonical record.
- Make sync incremental, retryable, and idempotent; record failures and checkpoints so partial runs can resume safely.
- Avoid retaining more sensitive raw data than needed; define retention and deletion behavior before broad ingestion.

## Decision record

### D-001 — Start with a modular monorepo

**Decision:** Keep the Next.js app, NestJS API, Python ingestion service, Supabase migrations, and documentation in one repository, with explicit component boundaries.

**Reason:** Atlas is an individual project with coordinated changes across its data contract and services. One repository makes those changes reviewable together while preserving the option to split components later.

### D-002 — Preserve source data and model canonical records

**Decision:** Keep original source payloads where practical and map them into source-agnostic canonical tables with provenance.

**Reason:** This supports reprocessing as the model evolves and avoids coupling the dashboard to one provider's field names.

### D-003 — Prefer official Garmin access, pending verification

**Decision:** Treat Garmin's official Personal API as the preferred primary integration, but do not assume availability or sufficient coverage until access and required fields are verified. Evaluate `python-garminconnect` only for demonstrated gaps; use GarminDB as a reference for historical imports and FIT parsing.

**Reason:** This aligns with the project brief while keeping the integration choice evidence-based. Unofficial access introduces reliability and account-security risks, so any use needs a specific benefit and containment plan.

## Open decisions

- Confirm Garmin developer access, available endpoints, data coverage, refresh limits, and permitted use.
- Determine whether Garmin FIT files or API records supply the required activity detail, and whether a supplementary path adds measurable coverage.
- Verify whether Freeletics exposes an authorized user data export or API.
- Define authentication and the user/tenant model before finalizing RLS policies.
- Decide raw-object retention, deletion, and reprocessing policies.
- Define canonical schemas after the first source coverage audit.

# Project Atlas

Personal fitness intelligence platform for Garmin, Freeletics, and future health data sources. Atlas will preserve source data, normalize it into source-agnostic models, and present performance, training, and recovery insights.

## Repository layout

```text
apps/
  api/                 NestJS API
  web/                 Next.js + TypeScript dashboard
services/
  ingestion/           Python source ingestion and file processing
supabase/
  migrations/          PostgreSQL schema migrations
  seed/                Development-only seed data
docs/
  architecture.md      System design and decision record
```

These directories are initial boundaries; application scaffolding and dependencies will be added as each component is implemented.

## Proposed data flow

1. A source adapter retrieves data (official Garmin API as the preferred route, subject to access and coverage validation).
2. The Python ingestion service validates and stores the original payload or FIT file in Supabase Storage, then records ingestion status and source metadata.
3. Transformations map source records into canonical PostgreSQL tables. Upserts and stable source identifiers make repeated syncs idempotent.
4. The NestJS API exposes authorized application data to the Next.js dashboard.

See [docs/architecture.md](docs/architecture.md) for boundaries, security notes, open questions, and decisions.

## Configuration

Copy the relevant template and fill it with local values:

```bash
cp apps/web/.env.example apps/web/.env.local
cp apps/api/.env.example apps/api/.env
cp services/ingestion/.env.example services/ingestion/.env
```

Never commit real credentials. The web app may contain only public Supabase URL/key values; service-role keys, Garmin credentials, and database secrets belong only in server-side environments.

## Current status

Repository structure and architecture baseline only. Garmin API access and coverage, the need for any unofficial supplementary connector, and Freeletics data access remain to be verified before implementation choices are finalized.

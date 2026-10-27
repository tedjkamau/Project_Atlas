<div align="center">

# 🏃 Project Atlas

### Train with purpose. Understand the data. Get to the start line stronger.

A personal fitness intelligence platform bringing Garmin, Freeletics and future health data into one place.

[![Race day](https://img.shields.io/badge/London_Landmarks_Half-4_Apr_2027-7c3aed?style=for-the-badge&logo=strava&logoColor=white)](https://llhm.co.uk/)
[![Status](https://img.shields.io/badge/status-in_development-f59e0b?style=for-the-badge)](#-project-status)
[![Data](https://img.shields.io/badge/first_source-Garmin-00a3e0?style=for-the-badge&logo=garmin&logoColor=white)](#-data-sources)

**159 days to race day** · 13.1 miles through London · One place to understand the journey

</div>

---

## The idea

Atlas turns personal training data into a clearer picture of progress.

It will preserve the original source data, organise it into consistent models, and help make sense of performance, training and recovery over time. The first milestone is a reliable Garmin data pipeline; the longer-term goal is a useful personal dashboard that connects the dots across workouts and health metrics.

## 🏁 The goal

Build toward the **London Landmarks Half Marathon on 4 April 2027** with a better view of the work behind each run:

- Track running and strength training together.
- Follow changes in aerobic fitness and performance.
- Put recovery, sleep and HRV alongside training load.
- Keep a history of the data, not just the latest snapshot.

## 🔌 Data sources

| Source | Planned role | Current position |
| --- | --- | --- |
| **Garmin Personal API** | Primary source for activities and health data | First proof of concept underway |
| **Freeletics** | Strength and coached workout history | API access to investigate |
| **`python-garminconnect`** | Compare coverage for potential Garmin gaps | Separate fallback experiment |
| **GarminDB** | Reference for FIT parsing and historical archives | Evaluation/reference |

Atlas will only add a supplementary route when it fills a confirmed gap.

## 🧭 How Atlas works

```text
Garmin · Freeletics · future sources
                  ↓
           Source adapters
                  ↓
      Original JSON, FIT and metadata
                  ↓
        Canonical Atlas data model
                  ↓
        NestJS API → Next.js dashboard

Each source keeps its own provenance. Repeated syncs should be safe, and the same activity should not be counted twice when it appears through more than one route.
🏗️ Repository layout
apps/
  api/                 NestJS API
  web/                 Next.js + TypeScript dashboard

services/
  ingestion/           Python source ingestion and file processing

supabase/
  migrations/          PostgreSQL schema migrations
  seed/                Development-only seed data

docs/
  architecture.md      System design, boundaries and decisions

These are the initial project boundaries. Application scaffolding and dependencies will be added as each component is implemented.
🚦 Project status
Current focus: prove the Garmin ingestion path.
- [x] Create the repository and initial project structure
- [x] Document the first architecture baseline
- [x] Confirm the official Garmin Personal API can return activity and daily health records
- [x] Retrieve an activity and its original FIT archive
- [ ] Resolve sleep response validation
- [ ] Compare sleep and other priority metrics with python-garminconnect
- [ ] Inspect and map real payloads into the canonical model
- [ ] Build the first dashboard view
- [ ] Investigate Freeletics workout access
🔄 Data flow
1. A source adapter retrieves data.
2. The Python ingestion service validates the response and stores the original payload or FIT file.
3. Ingestion metadata records the source, retrieval time, outcome and identifiers.
4. Transformations map source records into canonical PostgreSQL tables.
5. Stable source identifiers and upserts make repeated syncs idempotent.
6. The NestJS API serves authorised Atlas data to the Next.js dashboard.
See [`docs/architecture.md`](docs/architecture.md) for system boundaries, security notes, open questions and decisions.
⚙️ Local configuration
Copy the relevant environment templates and add local values:
cp apps/web/.env.example apps/web/.env.local
cp apps/api/.env.example apps/api/.env
cp services/ingestion/.env.example services/ingestion/.env

Keep real credentials out of Git. Public Supabase values may be used by the web app where appropriate; Garmin credentials, service-role keys and database secrets belong only in server-side environments.
🛠️ Tech stack
- Dashboard: Next.js, TypeScript
- API: NestJS
- Ingestion: Python
- Database and file storage: Supabase PostgreSQL and Storage
- Garmin FIT processing: to be selected after the proof of concept


One runner. Many data sources. One clearer view of the road to race day.

```
